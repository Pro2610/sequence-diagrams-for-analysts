```mermaid
sequenceDiagram
    autonumber
    participant SRC as Provider (Webhook Sender)
    participant H as Your Webhook Endpoint
    participant KV as Replay Store (KV/Redis)
    participant SIG as Signature Verifier
    participant APP as App

    SRC->>H: POST /webhook (body, ts, id, sig)
    H->>SIG: Verify HMAC(sig, secret, body|ts|id)
    alt Invalid signature or stale ts
        SIG-->>H: REJECT
        H-->>SRC: 400/401
    else Valid signature
        H->>KV: SETNX id with TTL 5m
        alt Already exists
            KV-->>H: DUPLICATE
            H-->>SRC: 200 (ignored replay)
        else New
            KV-->>H: OK
            H->>APP: Process event
            APP-->>H: Done
            H-->>SRC: 200
        end
    end
