´´´mermaid
sequenceDiagram
    participant Client
    participant API_Gateway as API Gateway
    participant Service_A as Service A (Order)
    participant Service_B as Service B (Inventory)

    Client->>API_Gateway: 1. Request to create order (POST /orders)
    activate API_Gateway
    API_Gateway->>Service_A: 2. Routing: create order
    activate Service_A
    Service_A->>Service_B: 3. Check item availability
    activate Service_B
    Service_B-->>Service_A: 4. Item is available
    deactivate Service_B
    Service_A->>Service_A: 5. Save order to DB
    Service_A-->>API_Gateway: 6. Order successfully created
    deactivate Service_A
    API_Gateway-->>Client: 7. HTTP 201 Created (Response)
    deactivate API_Gateway
