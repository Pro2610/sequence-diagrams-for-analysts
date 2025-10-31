```mermaid
sequenceDiagram
    autonumber
    participant KMS as KMS/HSM
    participant SEC as Secrets Manager
    participant SVC as Service (v1)
    participant SVC2 as Service (v2 new pods)
    participant CD as CI/CD

    CD->>KMS: Generate new secret/version
    KMS-->>SEC: Store secret v2
    CD->>SVC2: Deploy with secret v2 (10% traffic)
    SVC2->>SEC: Fetch v2
    Note over SVC2: Health OK → increase traffic
    CD->>SVC2: Roll to 100%
    CD->>SVC: Drain old pods (v1)
    SEC->>SEC: Decommission v1 after TTL
