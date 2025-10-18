
# BI Dashboard Refresh
```mermaid
sequenceDiagram
    participant U as User
    participant BI as BI Server
    participant DWH as Data Warehouse
    U->>BI: Open dashboard
    BI->>DWH: Query data
    DWH-->>BI: Return result
    BI-->>U: Show updated charts
