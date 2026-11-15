**Figure 6 — The Anchoring Decision Tree**

```mermaid
flowchart TD
    A[Action occurs] --> B[build_receipt]
    B --> C{AnchorPolicy.decide}
    C -->|critical| D[IMMEDIATE ANCHOR<br/>~0.0007 MATIC per action]
    C -->|routine| E[BATCH QUEUE<br/>~0.0007 MATIC per 100 actions]
    C -->|noise| F[SKIP ANCHOR<br/>~0 MATIC]
    D --> G[Persist with anchor]
    E --> H[Hourly batch: build Merkle tree<br/>anchor root once]
    H --> I[Update N receipts:<br/>merkle_path + batch anchor]
    I --> G2[Persist]
    F --> K[Persist without anchor<br/>still valid, still verifiable]

    style D fill:#ffe6e6,stroke:#c00
    style E fill:#e6f3ff
    style F fill:#f0f0f0
```

*Critical actions (financial transfers, wire transfers, contract creation, payment settlement, risk tier 3+, BLOCK, HITL, high novelty, cross-org, disputed, claimed) anchor immediately. Routine actions (reads, monitoring, memory writes, allow verdicts at tier 1-2) enter a batch queue. Noise actions (retries, health checks, internal loops) skip anchoring — the receipt is still valid, still verifiable offline. For a 10,000-action/day agent, this reduces daily anchoring cost from ~$4/day to ~$0.01/day while critical actions still anchor in real time.*
