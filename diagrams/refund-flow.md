
# Refund Flow
```mermaid
sequenceDiagram
    participant CS as Customer Service
    participant GW as Payment Gateway
    participant PR as Processor
    participant BN as Bank
    CS->>GW: Initiate refund
    GW->>PR: Send refund
    PR->>BN: Process credit
    BN-->>PR: Confirm
    PR-->>GW: Completed
    GW-->>CS: Refund succeeded
