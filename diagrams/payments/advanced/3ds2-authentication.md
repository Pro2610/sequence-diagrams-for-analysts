# 3-D Secure 2.x Authentication (Frictionless vs Challenge)

```mermaid
sequenceDiagram
    autonumber
    participant C as Customer
    participant FE as Checkout UI
    participant GW as Payment Gateway
    participant DS as Directory Server (Scheme)
    participant ACS as Issuer ACS
    participant PR as Processor

    C->>FE: Enter card + amount
    FE->>GW: Init payment (3DS enabled)
    GW->>DS: 3DS Method + AReq
    alt Frictionless
        DS->>ACS: Route AReq
        ACS-->>GW: ARes (frictionless, liab.shift=YES)
        GW->>PR: Submit AUTH with 3DS data
        PR-->>GW: Approved
        GW-->>FE: Success (no challenge)
    else Challenge Flow
        DS->>ACS: Route AReq
        ACS-->>GW: ARes (challenge required)
        GW-->>FE: Render challenge iframe
        C->>ACS: Complete challenge (OTP/Biometrics)
        ACS-->>GW: CRes (authenticated)
        GW->>PR: AUTH with 3DS result
        PR-->>GW: Approved/Declined
        GW-->>FE: Result + liability info
    end
