```mermaid
sequenceDiagram
    autonumber
    participant CL as Client
    participant AUTH as Auth Server
    participant DB as Token Store
    participant ALR as Alerts

    CL->>AUTH: POST /token {refresh_token RT1}
    AUTH->>DB: Validate RT1 (not used, not revoked)
    DB-->>AUTH: OK
    AUTH-->>CL: access_token AT2 + new refresh RT2
    AUTH->>DB: Mark RT1 as used (rotated), store RT2

    Note over AUTH,DB: If RT1 used again → reuse detected
    CL->>AUTH: POST /token {refresh_token RT1}  %% attacker replay
    AUTH->>DB: RT1 status=used
    alt Reuse detected
        AUTH->>DB: Revoke token family (RT2, RTn)
        AUTH->>ALR: Security alert (user/session)
        AUTH-->>CL: 401 invalid_grant
    end
