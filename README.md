# Sequence Diagrams for Analysts

A compact, practical collection of **Mermaid sequence diagrams** for common analytics, product, and payments scenarios.

## 📚 Contents
- [Login & Authentication Flow](diagrams/auth-login.md)
- [Payment Processing](diagrams/payment-processing.md)
- [Refund Flow](diagrams/refund-flow.md)
- [ETL Job](diagrams/etl-job.md)
- [BI Dashboard Refresh](diagrams/bi-refresh.md)
- [API Gateway Validation](diagrams/api-gateway-validation.md)

---

### 🧩 Example: Login & Authentication Flow
```mermaid
sequenceDiagram
    participant U as User
    participant FE as Frontend
    participant BE as Backend
    participant IDP as Identity Provider
    U->>FE: Open login page
    FE->>IDP: Redirect for OAuth
    IDP-->>BE: Return token
    BE-->>FE: Session created
    FE-->>U: Access granted
