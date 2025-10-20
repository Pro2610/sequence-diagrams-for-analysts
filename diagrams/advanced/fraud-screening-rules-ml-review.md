```mermaid
sequenceDiagram
    autonumber
    participant CU as Customer
    participant FE as Checkout UI
    participant GW as Payment Gateway
    participant FR as Fraud Engine (Rules+ML)
    participant MR as Manual Review
    participant PR as Processor

    CU->>FE: Submit payment
    FE->>GW: POST /payments
    GW->>FR: Risk evaluate (features)
    alt Hard rule hit (deny list/velocity)
        FR-->>GW: DECISION=DECLINE (reason)
        GW-->>FE: Declined (fraud)
    else Soft risk (score >= review_threshold)
        FR-->>GW: DECISION=REVIEW
        GW-->>FE: Payment PENDING_REVIEW
        GW->>MR: Create case (evidence, score)
        MR-->>GW: APPROVE or DECLINE
        alt Approved by MR
            GW->>PR: Submit AUTH/CAPTURE
            PR-->>GW: Result
            GW-->>FE: Success
        else Declined by MR
            GW-->>FE: Declined (manual)
        end
    else Low risk
        FR-->>GW: DECISION=APPROVE
        GW->>PR: AUTH/CAPTURE
        PR-->>GW: Result
        GW-->>FE: Success
    end
