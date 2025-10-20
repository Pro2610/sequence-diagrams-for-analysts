```mermaid
sequenceDiagram
    autonumber
    participant CL as Client
    participant GW as API Gateway
    participant Q as Message Queue
    participant WK as Worker
    participant SVC as Downstream Service
    participant DLQ as Dead Letter Queue
    participant LOG as Observability

    CL->>GW: POST /jobs {payload}
    GW->>Q: Enqueue message (idempotency-key)
    GW-->>CL: 202 Accepted (job_id)

    loop Consume
        Q->>WK: Deliver message
        WK->>SVC: Call downstream
        alt Success
            SVC-->>WK: 2xx
            WK->>LOG: job_done
        else Transient error
            SVC-->>WK: 5xx/timeout
            WK->>Q: NACK with backoff (retry N)
        else Permanent error/poison
            WK->>DLQ: Move to DLQ
            WK->>LOG: job_failed_dlq
        end
    end
