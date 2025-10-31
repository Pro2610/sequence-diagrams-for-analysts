```mermaid
sequenceDiagram
    autonumber
    participant DWH as Warehouse (Hot)
    participant ARC as Archive (Cold Storage)
    participant POL as Retention Policy
    participant JOB as Retention Job
    participant AUD as Compliance Log

    POL->>JOB: Rules (table, column, TTL)
    JOB->>DWH: Select eligible partitions
    DWH-->>JOB: Row set / partitions
    alt Archive
        JOB->>ARC: Move to cold (compressed)
        JOB->>DWH: Delete from hot
    else Purge (PII/GDPR)
        JOB->>DWH: Anonymize/Delete keys
    end
    JOB->>AUD: Record retention action
