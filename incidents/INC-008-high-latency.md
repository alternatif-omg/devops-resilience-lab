# INC-008 — High Application Latency



## Summary



Endpoint `/pay` mengalami peningkatan response time karena Payments berjalan dalam kondisi slow mode.



Request tetap berhasil, tetapi latency end-to-end meningkat secara signifikan.



## Impact



- API tetap Running.

- Payments tetap Running.

- Request `/pay` tetap menghasilkan HTTP 201.

- Response time meningkat menjadi sekitar 2 detik.

- Availability tidak terganggu, tetapi performance menurun.



## Detection



Latency diamati melalui response time request end-to-end.



Saat fault aktif:



```text

HTTP 201 | total ≈ 2s

```



Kondisi ini berbeda dengan INC-007 karena request tidak menghasilkan HTTP error.



## Investigation



### 1. Check Application Availability



API dan Payments tetap Running:



```bash

kubectl get pods -n resilience

```



Tidak ditemukan Pod crash atau availability failure.



### 2. Check Request



Request `/pay` tetap berhasil:



```text

HTTP 201

```



Namun response time meningkat menjadi sekitar 2 detik.



### 3. Check Payments Configuration



Fault injection mengaktifkan:



```text

SLOW_MODE=1

```



pada Payments.



Application Payments memiliki mekanisme slow mode yang menambahkan delay pada processing request.



## Root Cause



Environment variable `SLOW_MODE=1` menyebabkan Payments memperlambat pemrosesan request.



Karena API melakukan synchronous request ke Payments, latency Payments diteruskan ke response time API.



Flow:



```text

Client

  |

  v

API

  |

  v

Payments

  |

  +--> ~2 second delay

  |

  v

API response

```



## Resolution



Fault dibersihkan menggunakan:



```bash

NAMESPACE=resilience ./scripts/fault-inject.sh cleanup

```



Kemudian rollout Payments ditunggu hingga selesai.



## Verification



Setelah recovery:



```text

HTTP 201 | total=0.009396s

```



Response time turun dari sekitar 2 detik menjadi sekitar 9 ms.



## Lessons Learned



- Service dapat tetap available tetapi mengalami performance degradation.

- Status Pod Running tidak cukup untuk mengukur kualitas service.

- Dependency latency dapat meningkatkan latency service upstream.

- Response time dan percentile seperti p95 penting selain availability.

- Monitoring sebaiknya mengamati latency, traffic, errors, dan saturation.
