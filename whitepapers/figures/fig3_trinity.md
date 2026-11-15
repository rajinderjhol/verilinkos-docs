**Figure 3 — The Evidence Trinity**

```mermaid
flowchart TB
    subgraph Authority["Proof of Authority"]
        A1[Delegation token<br/>scoped, monotonic, revocable]
        A2[Public revocation registry<br/>sub-5s propagation]
        A1 --> A2
    end
    subgraph Perception["Proof of Perception"]
        P1[Visual state witness<br/>screenshot hash + DOM hash<br/>+ viewport + timestamp]
    end
    subgraph Action["Proof of Action"]
        Ac1[Signed VAP receipt<br/>Merkle root + Ed25519]
        Ac2[Optional anchor<br/>Polygon Amoy / Mainnet]
        Ac3[Optional witness<br/>peer attestation with TTL]
        Ac1 --> Ac2 --> Ac3
    end

    A1 -.-> Q1[Question 1: Regulator<br/>should it have acted?]
    P1 -.-> Q2[Question 2: Plaintiff<br/>was it deceived?]
    Ac1 -.-> Q3[Question 3: Insurer<br/>is the claim real?]

    style Authority fill:#e6f3ff
    style Perception fill:#fff0e6
    style Action fill:#e6ffe6
    style Q1 fill:#f0f0f0
    style Q2 fill:#f0f0f0
    style Q3 fill:#f0f0f0
```

*The three artifacts map one-to-one onto the three underwriter questions from Section 3. Authority tells the regulator whether the agent should have acted; perception tells the plaintiff whether the agent was deceived; action tells the insurer whether the claim is real.*
