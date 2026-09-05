# 04 — Mercor / LiteLLM supply chain breach

**Date:** breach 31 March 2026; reported 3 April 2026
**Impact:** proprietary training-data workflows and contractor information potentially exposed, affecting work performed for Meta and other AI labs

---

## What happened

Mercor suffered a breach involving malicious updates to **LiteLLM** — a widely used open-source proxy/SDK layer that sits between applications and model providers. Because LiteLLM occupies the position it does in the stack, a compromise there gets access to whatever passes through it: prompts, responses, API keys, and in this case exposure touching proprietary training-data workflows and contractor information.

## Why this one matters

Two reasons, and the second is the one people miss.

**First:** this is `AML.T0010` in its most ordinary and therefore most dangerous form. Not an exotic backdoored model — a normal dependency update, through a normal package channel, into a normal CI pipeline. The AI-specific part of the AI supply chain is the least of it; the boring software supply chain is where the compromise actually lived.

**Second, the part that's specific to AI infrastructure:** an LLM proxy layer is an unusually high-value position. A compromised logging library sees log lines. A compromised LLM gateway sees *every prompt and every response* — which, in an organisation using AI seriously, is a superset of a great deal of sensitive business context. The blast radius of a component is a function of what flows through it, and in modern AI stacks the gateway is where everything flows.

Most dependency-risk scoring treats packages roughly uniformly by popularity and CVE count. Position in the data path deserves to be a scoring input on its own.

## OWASP LLM Top 10 classes

| Class | Applies because |
|---|---|
| **LLM03 — Supply Chain** | **Primary.** Compromise arrived through a dependency in the AI software stack. |
| **LLM04 — Data and Model Poisoning** | Training-data workflows were in scope of the exposure — creating downstream integrity risk to anything trained on that pipeline. |

## MITRE ATLAS technique chain

| Technique | Role in this incident |
|---|---|
| `AML.T0010.001` — AI Supply Chain Compromise: AI Software | **Primary.** The compromised component is a library in the AI software stack. |
| `AML.T0011.001` — User Execution: Malicious Package | The malicious update is pulled and executed through normal dependency installation. |
| `AML.T0055` — Unsecured Credentials | API keys transiting or stored by the proxy layer become reachable. |
| `AML.T0035` — AI Artifact Collection | Collection of training-data workflow artifacts and related material. |

## NIST AI RMF — which function owned this

**GOVERN, then MAP.**

- **GOVERN** owns it: third-party component vetting, dependency-update policy, and — critically — *who is accountable* for the security posture of infrastructure the AI stack depends on. In many organisations, nobody owns "the LLM gateway" from a risk perspective; it was adopted by engineers as a convenience layer and never entered the risk register.
- **MAP** is close behind: knowing what is actually in the AI supply chain, and where each component sits in the data path. You cannot govern a dependency you have not inventoried.

This pairing — GOVERN + MAP — is the same one Incident 04's class (LLM03) drew in the `redteam-log` framework mapping, which is a useful consistency check: the class-level mapping and the incident-level mapping agree.

## Counterfactual control

**The one control:** a model/AI bill-of-materials that explicitly records *data-path position* for each component, combined with pinned-and-hash-verified dependency versions for anything in that path. Pinning alone stops the silent malicious update; recording position is what makes someone look at the gateway and realise it deserves stricter treatment than a formatting library.

Supporting control: credentials transiting the proxy should be short-lived and scoped per-application, so that a gateway compromise yields keys that expire rather than keys that persist.

## One line for a non-technical risk owner

*"One routine software update to a component nobody thought of as sensitive exposed every conversation our AI systems were having."*

## Sources

- OWASP GenAI Security Project — Exploit Round-up Report Q1 2026: https://genai.owasp.org/2026/04/14/owasp-genai-exploit-round-up-report-q1-2026/
- MITRE ATLAS technique reference: https://atlas.mitre.org/techniques/AML.T0010
