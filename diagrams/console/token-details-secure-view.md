```mermaid
sequenceDiagram
    autonumber
    participant AG as Agent / Merchant User
    participant UI as Token Details UI
    participant TOK as Token Service
    participant POL as Permission Service
    participant VAULT as Secure Vault (PAN vault)
    participant AUD as Audit Log

    AG->>UI: Open "Token Details"
    UI->>TOK: GET /tokens/{token_id}
    TOK-->>UI: token metadata {last4, card_brand, holder_name, created_at} (no PAN)

    UI-->>AG: Show masked info ****1234, last txn date, status

    AG->>UI: Click "View full card"
    UI->>POL: canViewFullPAN(user_role, merchant_id)?
    alt Not allowed
        POL-->>UI: DENY
        UI-->>AG: Access denied (sensitive data)
    else Allowed + reason required
        POL-->>UI: ALLOW_WITH_AUDIT
        UI->>TOK: GET /tokens/{id}/reveal?actor=AG
        TOK->>VAULT: Fetch & decrypt PAN
        VAULT-->>TOK: PAN 4111 1111 ...
        TOK->>AUD: Log PAN_access {actor, token_id, timestamp}
        TOK-->>UI: Full PAN (temporary)
        UI-->>AG: Show in secure modal (not copyable)
    end
