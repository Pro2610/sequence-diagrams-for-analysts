# Dispute / Chargeback Lifecycle

```mermaid
sequenceDiagram
    autonumber
    participant NW as Card Network
    participant IS as Issuer
    participant ACQ as Acquirer/Processor
    participant GW as Gateway
    participant MER as Merchant (Console)
    participant DOC as Evidence Store

    IS->>NW: Dispute initiated (reason code)
    NW->>ACQ: Notify chargeback
    ACQ->>GW: Notification (t0)
    GW-->>MER: Alert + deadline (t0+X days)

    MER->>DOC: Upload evidence (receipt, logs, 3DS proof)
    DOC-->>MER: Evidence bundle ID
    MER->>GW: Submit representment {bundle_id}
    GW->>ACQ: Forward representment
    ACQ->>NW: Representment → Issuer
    NW->>IS: Review evidence

    alt Issuer accepts representment
        IS-->>NW: Reversal
        NW-->>ACQ: Funds returned
        ACQ-->>GW: Dispute won
        GW-->>MER: Case CLOSED (won)
    else Goes to arbitration/2nd presentment
        IS-->>NW: Uphold chargeback
        NW-->>ACQ: Funds kept
        ACQ-->>GW: Dispute lost
        GW-->>MER: Case CLOSED (lost)
    end
