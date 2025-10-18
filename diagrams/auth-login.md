
# Login & Authentication Flow

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
