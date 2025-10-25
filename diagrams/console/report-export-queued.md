```mermaid
sequenceDiagram
    autonumber
    participant U as Console User
    participant UI as Reports UI
    participant RPT as Reports Service
    participant Q as Export Queue / Worker
    participant STR as File Storage
    participant AUD as Audit Log

    U->>UI: Click "Export report" (with filters & date range)
    UI->>RPT: POST /exports {report_id, filters, user_id}
    RPT-->>UI: 202 Accepted (export_job_id, status=IN_QUEUE)

    RPT->>Q: Enqueue job export_job_id
    Q->>RPT: Fetch job details
    Q->>Q: Generate file (CSV / XLSX) using snapshot data
    Q->>STR: Upload file, get signed_url
    STR-->>Q: signed_url + expires_at
    Q->>RPT: PATCH job status=READY url=signed_url
    RPT->>AUD: Log export {user_id, report_id, row_count}

    UI->>RPT: GET /exports/{export_job_id}/status (poll or websocket)
    RPT-->>UI: READY + signed_url
    UI-->>U: "Download report" button enabled
