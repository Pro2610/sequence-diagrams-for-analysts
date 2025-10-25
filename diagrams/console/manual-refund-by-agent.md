```mermaid
sequenceDiagram
    autonumber
    participant AG as Agent (Support / Finance)
    participant UI as Admin Console
    participant PAY as Payment Service
    participant GW as Gateway / Processor
    participant AUD as Audit Log

    AG->>UI: Open Payment Details
    UI-->>AG: Show "Refund" button (if CAPTURED && not already refunded)

    AG->>UI: Click Refund (enter amount, reason)
    UI->>PAY: POST /refunds {payment_id, amount, reason, actor_id}
    PAY->>GW: Create refund request
    GW-->>PAY: refund_id, status=PENDING
    PAY-->>UI: 202 Accepted (refund pending)
    UI-->>AG: Show status "Refund in progress"

    GW-->>PAY: async callback status=SUCCEEDED
    PAY->>AUD: Log refund (amount, actor_id, reason)
    PAY-->>UI: Update to REFUNDED
    UI-->>AG: Mark order as REFUNDED / PARTIALLY REFUNDED
