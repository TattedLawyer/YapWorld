# YapWorld

An agentic AI platform shipped to three markets from one core: a consumer companion, a clinical engagement product for hospitals and insurers, and an aged-care product for operators, residents and families.

Live: **[yapworld.net](https://yapworld.net)** · **[yaphealth.net](https://yaphealth.net)** · **[yapcare.net](https://yapcare.net)**

This repository documents the engineering decisions behind it. I led the team that built it and made the calls below.

---

## Four decisions that shaped the build

### One core, a policy layer per market

A consumer companion, a clinical product and an aged-care product need the same underlying agent and different answers to one question: what may this AI say to this person, with this data, under this regulator.

Three products meant three agents to maintain, three safety reviews, three regressions every time the core changed. The alternative was one core with deployment-scoped policy sitting on top.

The cost is real. Every core change now has to be correct under the strictest of the three regimes, which slows the consumer product down to the speed of the clinical one. The return is that the third market shipped without rebuilding the first two.

### Memory that decides what to keep

Retrieval-augmented memory searches past interactions and returns what matches. It never decides what mattered. For a product where the relationship is the feature, that fails in a specific way: the model can recall a conversation but cannot tell which conversation changed something.

So memory here is an active component. An agent reads each interaction and decides what is worth keeping. Storage is organised by semantic adjacency rather than chronology, memories confirmed by later behaviour gain retrieval weight, and contradicted ones decay.

The failure mode this creates is worth naming: an autonomous curator can be wrong about what mattered. The mitigation is graceful fallback — if retrieval fails, the agent answers from conversation context instead of crashing.

[Full documentation](agentic-memory-system.md)

### Safety enforced by deployment, not by prompt

Prompt-level guardrails fail exactly when they matter. An instruction that says do not discuss self-harm holds until a user finds the phrasing that gets around it.

The safety layer is therefore structural and swaps per deployment. What the agent may say near a health claim, to a minor, or to a resident in care is governed by which policy is loaded, not by what the prompt asks for. Real-time monitoring escalates to professional crisis resources rather than attempting to handle a crisis in-product.

That last boundary was a deliberate limit: the system is built to recognise when it is out of its depth and hand off, because an AI companion that tries to manage a mental-health emergency is a liability, not a feature.

[Full documentation](Guardian-Safety-System.md)

### 75+ languages, native rather than translated

Translating an English product produces something that reads as foreign. For a companion that has to feel like it knows you, that is fatal.

The platform speaks over 75 languages across all three verticals, handled at the model layer rather than by a translation pass. Acceptance was verified with native speakers, because the test for a companion is whether a person believes it and no automated score captures that.

### Per-user runtime, and the economics that made it possible

Every user-agent pair runs in its own dedicated pod. That is the right design for a persistent relationship, because stateless invocation cannot hold one, and it is the wrong design for almost any budget.

It became affordable when the runtime layer did. A Rust-based agent runtime holds each agent in single-digit megabytes with cold starts in milliseconds; the same agent on a Python runtime consumes hundreds of megabytes per process. Per-user pods stopped being a thought experiment at that point.

Orchestration provisions pods and routes between them. Persistent state is scoped to the pair, so one user's context cannot leak into another's.

### Identity grounded in two streams

A psychological model with no ground truth drifts into narrative. The platform grounds it against continuous biometric signal.

The biometric pipeline ingests from any wearable the user authorises, the first-party smart ring or Apple Watch, Fitbit, Whoop, Oura, Garmin, normalising heart rate, heart rate variability, sleep, recovery, temperature and activity across devices that report them differently. The psychological stream comes from conversation and behavioural context.

Both feed one model. Wearable platforms stop at biometrics. Conversational AI stops at language. Neither alone can tell you what a bad night's sleep means for the person having it.

Retrieval runs over a vector store, with a caching layer in front of the model calls. That caching is a cost decision as much as a latency one: at per-user pod density, uncached inference is the line item that decides whether the unit economics work.

## Also in this repository

**[Personality intelligence](personality-intelligence-system.md)** — modelling a user from conversation and behaviour, anchored to established psychometric instruments so the output cross-references to published literature rather than to an internal scale nobody can audit.

**[Wellness and therapeutic applications](Wellness-Therapeutic-Applications.md)** — the evidence base, and the line between wellness guidance and clinical claims.

**[Creator economy](creator-economy.md)** — the persona ecosystem and creator monetisation.

Seven US provisional patent applications cover the architecture.

## Related work

**[Switchboard](https://github.com/TattedLawyer/switchboard-ai-integration)** — supervised AI outreach. Connects business systems that do not share data, then puts an approval-gated agent on top: no outbound action reaches a real customer without a human approving it, enforced by database triggers rather than application code, so a bug in the agent cannot bypass it. Live voice agent with sub-second conversational turn latency over telephony.

**[Contract Coach](https://github.com/TattedLawyer/contract-coach)** — retrieval-augmented contract analysis. MIT licensed, runnable, live demo.

---

`agentic AI` · `LLM` · `conversational memory` · `RAG` · `AI safety` · `guardrails` · `responsible AI` · `multi-tenant architecture` · `digital health` · `wearables` · `multilingual AI` · `psychometrics`
