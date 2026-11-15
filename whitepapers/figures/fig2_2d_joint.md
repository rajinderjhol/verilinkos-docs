**Figure 2 — The Receipt as the Joint of Two Orthogonal Threads**

```mermaid
flowchart LR
    subgraph Evidentiary["Evidentiary Thread"]
        E1[Identity] --> E2[Authority]
        E2 --> E3[Perception]
        E3 --> E4[Decision]
        E4 --> E5[Action]
        E5 --> E6[Attestation]
    end

    subgraph Joint["THE RECEIPT"]
        R[One canonical artifact<br/>binds evidentiary + economic<br/>verifiable offline]
    end

    subgraph Economic["Economic Thread"]
        C1[Negotiation] --> C2[Authorization]
        C2 --> C3[Commitment]
        C3 --> C4[Performance]
        C4 --> C5[Settlement]
        C5 --> C6[Reversal]
    end

    E6 --> R
    C6 --> R

    style Joint fill:#fffbe6,stroke:#c90,stroke-width:3px
    style Evidentiary fill:#e6f3ff
    style Economic fill:#e6ffe6
```

*The receipt is not the record of action; it is the record of action AND its economic consequence, bound into one artifact. The evidentiary thread answers "what happened?"; the economic thread answers "what did it commit?"; the receipt is the unique 2D point where both threads cross.*
