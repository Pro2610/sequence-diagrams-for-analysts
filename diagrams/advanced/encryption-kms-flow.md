# Encryption at Rest & In Transit Using KMS

```mermaid
sequenceDiagram
    autonumber
    participant CL as Client
    participant API as Service API (TLS)
    participant KMS as Key Management Service (KMS/HSM)
    participant DB as Encrypted Storage
    participant AUD as Security Audit Log

    CL->>API: HTTPS request (TLS in transit)
    API->>KMS: Request data encryption key (DEK) for tenant X
    KMS-->>API: Return DEK (wrapped or plaintext in memory only)

    API->>API: Encrypt sensitive fields with DEK
    API->>DB: INSERT encrypted blob + metadata (key_id, alg)

    DB-->>API: Later FETCH encrypted row
    API->>KMS: Ask KMS to decrypt / unwrap DEK
    KMS-->>API: DEK (temporary)
    API->>API: Decrypt data in memory
    API-->>CL: Response over TLS

    API->>AUD: Log key usage (who accessed, why)

    Note over API,KMS: App never stores master keys; DEK rotation + access to KMS is role/tenant scoped.
