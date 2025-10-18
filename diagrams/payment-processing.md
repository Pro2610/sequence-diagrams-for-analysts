```markdown
# Payment Processing Flow
```mermaid
sequenceDiagram
    participant C as Customer
    participant GW as Payment Gateway
    participant PR as Processor
    participant BN as Bank
    C->>GW: Make payment
    GW->>PR: Send authorization
    PR->>BN: Request funds
    BN-->>PR: Approve
    PR-->>GW: Confirm
    GW-->>C: Payment successful
