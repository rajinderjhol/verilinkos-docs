**Figure 4 — The Receipt Lifecycle (Runtime)**

```mermaid
sequenceDiagram
    autonumber
    participant Agent
    participant Guardian as Authorization Layer
    participant Build as Receipt Builder
    participant Anchor as Anchoring Service
    participant Chain as Polygon Amoy
    participant DB as Postgres vap_receipts

    Agent->>Guardian: propose action
    Guardian->>Guardian: evaluate against policy<br/>verdict: allow/block/warn/defer

    Note over Guardian,Build: Pass 1 — compute and sign
    Guardian->>Build: build(action_type, verdict, policy_id)
    Build->>Build: canonical serialize (RFC 8785)
    Build->>Build: SHA-256 leaf hash
    Build->>Build: Ed25519 sign (+ optional ML-DSA-87)
    Build-->>Guardian: VAPReceipt (merkle_root, signature)

    Note over Guardian,Chain: Pass 2 — optional anchor (policy-dependent)
    Guardian->>Anchor: anchor_or_batch(merkle_root)
    alt immediate-anchor policy
        Anchor->>Chain: eth_sendRawTransaction
        Chain-->>Anchor: tx_hash
        Anchor->>Chain: wait_for_transaction_receipt
        Chain-->>Anchor: status=0x1, blockNumber
    else batch policy
        Anchor->>Anchor: queue for hourly batch
    else skip policy
        Anchor->>Anchor: no anchor (receipt still valid)
    end
    Anchor-->>Guardian: VAPAnchor or None

    Note over Guardian,Build: Pass 3 — bind anchor
    Guardian->>Build: rebuild(merkle_root_override, anchors_override)
    Build-->>Guardian: final VAPReceipt

    Guardian->>DB: INSERT vap_receipts
    DB-->>Guardian: stored

    Guardian-->>Agent: ALLOW + receipt_id
    Agent->>Agent: execute action
The eight steps from action proposal to persisted receipt. Passes 1, 2, and 3 are the three build_receipt invocations discussed in Section 8.1. The anchor is optional: if the policy returns batch or skip, the receipt is still valid and still verifiable.
