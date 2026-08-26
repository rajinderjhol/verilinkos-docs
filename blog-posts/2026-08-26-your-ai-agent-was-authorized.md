# Your AI Agent Was Authorized. Was the Transaction?

Autonomous AI agents are moving from generating text to taking actions.

They can call APIs, purchase goods, initiate payments, move digital assets, modify systems, and increasingly operate with limited human intervention.

That creates a security question that I don't think gets enough attention:

> **How do you prove that the action that actually executes is the action that was authorized?**

Not that the agent had *some* authorization.

Not that the request carried the correct `intent_id`.

Not that an internal audit log says `APPROVED`.

I mean the **actual semantic transaction**:

**amount, recipient, asset, parameters, deadline, instructions and other execution conditions.**

What happens if those change after authorization but before the irreversible side effect?

I have been building a demonstration around exactly that problem. I call the interval between authorization and execution **Gap Governance**.

---

## A simple attack

Suppose an autonomous procurement agent is authorized to purchase:

> **€400 → supplier_A**

The authorization succeeds.

Then the execution request is modified in-flight:

> **€4,000 → supplier_A**

The agent, UI, or upstream application may be compromised. The request still carries the same intent identifier. It may even look superficially legitimate.

But the semantic transaction has changed.

The system should not execute it.

The demonstration looks like this:

```text
AUTHORIZED INTENT

€400 → supplier_A
       │
       ▼
    PERMIT
       │
       ▼
EXECUTION PENDING
       │
       │  request modified
       ▼
€4,000 → supplier_A
       │
       ▼
EXECUTION GATEWAY
       │
       ├── Authorized amount: €400
       ├── Requested amount:  €4,000
       │
       └── DENY
             │
             ▼
       NO SIDE EFFECT
```

The settlement/payment adapter is **never called**.

That's the property I wanted to demonstrate: **The governed execution boundary prevents unauthorized parameters from reaching the settlement system.**

---

# The important part isn't the UI

A common approach would be to put a check in the application or interface.

But if an attacker can bypass the UI and call the execution interface directly, that isn't an adequate enforcement boundary.

So the VeriLinkOS reference architecture puts the enforcement point immediately before the side effect:

```text
             PRINCIPAL
                 │
                 ▼
               AGENT
                 │
                 ▼
          AUTHORIZED INTENT
                 │
                 ▼
            PERMIT GRANTED
                 │
                 ▼
         EXECUTION PENDING
                 │
                 ▼
        ┌───────────────────┐
        │  EXECUTION        │
        │  GATEWAY          │
        │                   │
        │  Semantic binding │
        │  Policy check     │
        │  Integrity check  │
        └─────────┬─────────┘
                  │
                  ▼
           SIDE-EFFECT ADAPTER
                  │
          ┌───────┼───────┐
          ▼       ▼       ▼
       Payment  Crypto  Procurement
```

The downstream adapters are intentionally **governance-dumb**.

They don't decide whether something is authorized. They execute what the gateway sends them. The governance decision happens before them.

---

# Then I tried to break it

The demonstration isn't just a happy-path transaction. The test harness attacks the boundary.

### Amount escalation
`PERMIT: €400` → `REQUEST: €4,000` → `DENY` → `ADAPTER NOT CALLED`

### Recipient manipulation
`PERMIT: supplier_A` → `REQUEST: wallet_B` → `DENY` → `ADAPTER NOT CALLED`

### Permit expiry
`PERMIT: valid until T` → `REQUEST: execution after T` → `DENY` → `ADAPTER NOT CALLED`

### Replay
`PERMIT + SETTLEMENT` → `SECOND EXECUTION` → `DENY` → `ADAPTER NOT CALLED`

### Authority revocation
`PERMIT` → `AUTHORITY REVOKED` → `EXECUTION` → `DENY`

### Prompt/instruction manipulation
`AUTHORIZED INTENT` → `MALICIOUS INSTRUCTION` → `BOUND CONTEXT MISMATCH` → `DENY`

### Direct bypass
Don't use the UI. Call the execution gateway directly with unauthorized parameters. The gateway still rejects the request. The side-effect system remains untouched.

---

# Then there's the second problem: evidence

Preventing an unauthorized transaction is one problem. Proving what happened is another.

A system shouldn't have to say:

> “Trust our logs. We promise the transaction was denied.”

The demonstration produces an evidence bundle containing the relevant authorization, execution state, and cryptographic evidence.

A separate verifier can consume the evidence without importing the VeriLinkOS implementation.

```text
VERILINKOS INDEPENDENT VERIFIER

Evidence: receipt-001.json

Authorization       VALID
Intent binding      VALID
Semantic binding    VALID
Deadline            VALID
Authority           VALID
Replay status       VALID
Evidence integrity  VALID
State continuity    VALID

RESULT: VALID
```

The point isn't that cryptography magically makes a system secure.

The point is that the **claim becomes independently testable**.

---

# The question I'm interested in now

I don't think the interesting question is:

> **“Can AI agents be governed?”**

That's too broad.

The more concrete question is:

> **Where is the enforcement boundary between what an autonomous system was authorized to do and what it actually executes?**

I'm not claiming that this solves autonomous-system security in general. It doesn't. A compromised machine, stolen credentials, malicious infrastructure, flawed policy, compromised signing keys and many other threats remain separate problems.

The claim is narrower:

> **If an execution request reaches the demonstrated enforcement boundary with semantics that differ from the authorized intent, the boundary refuses to pass it to the side-effect system.**

That's a claim that can be tested.

And broken.

---

# I'm looking for people working on systems where autonomous software can cause consequential actions

* AI payment systems
* agentic commerce
* crypto/asset infrastructure
* autonomous trading
* procurement automation
* enterprise agents
* payment infrastructure
* agent wallets
* transaction authorization
* AI security
* identity and authorization infrastructure

I'm particularly interested in people who think they **already have this problem solved**.

Bring the architecture. Bring the attack. Bring the objection.

I'll show you the reference implementation and let you try to modify the transaction between authorization and execution.

If you have a better way to solve the problem, I want to understand it.

If your system already has this boundary, I want to understand where you put it.

And if it doesn't, I'd like to understand what prevents you from putting one there.

---

## The question I'd like to leave you with

Imagine your autonomous agent is authorized to do this:

> **€400 → supplier_A**

Five seconds later, the system actually attempts:

> **€4,000 → supplier_A**

**Where, exactly, does your architecture stop that transaction?**

And can you prove—independently—that it was stopped for the right reason?

That's the conversation I'm interested in having.

**If you're building autonomous systems capable of moving money, assets, or creating real-world commitments, reach out.**
