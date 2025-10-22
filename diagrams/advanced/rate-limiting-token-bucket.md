```mermaid
sequenceDiagram
    autonumber
    participant CL as Client
    participant GW as API Gateway
    participant BU as Token Bucket (per key)
    participant SVC as Service
    participant LOG as Metrics/Alerts

    CL->>GW: Send request
    GW->>BU: Check tokens(client_id)
    alt Has tokens
        BU-->>GW: ok (decrement 1)
        GW->>SVC: Forward request
        SVC-->>GW: 200 OK
        GW->>LOG: record latency
        GW-->>CL: response
    else No tokens
        BU-->>GW: exhausted
        GW-->>CL: 429 Too Many Requests
        GW->>CL: Header Retry-After: <seconds>
        GW->>LOG: rate_limited=true
    end

    loop refill interval
        BU->>BU: Add tokens up to capacity
    end
