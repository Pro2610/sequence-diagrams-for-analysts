# Sequence Diagrams for Analysts

A compact, practical collection of **Mermaid sequence diagrams** for common analytics, product, and payments scenarios.

## 📚 Contents
1. Login & Authentication Flow
2. Payment Processing (Auth → Capture)
3. Refund Flow
4. ETL Job (Extract → Transform → Load)
5. BI Dashboard Data Refresh
6. API Gateway Validation

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
