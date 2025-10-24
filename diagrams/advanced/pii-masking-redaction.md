# PII Masking / Redaction Before Storage

```mermaid
sequenceDiagram
    autonumber
    participant FE as Frontend / Form
    participant API as Ingestion API
    participant PII as PII Sanitizer / Redactor
    participant DB as Application DB
    participant LOG as Audit Log (Restricted)

    FE->>API: POST /customers {name, email, phone, card_pan, address}
    API->>PII: Sanitize payload

    alt Sensitive field
        PII->>PII: Hash / tokenize / mask (****1234)
    else Non-sensitive field
        PII->>PII: Keep as-is
    end

    PII-->>API: Clean payload {name, email_hash, phone_hash, last4, country,...}
    API->>DB: INSERT cleaned data (no raw PAN, no full address if not needed)
    API->>LOG: Append audit (who created, when, IP)

    Note over DB,LOG: Access controlled by role (need-to-know policy)
