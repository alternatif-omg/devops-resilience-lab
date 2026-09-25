INC-007 — API High Error Rate

Symptom:

- HighErrorRate masuk PENDING
- API /pay menghasilkan HTTP 503
- API /healthz tetap HTTP 200

Investigation:

- Prometheus menunjukkan peningkatan status 5xx
- Loki menunjukkan POST /pay → 503
- API tetap healthy
- API bergantung pada Payments
- Payments /process menghasilkan HTTP 500
- Ditemukan FAIL_MODE=1 pada Payments

Root Cause:
Payments berjalan dengan fault injection FAIL_MODE=1 sehingga
/process menghasilkan HTTP 500. API menerjemahkan kegagalan
dependency tersebut menjadi HTTP 503.

Recovery:
NAMESPACE=resilience ./scripts/fault-inject.sh cleanup

Verification:
POST /pay → HTTP 201
