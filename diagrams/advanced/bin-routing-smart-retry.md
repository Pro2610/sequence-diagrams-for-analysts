```mermaid
sequenceDiagram
    autonumber
    participant FE as Checkout UI
    participant GW as Payment Gateway
    participant BIN as BIN Router
    participant PA as Processor A
    participant PB as Processor B
    participant NW as Card Network

    FE->>GW: Create payment
    GW->>BIN: Route by BIN + rules
    alt Primary A
        BIN-->>GW: Use Processor A
        GW->>PA: AUTH
        alt A declines (do_not_honor / timeout)
            PA-->>GW: Declined / No response
            GW->>BIN: Retry policy?
            BIN-->>GW: Retry to Processor B (allowed codes only)
            GW->>PB: AUTH (same idempotency key)
            PB->>NW: Send auth
            NW-->>PB: Approved
            PB-->>GW: Approved
        else Approved on A
            PA-->>GW: Approved
        end
    else Primary B
        BIN-->>GW: Use Processor B
        GW->>PB: AUTH → NW → Approved
    end
    GW-->>FE: Final result
