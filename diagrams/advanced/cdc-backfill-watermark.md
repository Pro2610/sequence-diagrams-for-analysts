```mermaid
sequenceDiagram
    autonumber
    participant SRC as OLTP Source
    participant CDC as Change Stream (CDC)
    participant STG as Staging (Raw)
    participant XFM as Transform
    participant DWH as Warehouse
    participant WM as Watermark Store
    participant ALR as Alerts

    par Initial backfill
        SRC->>STG: Dump historical snapshot
        STG->>XFM: Load snapshot batches
        XFM->>DWH: Upsert to fact/dim
        XFM->>WM: Set watermark = max(updated_at)
    and Start CDC
        SRC->>CDC: Emit changes (insert/update/delete)
    end

    loop Stream
        CDC->>STG: Append change batch
        STG->>XFM: Transform & dedupe by PK+ts
        XFM->>DWH: MERGE using watermark
        XFM->>WM: Advance watermark
    end

    alt Gap detected (late data)
        XFM->>ALR: Slack alert with lag metrics
        XFM->>DWH: Targeted backfill for window
    end
