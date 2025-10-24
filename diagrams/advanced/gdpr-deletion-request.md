# GDPR Deletion Request (Right to be Forgotten)

```mermaid
sequenceDiagram
    autonumber
    participant USER as User / Data Subject
    participant PORT as Privacy Portal / Support
    participant PRIV as Privacy Service
    participant MAP as Data Map / Registry
    participant SYS as Owning Systems (DB, Logs, Warehouse)
    participant AUD as Compliance Audit Trail

    USER->>PORT: Submit "Delete my data"
    PORT->>PRIV: Create GDPR erase ticket (user_id, identifiers)
    PRIV->>MAP: Lookup all systems storing this user

    MAP-->>PRIV: Systems list (core DB, backups, analytics, cold storage)

    loop Erasure workflow
        PRIV->>SYS: Anonymize / Delete PII for user_id
        SYS-->>PRIV: Status (success / cannot delete)
    end

    PRIV->>AUD: Record what was deleted, by whom, when
    PRIV-->>PORT: Deletion complete / limitations notice to user

    Note over SYS: Some data may be legally retained (tax, AML); it's pseudonymized instead of fully deleted.
