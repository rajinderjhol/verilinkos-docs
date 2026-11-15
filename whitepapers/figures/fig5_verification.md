**Figure 5 — The Verification Flow (Third-Party, Offline)**

```mermaid
sequenceDiagram
    autonumber
    participant Recipient as Carrier / Court / Counterparty
    participant Verifier as Offline Verifier
    participant Anchor as Trust Anchor
    participant Chain as Polygon Amoy (optional)

    Recipient->>Verifier: receipt.json
    Verifier->>Anchor: fetch trust-anchor.json (cached)
    Anchor-->>Verifier: public keys + revocations

    Note over Verifier: Offline checks (no network required)
    Verifier->>Verifier: 1. signer_pubkey in registry?
    Verifier->>Verifier: 2. signature valid over merkle_root?
    Verifier->>Verifier: 3. merkle_path to merkle_root matches?
    Verifier->>Verifier: 4. delegation chain monotonic?
    Verifier->>Verifier: 5. perception witness hash matches?

    Note over Verifier,Chain: Optional on-chain check
    alt anchors non-empty AND confirmed
        Verifier->>Chain: eth_getTransactionReceipt(tx_hash)
        Chain-->>Verifier: status=0x1, blockNumber
        Verifier->>Verifier: 6. block matches anchor.block?
    else FALLBACK or Simulated
        Verifier->>Verifier: 6. anchor skipped (honest marker)
    end

    Verifier-->>Recipient: AUTHENTIC / AUTHENTIC+FALLBACK / AUTHENTIC+SIMULATED
```

*Checks 1 through 5 run offline from the receipt bytes and one cached trust anchor. The chain lookup (check 6) is optional — it adds "this record existed by block N" but is not required for authenticity. This is the architectural difference from chain-required evidence systems.*
