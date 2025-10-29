```mermaid
sequenceDiagram
    participant Service_A as Service A (Order)
    participant Message_Broker as Message Broker
    participant Service_B as Service B (Notification)
    participant Service_C as Service C (Analytics)

    Service_A->>Message_Broker: 1. Publish "OrderCreated" Event
    activate Service_A
    deactivate Service_A
    Message_Broker-->>Service_B: 2. Deliver Event
    activate Service_B
    Message_Broker-->>Service_C: 3. Deliver Event
    activate Service_C
    Service_B->>Service_B: 4. Send email to client
    Service_B-->>Message_Broker: 5. Acknowledge processing
    deactivate Service_B
    Service_C->>Service_C: 6. Update sales statistics
    Service_C-->>Message_Broker: 7. Acknowledge processing
    deactivate Service_C
