```mermaid
sequenceDiagram
    autonumber
    participant APP as Merchant App
    participant NT as Network Token Service (Scheme)
    participant WAL as Wallet/Issuer Tokenization
    participant GW as Gateway
    participant PR as Processor

    APP->>NT: Request network token (PAN, device)
    NT->>WAL: Provision token with issuer
    WAL-->>NT: token_id + metadata
    NT-->>APP: network_token + cryptogram params

    APP->>GW: Charge {network_token, cryptogram}
    GW->>PR: AUTH with token + crypto
    PR-->>GW: Approved/Declined
    GW-->>APP: Result

    Note over NT: Token lifecycle events: rotate, suspend, revoke
    NT->>APP: Notify rotate token (lifecycle hook)
    APP->>APP: Update stored token reference
