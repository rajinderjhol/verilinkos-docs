# From Responsible AI to Verifiable Agent Actions

## A Financial Infrastructure Framework for Accountable Autonomous AI

**VeriLinkOS**

**August 2026**

---

## Abstract

Artificial intelligence is moving from systems that primarily advise humans toward systems that can act on behalf of humans and organizations.

In financial services, this transition is consequential.

An AI system that recommends a transaction presents one class of governance challenge. An autonomous agent that can initiate the transaction, call external services, delegate authority to another agent, access sensitive information, or execute a financial operation presents another.

The central question therefore evolves from:

> **Can an institution govern its AI system?**

to:

> **Can an institution establish, enforce and independently demonstrate what an autonomous agent was authorized to do and what it actually did?**

The Financial Stability Board (FSB) has identified responsible AI adoption as an important issue for financial institutions and has proposed a set of sound practices covering AI governance, risk management and the AI lifecycle. Its consultation explicitly asks whether these practices appropriately address emerging forms of AI, including GenAI and agentic AI.

VeriLinkOS submitted a response to this FSB consultation. The response is published by the FSB as part of the public consultation responses. This publication should not be interpreted as FSB endorsement of VeriLinkOS or its proposals.

This paper develops a complementary technical proposition:

> **As AI systems acquire the ability to act autonomously, responsible AI requires not only governance of the AI system, but verifiable governance of the actions performed by its agents.**

VeriLinkOS proposes an infrastructure model in which consequential agent actions can be associated with:

* a verifiable agent identity;
* an identifiable principal and delegation chain;
* explicit authority and policy;
* runtime enforcement;
* cryptographically verifiable evidence;
* provenance across chains of actions; and
* interoperable trust between independently operated agents.

The objective is not to replace existing financial governance, risk-management or regulatory frameworks.

It is to provide technical infrastructure capable of making those controls more operational, observable and independently verifiable in an increasingly autonomous financial environment.

---

# 1. The Transition from AI Systems to AI Agents

Financial institutions have used algorithmic systems for decades.

Traditional systems generally operate within relatively well-defined boundaries:

**Human → Application → Decision → Human/Institutional Action**

The emergence of agentic AI changes this model.

A more autonomous system may operate as:

**Principal → Agent → Tools → External Systems → Other Agents → Action**

An agent may:

* retrieve information;
* make decisions;
* call APIs;
* execute workflows;
* delegate tasks;
* communicate with other agents;
* access financial systems;
* initiate transactions; or
* continue operating without a human approving every intermediate step.

This creates a new governance problem.

A model can be evaluated.

An application can be authorized.

A user can be authenticated.

But an autonomous agent combines these capabilities into a system that can **exercise delegated authority**.

The important unit of accountability therefore becomes not only the model or application, but the **agent action**.

---

# 2. Responsible AI Is Necessary — But Action-Level Accountability Is Emerging

The FSB's consultation report describes responsible AI adoption as requiring financial institutions to understand the opportunities and risks of AI and apply appropriate governance and guardrails across the AI lifecycle. It proposes 12 sound practices and explicitly asks whether those practices sufficiently address emerging and complex forms of AI such as GenAI and agentic AI.

This provides an important foundation.

However, autonomous agents create an additional technical question:

> **How does an institution demonstrate, after the fact and across organizational boundaries, that a particular autonomous action was authorized, policy-compliant and attributable to a particular agent?**

Consider two situations.

### Situation A — AI recommendation

A model recommends that a payment should be made.

A human reviews the recommendation and initiates the payment.

The human remains the final operational actor.

### Situation B — Autonomous agent

An agent:

1. receives delegated authority;
2. evaluates available information;
3. calls one or more tools;
4. determines that a payment should be made;
5. initiates the payment;
6. receives a response;
7. potentially triggers another agent;
8. completes the workflow.

The second situation requires a richer accountability record.

It is no longer sufficient to know that:

> "The AI system was approved."

The institution may need to establish:

> **Which agent acted?**

> **On whose behalf?**

> **Under what authority?**

> **For what action?**

> **Under which policy?**

> **What evidence was available at the time?**

> **Was the action permitted before execution?**

> **What happened afterward?**

> **Can an independent party verify the resulting evidence?**

These are **verifiable agent action** questions.

---

# 3. From System Governance to Action Governance

The distinction can be summarized as follows.

| Traditional AI governance           | Agentic AI accountability                               |
| ----------------------------------- | ------------------------------------------------------- |
| Which model is approved?            | Which agent acted?                                      |
| Who owns the system?                | On whose behalf did the agent act?                      |
| What is the intended use?           | What specific action was requested?                     |
| What risks does the system present? | What authority did the agent possess at execution time? |
| Was the model evaluated?            | Was the particular action authorized?                   |
| Are logs retained?                  | Can the action record be independently verified?        |
| Is the application controlled?      | Can delegated authority be traced across agents?        |
| Is the system compliant?            | Can the institution prove why an action was permitted?  |

These are not competing governance models.

They are complementary layers.

**Responsible AI governance determines what an institution should permit.**

**Verifiable agent infrastructure provides mechanisms for demonstrating what happened when autonomous systems act.**

---

# 4. Why Financial Services Are Particularly Sensitive

Financial institutions already operate under extensive requirements concerning:

* authorization;
* segregation of duties;
* operational resilience;
* cybersecurity;
* auditability;
* transaction integrity;
* model risk;
* outsourcing;
* third-party risk;
* data governance;
* accountability; and
* regulatory supervision.

Agentic AI does not eliminate these requirements.

It introduces a new execution layer.

An autonomous agent may sit between a human principal and a consequential action.

For example:

**Treasury Department**

↓

**Treasury Agent**

↓

**Payment Agent**

↓

**Bank API**

↓

**Payment**

The question is no longer simply whether the treasury application was authorized.

The institution must also understand the **chain of delegated authority and execution**.

---

# 5. The Verifiable Agent Action Model

VeriLinkOS proposes treating a consequential autonomous action as a first-class object.

A simplified representation is:

**Principal**

→ delegates authority to

**Agent**

→ requests

**Action**

→ evaluated against

**Policy**

→ enforced by

**Guardian**

→ produces

**Verifiable Action Evidence**

→ linked to

**Action Chain**

This creates an explicit relationship between:

**identity + authority + policy + execution + evidence**

rather than treating each as an isolated subsystem.

---

# 6. The Five Questions of Verifiable Agent Accountability

A useful framework for financial institutions is five questions.

## 6.1 Who is acting?

An autonomous agent should have an identifiable cryptographic identity.

Identity should distinguish:

* the agent;
* its operator;
* its organizational principal;
* relevant credentials;
* lifecycle state; and
* trust information.

VeriLinkOS addresses this through **Trust Passports** and portable agent identity mechanisms.

The purpose is not merely authentication.

The identity becomes part of the evidence surrounding an action.

---

## 6.2 Who authorized the agent?

An agent generally acts because authority has been delegated.

The delegation may originate with:

* a human;
* an organization;
* another agent;
* a business process; or
* a combination of these.

The important property is **traceability**.

For example:

**Corporate Treasury**

↓

authorizes

**Treasury Agent**

↓

delegates limited authority to

**Payment Agent**

↓

requests

**$10M payment**

The system should be capable of representing that chain.

---

# 7. Authority Must Be Specific

Identity alone is insufficient.

Knowing that:

> "Agent X is legitimate"

does not answer:

> "Is Agent X allowed to perform this action?"

Authorization therefore needs to be:

* scoped;
* contextual;
* time-bounded where appropriate;
* policy-aware;
* potentially transaction-limited; and
* enforceable before execution.

This is the role of **Guardian Enforcement** within VeriLinkOS.

The conceptual principle is:

> **An action should be evaluated against authority before it is executed, not merely recorded after it occurs.**

This creates a distinction between:

**audit logging**

and

**preventive accountability**.

---

# 8. Policy Must Become Executable

A financial institution may have a policy stating:

> "Treasury agents may initiate payments up to $10 million to approved counterparties."

A governance document is useful.

But an autonomous system needs an executable representation of that policy.

The system should be able to evaluate:

**Agent**

*

**Principal**

*

**Requested action**

*

**Amount**

*

**Counterparty**

*

**Context**

*

**Delegated authority**

→

**Permit / Deny**

The decision itself should become part of the evidence.

This is the bridge between governance and runtime enforcement.

---

# 9. Evidence Must Be More Than a Log

Traditional application logs generally answer:

> "What does this system say happened?"

Verifiable action evidence seeks to answer:

> **"Can an independent party verify what happened?"**

This is the purpose of the **Verifiable Action Protocol (VAP)**.

A simplified action record may contain:

```text
Agent Identity
Principal
Delegation
Requested Action
Policy Context
Authorization Decision
Timestamp
Execution Result
Parent Action
Cryptographic Evidence
```

The exact implementation should depend on the security, privacy and regulatory requirements of the use case.

The important principle is that the evidence is designed to be:

* attributable;
* integrity-protected;
* verifiable;
* portable; and
* capable of surviving beyond the runtime that produced it.

---

# 10. Allowed and Denied Actions Both Matter

A common mistake in security systems is to record only successful operations.

For autonomous systems, denied actions can be equally important.

Consider an agent attempting:

**$50M transfer**

when its authority permits:

**$10M maximum**.

The correct outcome is:

**DENIED**

But the denial itself is useful evidence.

A verifiable record can demonstrate:

> The agent attempted the action.

> The authority was insufficient.

> The policy evaluated the request.

> The action was blocked before execution.

> The enforcement system produced a verifiable decision.

This provides evidence of **preventive control**, not merely evidence of a breach after the fact.

---

# 11. From Individual Actions to Action Chains

A single autonomous action may not tell the whole story.

Consider:

**Agent A**

→ requests information from

**Agent B**

→ delegates a task to

**Agent C**

→ calls a financial API

→ produces a transaction

Each step may affect the next.

The resulting system is better understood as an **action chain**.

VeriLinkOS's Action Chain concept provides a mechanism for associating related actions and provenance.

This is particularly important when:

* agents delegate tasks;
* multiple organizations participate;
* external tools are invoked;
* transactions depend on previous actions; or
* an incident investigation occurs.

The goal is not to record every internal model token.

The goal is to preserve the **security- and governance-relevant chain of consequential actions**.

---

# 12. Cross-Organizational Trust

The most important future use case may occur when autonomous agents cross institutional boundaries.

Consider:

**Bank A Agent**

→ interacts with

**Payment Network Agent**

→ interacts with

**Bank B Agent**

→ executes

**Transaction**

No single organization necessarily controls the complete chain.

This creates a trust problem.

Each participant needs to determine:

* Is the other agent genuine?
* Who operates it?
* Who authorized it?
* What authority does it have?
* Has its authority expired?
* Can its actions be verified?
* What evidence exists if something goes wrong?

This is where an interoperable trust layer can become more important than an institution-specific AI governance platform.

---

# 13. Interoperability Rather Than Replacement

VeriLinkOS is not intended to replace existing agent frameworks, identity systems, payment networks or financial controls.

It can instead operate between them.

A conceptual stack is:

```text
AI Models
    ↓
Agent Frameworks
    ↓
MCP / A2A / APIs / Tools
    ↓
VERILINKOS
    ├── Identity
    ├── Delegated Authority
    ├── Policy Enforcement
    ├── Verifiable Action Evidence
    └── Provenance
    ↓
Enterprise / Financial Infrastructure
```

This positioning is important.

Agent protocols answer questions such as:

> "How can agents communicate?"

Verifiable agent infrastructure answers:

> **"How can participants establish authority and prove consequential actions?"**

These capabilities can coexist.

---

# 14. Relationship to Existing Responsible-AI Controls

VeriLinkOS does not argue that cryptographic evidence replaces:

* human oversight;
* model validation;
* risk assessment;
* governance committees;
* internal controls;
* cybersecurity;
* operational resilience;
* regulatory supervision;
* data governance; or
* legal accountability.

Those remain essential.

Instead, VeriLinkOS can provide an **execution and evidence layer** underneath them.

A simplified model is:

**Governance**

determines

> what should be allowed.

**Policy**

defines

> how that intent becomes enforceable.

**Guardian**

enforces

> whether the requested action can execute.

**VAP**

records and proves

> what decision was made and what happened.

**Action Chain**

preserves

> how consequential actions relate to one another.

This creates a bridge between policy and execution.

---

# 15. A Reference Financial Use Case

Consider an autonomous corporate treasury system.

### Step 1 — Identity

A treasury agent presents a verifiable identity.

### Step 2 — Principal

The agent establishes that it acts on behalf of a corporate treasury function.

### Step 3 — Authority

The agent possesses delegated authority to initiate payments up to a defined threshold.

### Step 4 — Request

The agent requests:

**USD 10,000,000**

to an approved counterparty.

### Step 5 — Policy

The request is evaluated against:

* amount;
* counterparty;
* jurisdiction;
* time;
* account;
* transaction type;
* delegated authority;
* institutional policy.

### Step 6 — Enforcement

Guardian determines whether the action is permitted.

### Step 7 — Execution

The transaction is executed if permitted.

### Step 8 — Evidence

VAP produces a cryptographically verifiable action record.

### Step 9 — Provenance

The action becomes part of the relevant Action Chain.

### Step 10 — Verification

An authorized auditor, compliance function or other relying party can verify the evidence.

The important outcome is not simply:

> "The payment succeeded."

It is:

> **"The institution can demonstrate why the autonomous action was authorized, how it was enforced, and what happened."**

---

# 16. Agentic Commerce

Autonomous commerce introduces another dimension.

An agent may eventually:

* search for products;
* compare suppliers;
* negotiate;
* purchase;
* manage subscriptions;
* initiate payments; or
* interact with other commercial agents.

Payment networks are already exploring mechanisms for establishing trust between AI agents and merchants. For example, Visa's public Trusted Agent Protocol describes cryptographic mechanisms for an AI agent to prove its identity and associated authorization to merchants.

This is significant because it demonstrates that **agent identity and authorization are becoming practical financial-infrastructure concerns rather than purely theoretical research topics.**

VeriLinkOS extends the concept toward a broader action lifecycle:

**identity → authority → policy → execution → evidence → provenance**

The long-term opportunity is interoperability between these layers.

---

# 17. The Regulatory Value Proposition

For a financial institution, the value of verifiable agent actions is not that cryptography magically makes AI safe.

It does not.

The value is that it can make certain controls:

* more explicit;
* more enforceable;
* more observable;
* more portable; and
* easier to independently verify.

This can support several existing governance objectives.

### Accountability

Associate consequential actions with an identifiable agent and principal.

### Auditability

Provide evidence surrounding authorization and execution.

### Operational resilience

Preserve relevant action history even when multiple systems participate.

### Third-party risk

Make interactions with external agents more observable.

### Incident investigation

Reconstruct consequential agent actions and delegation.

### Supervisory evidence

Potentially provide machine-verifiable evidence of control operation.

These are complementary to the FSB's broader responsible-AI framework.

---

# 18. The Importance of Proportionality

Not every AI action requires cryptographic evidence.

A low-risk internal summarization agent does not need the same controls as an agent that:

* moves money;
* approves credit;
* changes a market position;
* accesses highly sensitive information; or
* delegates authority to another system.

Verifiable agent infrastructure should therefore support **risk-based deployment**.

Possible tiers include:

### Low risk

Conventional logging and governance.

### Moderate risk

Identity + authorization + enhanced audit.

### High risk

Pre-action enforcement + cryptographic evidence + provenance.

### Critical / systemic

Cross-organizational identity + delegated authority + independently verifiable evidence + enhanced resilience and supervisory controls.

This approach is consistent with the FSB's emphasis on responsible adoption and proportionate application of controls across AI use cases.

---

# 19. What VeriLinkOS Is — and Is Not

## VeriLinkOS is:

* a control and trust layer for autonomous agents;
* an identity and authority framework;
* a runtime policy enforcement mechanism;
* a protocol for verifiable action evidence;
* a provenance mechanism for consequential agent actions; and
* an interoperability layer for agentic systems.

## VeriLinkOS is not:

* a replacement for financial regulation;
* a substitute for human accountability;
* a guarantee that an AI model is correct;
* a replacement for model-risk management;
* a replacement for cybersecurity;
* a replacement for institutional governance; or
* an assertion that every AI action requires blockchain or cryptographic recording.

The purpose is narrower and more practical:

> **Make consequential autonomous actions governable and verifiable.**

---

# 20. From Logs to Proof

The conceptual transition can be summarized as:

### Traditional

**System**

→ produces

**Log**

→ institution trusts

**Log**

### Verifiable

**Agent**

→ presents

**Identity + Authority**

→ requests

**Action**

→ policy evaluates

**Action**

→ enforcement decides

**Permit / Deny**

→ system produces

**Cryptographically Verifiable Evidence**

→ authorized parties independently verify

**Evidence**

The second model does not eliminate traditional logs.

It adds an evidence layer designed for environments in which actions may cross organizational, technological and jurisdictional boundaries.

---

# 21. A Potential Trust Fabric for the Agentic Financial System

As autonomous systems become more capable, financial infrastructure may evolve from:

**Human identity**

to:

**Human identity + machine identity**

and eventually:

**Human identity + organizational identity + agent identity + delegated authority + verifiable action evidence.**

This could become an important infrastructure primitive for agentic finance.

The ultimate objective is not to make every organization trust every AI agent.

It is to make trust **machine-readable, policy-controlled and evidence-based**.

---

# 22. The Strategic Opportunity

The emerging agentic economy may require infrastructure analogous to existing financial trust infrastructure.

Traditional financial systems developed mechanisms for:

* identity;
* authorization;
* messaging;
* transaction integrity;
* settlement;
* audit;
* dispute resolution; and
* regulatory oversight.

Autonomous agents introduce a new participant:

> **the machine acting with delegated authority.**

That participant requires mechanisms for establishing:

**Who it is.**

**Who authorized it.**

**What it can do.**

**What it actually did.**

**What other agents it relied upon.**

**What evidence exists.**

The opportunity is therefore larger than AI governance alone.

It is the emergence of **machine-to-machine trust infrastructure**.

---

# 23. Implications for Financial Institutions

Financial institutions preparing for agentic AI should consider developing an explicit control model for autonomous actions.

Questions to consider include:

1. Which AI systems can act autonomously?
2. Which agents have persistent identities?
3. Who is the principal behind each agent?
4. How is authority delegated?
5. Can delegated authority be constrained?
6. Which actions require pre-execution policy enforcement?
7. What evidence is generated for allowed actions?
8. What evidence is generated for denied actions?
9. Can action chains be reconstructed?
10. Can evidence be independently verified?
11. How are external agents trusted?
12. What happens when an agent's authority is revoked?
13. How are compromised agents isolated?
14. How can regulators and auditors obtain relevant evidence?
15. How does the control model scale across jurisdictions?

These questions transform responsible AI from a governance-only discussion into an operational architecture.

---

# 24. Implications for Regulators and Standards Bodies

A future regulatory framework for agentic finance may benefit from distinguishing between:

**governance requirements**

and

**technical evidence mechanisms.**

Regulators do not necessarily need to prescribe one vendor or protocol.

Instead, standards could define outcomes such as:

> A financial institution should be capable of demonstrating the identity, authority, policy basis and execution history associated with specified categories of consequential autonomous actions.

Multiple implementations could satisfy such requirements.

This creates room for open protocols and interoperable infrastructure.

VeriLinkOS proposes one technical approach to this problem.

---

# 25. The FSB Consultation as a Point of Convergence

The publication of the FSB consultation responses is an important milestone in the emerging discussion around responsible AI in finance.

The FSB's consultation explicitly asks whether its proposed sound practices appropriately address emerging and complex forms of AI, including agentic AI.

VeriLinkOS's participation in the public consultation places its proposal within that broader discussion.

The significance is not that the FSB has endorsed VeriLinkOS.

It has not.

The significance is that the financial system is actively working through the governance implications of increasingly autonomous AI, while technical proposals are emerging around identity, authorization, evidence and interoperability.

VeriLinkOS's thesis is:

> **Responsible AI adoption ultimately requires mechanisms for responsible autonomous action.**

And responsible autonomous action requires more than model governance.

It requires **verifiable authority and verifiable execution.**

---

# 26. A Proposed Framework

VeriLinkOS proposes the following conceptual framework for consequential agent actions:

```text
                 PRINCIPAL
                     │
                     ▼
               AGENT IDENTITY
                     │
                     ▼
            DELEGATED AUTHORITY
                     │
                     ▼
              ACTION REQUEST
                     │
                     ▼
              POLICY EVALUATION
                     │
                     ▼
           GUARDIAN ENFORCEMENT
                 ┌───┴───┐
                 │       │
              DENY      ALLOW
                 │       │
                 ▼       ▼
              RECEIPT  EXECUTION
                         │
                         ▼
                 VERIFIABLE ACTION
                         │
                         ▼
                    ACTION CHAIN
                         │
                         ▼
                AUDIT / VERIFICATION
```

The framework is deliberately modular.

Organizations can adopt the components appropriate to their risk profile.

---

# 27. Design Principles

A verifiable agent infrastructure should follow several principles.

## 27.1 Identity before authority

An entity cannot meaningfully receive delegated authority without an identifiable identity.

## 27.2 Authority before action

Consequential actions should be evaluated before execution.

## 27.3 Evidence at execution time

Evidence should be generated when the authorization decision and action occur, rather than reconstructed later.

## 27.4 Denials are evidence

Blocked actions should be observable as part of the security record.

## 27.5 Delegation must be traceable

Authority should remain attributable through agent-to-agent delegation.

## 27.6 Verification should be independent

Where appropriate, a relying party should be able to verify evidence without trusting the original application runtime.

## 27.7 Interoperability over lock-in

The trust layer should integrate with existing identity, agent, payment and enterprise infrastructure.

## 27.8 Risk-based deployment

Higher-risk actions should receive stronger controls.

## 27.9 Privacy by design

Evidence should reveal what is necessary for accountability without unnecessarily exposing sensitive information.

## 27.10 Human accountability remains fundamental

An autonomous agent may execute an action, but responsibility ultimately remains with the humans and organizations that deploy, authorize and govern it.

---

# 28. Security and Limitations

Cryptographic evidence does not prove that an AI decision was correct.

It can establish properties such as:

* who presented an identity;
* what authority was presented;
* what policy was evaluated;
* what decision was recorded;
* whether evidence was altered; and
* how actions relate to one another.

It cannot by itself establish:

> "The model's reasoning was correct."

Nor can it eliminate:

* compromised credentials;
* malicious principals;
* flawed policies;
* incorrect data;
* unsafe models;
* implementation bugs; or
* operational failures.

Verifiable action infrastructure should therefore be considered a **control layer**, not a complete AI safety solution.

---

# 29. Toward an Open Standard

For this problem to become systemic infrastructure, interoperability is essential.

A future open standard for verifiable agent actions could define:

* agent identity requirements;
* delegation structures;
* authority constraints;
* action schemas;
* policy decision records;
* cryptographic evidence formats;
* verification procedures;
* revocation;
* provenance;
* privacy controls;
* conformance testing; and
* interoperability requirements.

Multiple organizations could implement the standard.

Financial institutions could choose providers.

Regulators could evaluate outcomes rather than endorse a specific vendor.

Agent developers could integrate once and operate across multiple institutions.

This is the direction in which VeriLinkOS intends to develop its protocol architecture.

---

# 30. Conclusion

The next phase of responsible AI in financial services will not be defined only by better models.

It will also be defined by how safely those models are allowed to act.

As autonomous agents become capable of making decisions, invoking tools, delegating tasks and executing consequential transactions, financial institutions will increasingly need to answer a fundamental question:

> **Can we prove what our AI agents were authorized to do—and what they actually did?**

The FSB's consultation on responsible AI adoption demonstrates that financial authorities and institutions are actively considering how governance frameworks should evolve as AI becomes more capable and autonomous.

VeriLinkOS proposes that one important part of that evolution is an infrastructure layer for **verifiable agent actions**.

The model is straightforward:

**Identify the agent.**

**Identify the principal.**

**Establish delegated authority.**

**Evaluate the action against policy.**

**Enforce before execution.**

**Generate verifiable evidence.**

**Preserve provenance.**

**Enable independent verification.**

The objective is not to replace responsible AI governance.

It is to make responsible autonomous action technically enforceable and demonstrable.

The future financial system may contain millions—or billions—of autonomous agents.

Those agents will not merely generate information.

They will act.

When they do, trust cannot depend solely on assertions, application logs or assumptions.

It must increasingly depend on **verifiable evidence of authority and action**.

That is the infrastructure problem VeriLinkOS is designed to address.

---

## Appendix A — Relationship to the FSB Consultation

VeriLinkOS submitted a response to the FSB consultation report **"Sound Practices for Responsible Adoption of Artificial Intelligence (AI)"**.

The FSB published the consultation report on 10 June 2026. The report identifies benefits and risks associated with AI adoption by financial institutions and proposes 12 sound practices covering organisation-wide AI governance and relevant stages of the AI lifecycle. The consultation explicitly asks whether the proposed practices appropriately address emerging and complex forms of AI, including GenAI and agentic AI.

The FSB subsequently published the public responses to the consultation.

**VeriLinkOS is one of the respondents whose submission is publicly listed by the FSB.**

The publication of the submission does **not** constitute FSB endorsement, certification or validation of VeriLinkOS.

The purpose of this paper is to contribute a technical perspective to the broader discussion:

> **How can financial institutions operationalize accountability when AI systems become autonomous actors?**

---

## Appendix B — Terminology

### Agent

A software system capable of taking actions toward a goal, potentially using tools, data or other agents.

### Principal

The human or organization on whose behalf an agent acts.

### Delegated Authority

 a defined set of permissions granted to an agent to perform specified actions.

### Agent Identity

A persistent or otherwise verifiable identifier associated with an agent and its relevant operator or principal.

### Guardian Enforcement

The runtime control layer that evaluates and enforces whether a requested action is permitted.

### Verifiable Action Protocol (VAP)

The VeriLinkOS protocol for representing and verifying cryptographic evidence associated with agent actions.

### Trust Passport

A portable representation of agent identity and associated trust information.

### Action Chain

A provenance structure linking consequential actions and their relationships across an agent workflow.

### Verifiable Agent Action

An action for which identity, authority, policy decision and relevant execution evidence can be independently verified to an appropriate level of assurance.

---

## Appendix C — Practical Questions for Financial Institutions

Organizations evaluating agentic AI can begin with a simple exercise.

For every autonomous system, ask:

**1. What can it do?**

**2. Who authorized it?**

**3. What happens if it attempts something outside its authority?**

**4. Can we prove what policy was applied?**

**5. Can we prove the decision?**

**6. Can we reconstruct the action chain?**

**7. Can another organization verify the evidence?**

**8. Can the authority be revoked?**

**9. Can the system continue to provide evidence during an incident?**

**10. Can we demonstrate these controls to an auditor or supervisor?**

If the answer to several of these questions is "no," the organization may have an AI governance framework—but it may not yet have an **agent accountability infrastructure**.

---

## Appendix D — References

1. Financial Stability Board, *Sound Practices for Responsible Adoption of Artificial Intelligence (AI): Consultation Report*, 10 June 2026.

2. Financial Stability Board, *Public Responses to Consultation on Sound Practices for Responsible Adoption of Artificial Intelligence (AI)*, August 2026. [FSB public responses](https://www.fsb.org/2026/08/public-responses-to-consultation-on-sound-practices-for-responsible-adoption-of-artificial-intelligence-ai/)

3. VeriLinkOS, *VeriLinkOS Documentation and Protocol Specifications*. [VeriLinkOS GitHub repository](https://github.com/rajinderjhol/verilinkos-docs)

4. Visa, *Trusted Agent Protocol*, open-source protocol for establishing cryptographic trust between AI agents and merchants.

---

**VeriLinkOS**

*From Responsible AI to Verifiable Agent Actions*

*August 2026*
