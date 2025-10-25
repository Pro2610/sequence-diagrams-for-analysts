
```mermaid
sequenceDiagram
    autonumber
    participant U as Console User (Merchant / Support)
    participant UI as Merchant Console UI
    participant TERM as Terminal Service
    participant POL as Policy/Permissions
    participant AUD as Audit Log

    U->>UI: Open Terminal Details (status = Fulfillment)
    UI->>POL: Check canActivate(user_role, terminal_id)
    alt Not allowed
        POL-->>UI: DENY
        UI-->>U: "Activate" button disabled
    else Allowed
        POL-->>UI: ALLOW
        UI-->>U: Show [Activate Terminal] action
        U->>UI: Click Activate Terminal
        UI->>TERM: PATCH /terminals/{id} status=ACTIVE
        TERM-->>UI: 200 OK (status=ACTIVE, last_update_ts)
        UI-->>U: Status badge changes to ACTIVE (green)
        UI->>AUD: Log "terminal_activated", actor, timestamp, previous_status
    end
