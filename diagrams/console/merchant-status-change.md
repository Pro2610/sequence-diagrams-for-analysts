```mermaid
sequenceDiagram
    autonumber
    participant ADM as Admin User
    participant UI as Merchant Console UI
    participant POL as Access Policy
    participant MER as Merchant Service
    participant RISK as Risk & Compliance
    participant AUD as Audit Log

    ADM->>UI: Open Merchant Profile
    UI->>POL: canChangeStatus(adm_role, merchant_id)
    alt No permission
        POL-->>UI: DENY
        UI-->>ADM: Status field is read-only
    else Allowed
        POL-->>UI: ALLOW
        UI-->>ADM: Show dropdown [ACTIVE / SUSPENDED / CLOSED]
        ADM->>UI: Select SUSPENDED + reason "Risk alert"
        UI->>RISK: Validate allowed_transition(from=ACTIVE, to=SUSPENDED)
        alt Transition allowed
            RISK-->>UI: OK
            UI->>MER: PATCH /merchants/{id} status=SUSPENDED reason="Risk alert"
            MER-->>UI: 200 OK (status=SUSPENDED)
            UI->>AUD: Log merchant_status_change {old, new, actor, reason}
            UI-->>ADM: Badge now SUSPENDED (yellow)
        else Transition blocked
            RISK-->>UI: REJECT (needs compliance approval)
            UI-->>ADM: "Manual escalation required"
        end
    end
