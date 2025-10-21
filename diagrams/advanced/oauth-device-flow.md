```mermaid
sequenceDiagram
    autonumber
    participant TV as Device (no keyboard)
    participant APP as Companion App/Browser
    participant IDP as Authorization Server
    participant API as Resource Server

    TV->>IDP: POST /device_authorization
    IDP-->>TV: device_code, user_code, verification_uri
    TV-->>APP: Show code + URL to user

    User->>APP: Open URL, enter user_code
    APP->>IDP: /authorize (login + consent)
    IDP-->>APP: Success page

    loop Poll
        TV->>IDP: POST /token (device_code)
        alt Not authorized yet
            IDP-->>TV: authorization_pending
        else Approved
            IDP-->>TV: access_token (+optional refresh)
            break
        end
    end
    TV->>API: Call protected endpoints (Bearer)
    API-->>TV: Data
