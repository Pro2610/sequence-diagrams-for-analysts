```mermaid
sequenceDiagram
    autonumber
    participant SCH as Prefect Scheduler
    participant FL as Flow (ETL)
    participant EX as Extract Task
    participant TR as Transform Task
    participant LD as Load Task
    participant C as Cache (Results)
    participant ALR as Alerts (Slack/Email)

    SCH->>FL: Trigger flow on schedule (cron)
    FL->>EX: Run extract (source delta)
    alt Cache hit
        C-->>EX: Use cached result
    else No cache
        EX-->>C: Store result with TTL
    end

    EX-->>TR: Dataset (parquet/csv)
    TR->>TR: Validate (row_count, null%, schema)
    alt Validation fail
        TR-->>FL: raise Exception
        loop Retries (max 3, exp backoff)
            FL->>TR: Retry transform
        end
        FL->>ALR: Page on-call (validation failed)
        FL-->>SCH: SLA BREACHED
    else OK
        TR-->>LD: Clean dataset
        LD->>LD: Upsert/MERGE to DWH
        LD-->>FL: Load metrics (rows, duration)
        FL->>ALR: Success summary + SLA ok
    end
