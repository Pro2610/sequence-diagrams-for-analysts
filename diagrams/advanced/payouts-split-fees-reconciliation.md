```mermaid
sequenceDiagram
    autonumber
    participant ORD as Order Service
    participant GW as Payment Gateway
    participant SPL as Splitter (Fees/Partners)
    participant LGR as Ledger
    participant BNK as Banking/Payouts
    participant REP as Reconciliation

    ORD->>GW: Create payment
    GW->>SPL: Compute splits (merchant, platform fee, partner)
    SPL-->>GW: Split map
    GW->>LGR: Post entries DR/CR (escrow)
    Note over LGR: Balance by entity & currency

    par Settlement window
        LGR->>BNK: Generate payout batch
        BNK-->>LGR: Transfer confirmation
    and Reports
        LGR->>REP: Export payout & fees report
        REP-->>ORD: Statement (CSV)
    end

    alt Mismatch on bank file
        REP->>LGR: Flag discrepancy
        LGR-->>BNK: Investigate/adjustment
    end
