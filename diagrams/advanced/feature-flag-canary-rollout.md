```mermaid
sequenceDiagram
    autonumber
    participant PM as PM toggles flag
    participant FF as Feature Flag Service
    participant APP as App Servers
    participant MET as Metrics/Errors
    participant ALR as Alerts

    PM->>FF: Enable feature X for 5%
    APP->>FF: Poll config (ETag)
    FF-->>APP: flag X=5%
    APP->>MET: Emit errors/latency by cohort
    alt KPIs healthy
        PM->>FF: Increase to 25% → 50% → 100%
    else KPIs degrade
        FF-->>APP: Roll back to 0%
        APP->>ALR: Incident created
    end
