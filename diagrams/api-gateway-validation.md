
# API Gateway Validation
```mermaid
sequenceDiagram
    participant CL as Client
    participant GW as API Gateway
    participant SV as Service
    CL->>GW: Send request
    GW->>GW: Validate schema + token
    GW->>SV: Forward if valid
    SV-->>GW: Response
    GW-->>CL: Return status
