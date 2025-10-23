```mermaid
sequenceDiagram
    autonumber
    participant JOB as ETL/Stream Jobs
    participant EXP as Exporters/SDK
    participant PR as Prometheus
    participant RL as Alertmanager
    participant GR as Grafana
    participant ONC as On-Call (Slack/Pager)

    JOB->>EXP: Expose metrics (duration, rows, lag, errors)
    EXP->>PR: /metrics scraped on interval
    PR->>RL: Fire alert if thresholds breached
    RL-->>ONC: Notify (Slack/PagerDuty)
    GR->>PR: Query for dashboards (SLO/SLA)
    ONC->>JOB: Mitigate/rollback
    JOB-->>EXP: Metrics recover → alert resolves
    RL-->>ONC: Auto-resolved notification
