```mermaid
sequenceDiagram
    autonumber
    participant APP as Order Service
    participant DB as DB (Orders + Outbox)
    participant REL as Relay/Dequeue Worker
    participant BUS as Message Bus

    APP->>DB: Begin TX
    APP->>DB: INSERT order
    APP->>DB: INSERT outbox(event=OrderCreated, payload)
    DB-->>APP: COMMIT
    REL->>DB: Poll outbox (status=NEW)
    DB-->>REL: Rows batch
    REL->>BUS: Publish OrderCreated
    REL->>DB: Mark outbox row SENT
