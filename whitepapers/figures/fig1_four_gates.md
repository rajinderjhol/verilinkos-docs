**Figure 1 — The Four Gates**

```mermaid
flowchart TB
    subgraph G1["1. Verification — can you prove what it DID?"]
        V[Signed, replayable receipt<br/>SCITT / RFC 9162 / COSE_Sign1]
    end
    subgraph G2["2. Governance — who was on WATCH?"]
        Go[Named accountable human<br/>EU AI Act Art 26 / ISO 42001]
    end
    subgraph G3["3. Standards — against what YARDSTICK?"]
        S[ISO 42001 mapping<br/>NIST AI RMF / ARAS covariates]
    end
    subgraph G4["4. Admissibility — what did it SEE?"]
        A[Visual + semantic state witness<br/>FRE 902(14) / EU AI Act Art 12]
    end

    G1 --> G2 --> G3 --> G4

    style G4 fill:#ffe6e6,stroke:#c00,stroke-width:3px
    style G1 fill:#e6f3ff
    style G2 fill:#e6ffe6
    style G3 fill:#fffbe6
The four gates are cumulative. Verification without governance is incomplete; governance without standards is incomparable; standards without admissibility is undefendable. The fourth gate (admissibility) is the one currently missing from the market and the one the current liability wave is most concerned with.
