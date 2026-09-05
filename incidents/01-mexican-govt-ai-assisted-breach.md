# 01 — Mexican government breach, AI-assisted

**Date:** attack late December 2025 – January 2026; publicly reported 25 February 2026
**Impact:** approximately 150 GB of tax and voter data exfiltrated from Mexican government agencies

---

## What happened

Attackers used a commercial LLM (Anthropic's Claude) as an operational tool to automate reconnaissance and accelerate exploit development against Mexican government systems. The AI was not the target and was not the system that was breached — it was the attacker's productivity tool. Roughly 150 GB of tax and voter records were taken.

## Why this one is first in the index

Because it inverts the assumption most AI-incident taxonomies are built on.

Nearly every framework treats the AI system as the *asset under attack* — something with a prompt to inject, weights to steal, a context window to leak. Here the victim organisation may not have run any AI at all. The AI sat entirely on the attacker's side, compressing the reconnaissance-and-exploit-development phase that used to gate how fast an unsophisticated attacker could move.

That distinction matters for defenders in a specific way: **there is no AI control you can apply to your own stack that mitigates this.** Your model governance policy is irrelevant. What changes is the assumed *tempo and skill floor* of the adversary in your threat model.

## OWASP LLM Top 10 classes

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | The model was driven to perform chained operational tasks (recon, exploit iteration) well beyond a single answer-a-question interaction. |
| **LLM02 — Sensitive Information Disclosure** | Applies to the *outcome* — data exfiltration — though via conventional means rather than via the model. |

Note the awkwardness: both classes are describing the attacker's tool, not the victim's system. The Top 10 is written from the perspective of someone deploying an LLM. It fits imperfectly here, which is itself informative.

## MITRE ATLAS technique chain

| Technique | Role in this incident |
|---|---|
| `AML.T0016.002` — Obtain Capabilities: Generative AI | **Primary.** The adversary acquired and used a commercial generative model as offensive tooling. |
| `AML.T0006` — Active Scanning | Reconnaissance against target infrastructure, accelerated by the model. |
| `AML.T0049` — Exploit Public-Facing Application | The actual entry vector into the victim environment. |
| `AML.T0025` — Exfiltration via Cyber Means | Bulk data removal by conventional means, not via a model API. |

ATLAS handles this case better than OWASP does, precisely because `AML.T0016.002` exists to describe the adversary *obtaining* AI capability rather than attacking it. This is the framework earning its keep.

## NIST AI RMF — which function owned this

**GOVERN.**

Not MAP, MEASURE or MANAGE — because there is no AI system in the victim's inventory to map or measure. The governance failure, if we assign one, is at the level of threat modelling policy: an organisation whose threat model still assumes exploit development is slow and expensive is governing against an adversary that no longer exists.

For the *provider* side (the lab whose model was used), GOVERN also applies, in the form of misuse detection and enforcement obligations — a different accountability question, and one worth keeping analytically separate from the victim's.

## Counterfactual control

**For the victim:** conventional, and that's the point — the entry vector was a public-facing application exploit. Patch latency and external attack-surface management are the controls that mattered. The AI angle changed *how quickly* the attacker found the way in, not *what* the way in was. A defender who reads this incident and starts writing an LLM policy has drawn the wrong lesson.

**Threat-model amendment this incident justifies:** raise the assumed baseline capability of unsophisticated adversaries. The gap between "script kiddie" and "competent operator" is narrowing, and time-to-exploit assumptions built on the old gap are now optimistic.

## One line for a non-technical risk owner

*"The attackers used AI to move faster than our patching cycle assumed anyone could — nothing about our own AI systems was involved, and nothing about our AI policy would have helped."*

## Sources

- OWASP GenAI Security Project — Exploit Round-up Report Q1 2026: https://genai.owasp.org/2026/04/14/owasp-genai-exploit-round-up-report-q1-2026/
