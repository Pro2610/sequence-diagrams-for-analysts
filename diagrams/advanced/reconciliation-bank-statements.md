```mermaid
sequenceDiagram
    autonumber
    participant BNK as Bank
    participant LGR as Ledger
    participant REP as Reconciliation Service
    participant MER as Merchant Console
    participant OPS as Ops Dashboard
    participant ALR as Alerts

    par Daily export
        BNK->>REP: Send MT940/CSV statement
        REP->>LGR: Fetch payout batches (expected)
    and Matching
        REP->>REP: Match by amount + reference + date
        alt Full match
            REP-->>LGR: Mark payout reconciled
        else Partial / missing
            REP->>LGR: Flag discrepancies
            REP->>OPS: Show “Unreconciled” table
            OPS->>MER: Request bank proof
            MER-->>OPS: Upload receipt
        end
    end

    par Summary
        REP->>ALR: Send daily summary (OK vs mismatched count)
        ALR-->>OPS: Slack notification / email
    end
