# Login & Authentication Flow

**Business Context:** Users authenticate to access a merchant console / analytics app.  
**Purpose:** Show boundaries between client, backend, identity provider (IdP), and session issuance.

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
