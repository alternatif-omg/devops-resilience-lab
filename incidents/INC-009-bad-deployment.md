# INC-009 — Bad Deployment and Rollback



## Summary



Deployment API baru menggunakan image tag yang tidak tersedia sehingga Pod revision baru gagal dijalankan.



Kubernetes mempertahankan Pod revision sebelumnya sehingga API tetap tersedia selama deployment gagal.



## Impact



- Pod revision baru gagal start.

- Status Pod baru menjadi `ErrImagePull`.

- Revision API sebelumnya tetap Running.

- Service tidak mengalami total outage.



## Detection



Setelah deployment image baru dilakukan:



```bash

kubectl get pods -n resilience

```



menunjukkan:



```text

resilience-lab-api-7b65f8bc57-*   0/1   ErrImagePull

```



Sementara revision sebelumnya:



```text

resilience-lab-api-6d9c5977f9-*   1/1   Running

```



## Investigation



### 1. Check Pod Status



```bash

kubectl get pods -n resilience

```



Revision baru menunjukkan:



```text

ErrImagePull

```



### 2. Check Deployment



```bash

kubectl describe deployment resilience-lab-api -n resilience

```



Deployment mencoba membuat Pod menggunakan revision baru.



### 3. Check Kubernetes Events



```bash

kubectl get events -n resilience \

  --sort-by='.lastTimestamp'

```



Events digunakan untuk mengidentifikasi kegagalan pengambilan container image.



### 4. Check Image Configuration



Bad deployment menggunakan image:



```text

ghcr.io/lotoos0/resilience-lab-api:inc009-broken

```



Tag tersebut tidak tersedia sehingga Kubernetes tidak dapat menarik image.



## Root Cause



Deployment API menggunakan container image tag yang tidak tersedia pada registry.



Akibatnya kubelet gagal melakukan image pull dan Pod revision baru masuk kondisi:



```text

ErrImagePull

```



atau dapat berkembang menjadi:



```text

ImagePullBackOff

```



## Resolution



Deployment dikembalikan ke revision sebelumnya:



```bash

kubectl rollout undo deployment/resilience-lab-api \

  -n resilience

```



Status rollout kemudian diperiksa:



```bash

kubectl rollout status deployment/resilience-lab-api \

  -n resilience

```



## Verification



Setelah rollback:



```text

resilience-lab-api   2/2   Ready

```



Pod API kembali Running dan Pod dengan bad image tidak lagi digunakan.



## Lessons Learned



- Deployment baru perlu diverifikasi setelah rollout.

- `ErrImagePull` dan `ImagePullBackOff` sering berkaitan dengan image name, tag, registry, atau authentication.

- Kubernetes RollingUpdate dapat mempertahankan revision sehat selama revision baru belum Ready.

- Rollback adalah recovery cepat ketika revision sebelumnya diketahui sehat.

- `kubectl get`, `describe`, `events`, dan `rollout` merupakan tool penting saat troubleshooting deployment.
