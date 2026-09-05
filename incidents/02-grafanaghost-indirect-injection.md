# 02 — GrafanaGhost indirect prompt injection

**Date:** disclosed 7 April 2026; vendor patch acknowledged 8 April 2026
**Impact:** enterprise data exfiltration from Grafana AI features via attacker-controlled image rendering

---

## What happened

Researchers found that hidden instructions planted in external resources — content Grafana's AI features would ingest as part of normal operation — could cause those features to exfiltrate enterprise data. The exfiltration path was the image-rendering workflow: the injected instruction caused the assistant to construct an image URL pointing at an attacker-controlled server, with the sensitive data embedded in the URL. Rendering the image performed the exfiltration.

The user never typed anything malicious. The attacker never touched the prompt box.

## Why this one matters

This is the cleanest available example of the **indirect** injection case that most defences quietly ignore.

Input filtering — the most commonly deployed prompt-injection control — is looking at what the *user* typed. Here the user typed a perfectly ordinary request. The malicious instruction arrived through the content pipeline, which is usually treated as data and therefore not inspected as instruction.

The second lesson is subtler and more transferable: **the exfiltration channel was a rendering feature, not a network call.** No tool with "send data" semantics was invoked. An outbound-request policy scoped to "tools the agent can call" would not have covered an `<img>` tag. Any component that causes the client to fetch a URL the model influenced is an exfiltration channel, whether or not it looks like one.

## OWASP LLM Top 10 classes

| Class | Applies because |
|---|---|
| **LLM01 — Prompt Injection** | **Primary.** Instructions embedded in ingested content overrode intended behaviour. |
| **LLM02 — Sensitive Information Disclosure** | Enterprise data left the boundary as a direct consequence. |

## MITRE ATLAS technique chain

| Technique | Role in this incident |
|---|---|
| `AML.T0066` — Retrieval Content Crafting | Attacker prepares the poisoned external content the system will later ingest. |
| `AML.T0051.001` — LLM Prompt Injection: Indirect | **Primary.** The instruction reaches the model through ingested content, not user input. |
| `AML.T0057` — LLM Data Leakage | The model surfaces data it had legitimate access to, into attacker-reachable output. |
| `AML.T0025` — Exfiltration via Cyber Means | The image fetch delivers the data to attacker infrastructure. |

Worth noting `AML.T0051.002` (**Triggered**) exists as a third sub-technique and is easy to confuse with Indirect. Triggered describes injection that lies dormant until a specific condition activates it. This incident is Indirect, not Triggered — the instruction fired on ingestion, with no trigger condition.

## NIST AI RMF — which function owned this

**MAP, then MEASURE.**

- **MAP** is the primary owner. The failure begins with not having identified that *ingested external content is an untrusted input channel* for this system. Grafana's AI features pull from dashboards, data sources and external resources by design — that surface exists at architecture time, and MAP is where it should have been enumerated.
- **MEASURE** is the secondary owner: once identified, indirect injection is testable. The absence of a test for it is a measurement gap.

This split matters. If you file this incident purely under MEASURE ("we should have tested for it"), you'll add a test and miss the systemic issue — that the ingestion surface was never inventoried in the first place, so you don't know what *else* to test.

## Counterfactual control

**The one control:** treat model-influenced URLs as untrusted output and enforce an egress allowlist on any client-side fetch the model can shape — image sources included. If the rendered image host must be on an allowlist, the exfiltration path closes even when the injection succeeds.

Note this is a *blast-radius* control, not a prevention control. It assumes the injection lands and makes it not matter — which is the correct design posture for a class of attack nobody has reliably prevented.

## One line for a non-technical risk owner

*"A booby-trapped dashboard could quietly tell our AI assistant to mail our data to a stranger, and to the assistant it looked like a normal instruction."*

## Sources

- OWASP GenAI Security Project — Exploit Round-up Report Q1 2026: https://genai.owasp.org/2026/04/14/owasp-genai-exploit-round-up-report-q1-2026/
- MITRE ATLAS technique reference: https://atlas.mitre.org/techniques/AML.T0051
