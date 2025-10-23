```mermaid
sequenceDiagram
    autonumber
    participant SRC as Source Systems
    participant S3 as Data Lake (S3)
    participant AF as Airflow DAG
    participant BZ as Bronze (Raw Delta)
    participant SV as Silver (Cleaned Delta)
    participant GD as Gold (Serving Delta)
    participant BI as BI/SQL (Spark/Trino)

    SRC->>S3: Land raw files (json/csv/parquet)
    AF->>BZ: Ingest to Bronze (append-only)
    AF->>SV: Clean + dedupe + schema evolve
    SV->>SV: OPTIMIZE + ZORDER (optional)
    AF->>GD: Model facts/dims for analytics
    BI->>GD: Query gold tables
