```mermaid
sequenceDiagram
    autonumber
    participant CL as Client
    participant API as API Gateway/Service
    participant SVC as Downstream Service
    participant C as Cache

    CL->>API: GET /recommendations
    API->>API: Check breaker state
    alt Closed (normal)
        API->>SVC: Request
        alt Success
            SVC-->>API: 200 data
            API->>C: Update cache
            API-->>CL: 200 data
        else Failure (timeouts/5xx)
            SVC-->>API: Error
            API->>API: Increment failure count
            alt Threshold reached
                API->>API: Open breaker
            end
            API->>C: Try stale cache
            C-->>API: cached data?
            API-->>CL: 200 (stale) / 503 if none
        end
    else Open (tripped)
        API->>C: Serve cache immediately
        C-->>API: cached data?
        API-->>CL: 200 (stale) / 503 if none
    end
