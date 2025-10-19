# ETL with Data Quality Gates, Quarantine & Rollback

```mermaid
sequenceDiagram
    autonumber
    participant SCH as Scheduler
    participant EX as Extractor
    participant STG as Staging (Raw)
    participant QC as Data Quality Checks
    participant TR as Transform (DBT)
    participant DWH as Warehouse
    participant QT as Quarantine
    participant ALR as Alerts

    SCH->>EX: Run extract job
    EX->>STG: Write batch (t partition)
    SCH->>QC: Run checks (row_count, null%, dupes, schema)
    alt Checks pass
        QC-->>SCH: OK
        SCH->>TR: Run models (incremental)
        TR->>DWH: Load fact_* / dim_*
        DWH-->>SCH: Load OK
        SCH->>ALR: Success with metrics
    else Checks fail
        QC-->>SCH: FAIL {rule, sample}
        SCH->>QT: Move bad rows to quarantine
        SCH->>DWH: DO NOT LOAD / ROLLBACK last partition
        SCH->>ALR: Page on-call with details & sample ids
    end
