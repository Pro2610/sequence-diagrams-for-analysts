```markdown
# ETL Job (Extract → Transform → Load)
```mermaid
sequenceDiagram
    participant SCH as Scheduler
    participant SRC as Source DB
    participant DWH as Data Warehouse
    participant BI as BI Tool
    SCH->>SRC: Extract data
    SCH->>DWH: Transform + Load
    SCH->>BI: Trigger dashboard refresh
