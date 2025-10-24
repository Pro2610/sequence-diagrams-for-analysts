# Card Tokenization & Safe Detokenization

```mermaid
sequenceDiagram
    autonumber
    participant FE as Checkout UI
    participant TOK as Tokenization Service (PCI scope)
    participant VAULT as Secure Card Vault
    participant APP as Merchant App / Order System
    participant GATE as Payment Gateway

    FE->>TOK: Send raw PAN + expiry (over TLS)
    TOK->>VAULT: Store PAN encrypted (HSM/KMS)
    VAULT-->>TOK: vault_id + metadata (last4, brand, bin)
    TOK-->>FE: Return token (opaque_token_abc123)

    FE->>APP: Create Order {amount, token=opaque_token_abc123}
    APP->>GATE: Charge using token (no raw PAN)
    GATE->>TOK: Detokenize? (need PAN for auth request)
    TOK->>VAULT: Fetch & decrypt PAN (secured)
    VAULT-->>TOK: PAN + expiry
    TOK-->>GATE: PAN (ephemeral, in memory)
    GATE->>GATE: Send auth to processor / network
    GATE-->>APP: Approved / Declined (no PAN leaked back)

    Note over APP,TOK: Merchant app never sees raw card data, only the token. PCI scope reduced.
