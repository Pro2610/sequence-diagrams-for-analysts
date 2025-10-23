```mermaid
sequenceDiagram
    autonumber
    participant PRD as Producer (Idempotent)
    participant SR as Schema Registry
    participant K as Kafka (Topic)
    participant C as Consumer (Transactional)
    participant SVC as Downstream Store
    participant DLQ as Dead Letter Queue
    participant MON as Monitoring

    PRD->>SR: Get schema ID
    PRD->>K: Produce messages (idempotent, keys)
    K-->>C: Deliver batch
    C->>C: Begin transaction
    C->>SVC: Upsert using key (idempotent sink)
    alt Processing error
        C->>DLQ: Write bad record + reason
        C->>C: Abort transaction
        MON->>MON: Increment dlq_count
    else OK
        C->>C: Commit transaction
        MON->>MON: Lag/throughput updated
    end
