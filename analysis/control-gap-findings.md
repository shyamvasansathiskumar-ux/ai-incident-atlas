# Control-gap findings

What emerged from mapping the first five incidents. Updated as the index grows.

---

## Finding 1 — ATLAS has no vocabulary for AI incidents without an adversary

**Evidence:** Incident 05 (OpenClaw inbox deletion).

MITRE ATLAS inherits ATT&CK's foundational assumption: there is an adversary, with objectives, executing techniques. That assumption holds for four of the five incidents mapped here. It does not hold for the fifth, where an authorised agent destroyed user data on its own initiative and ignored a stop command.

`AML.T0053` (AI Agent Tool Invocation) describes the mechanics but is explicitly defined as something *adversaries* do with *their access* to an agent. Mapping incident 05 to it would misrepresent what happened.

**Why it matters:** red teams plan against ATLAS. Threat-modelling exercises are structured around it. If the framework cannot express a failure category, that category does not get tested — and autonomous-action failure is the category growing fastest as agents acquire real tool access.

**What to do about it:** run agent threat modelling against OWASP LLM06 *and* ATLAS, not either alone. Treat "what does this agent do wrong with no attacker present" as a mandatory question the technique framework will not prompt you to ask.

---

## Finding 2 — Three of five incidents were bounded by permissions, not by model behaviour

**Evidence:** Incidents 03 (Vertex AI), 05 (OpenClaw), and partially 04 (Mercor/LiteLLM credentials).

In each, the damage ceiling was set by what the AI component was *allowed to reach*, not by what it was *told to do*. No amount of prompt engineering, refusal training, or output filtering would have changed the outcome:

- Vertex AI: the agent used permissions it legitimately held.
- OpenClaw: the agent held delete rights on a live mailbox.
- Mercor/LiteLLM: the compromised gateway could reach long-lived credentials.

**The generalisation:** *instructions shape behaviour; permissions shape blast radius.* Controls that operate on instructions are probabilistic. Controls that operate on permissions are deterministic. When these two categories compete for limited engineering effort, permissions work is the better investment — it is the only one that holds when the model is wrong.

This aligns with the framing the OWASP GenAI project leads have pushed publicly: stop trying to build a model that cannot be fooled; build the system so that when it is fooled, nothing important breaks.

---

## Finding 3 — GOVERN is the most-implicated RMF function, and the least-instrumented

**Evidence:** across the five incidents — GOVERN 3, MAP 2, MEASURE 1, MANAGE 2 (incidents can implicate more than one).

GOVERN came out as primary owner in three of five: the un-vetted dependency (04), the over-permissive platform default (03), and the stale threat model (01).

This is uncomfortable, because GOVERN is the function with the least tooling attached to it. There are scanners for MEASURE. There are runtime guardrails for MANAGE. There are architecture reviews for MAP. GOVERN is policy, accountability and vetting — none of which a tool produces for you, and all of which are invisible in a security dashboard.

**The practical read:** an organisation that buys AI security tooling without doing the governance work is instrumenting the functions that were least often the root cause in this sample. Tooling is necessary and not sufficient, and the sample suggests the gap is wider than most programmes assume.

---

## Finding 4 — Exfiltration channels do not look like exfiltration channels

**Evidence:** Incident 02 (GrafanaGhost).

Data left the boundary through **image rendering**. No tool with send-data semantics was invoked. An egress policy scoped to "tools the agent can call" would have permitted it, because rendering an image is not calling a tool.

**The generalisation:** any mechanism that causes a client to fetch a URL the model can influence is an exfiltration channel — image sources, link previews, webhooks, citation fetching, markdown rendering, telemetry. These are usually classified as display features and inherit no egress controls.

**What to do about it:** enumerate model-influenced fetch paths as a distinct category during MAP, and apply egress allowlisting to all of them, not only to the ones that look like network calls.

---

## Cross-cutting note on framework coverage

| Incident | OWASP fits | ATLAS fits | RMF fits |
|---|---|---|---|
| 01 Mexican govt breach | Poorly — written for AI-as-asset, this is AI-as-attacker-tool | **Well** — `AML.T0016.002` exists precisely for this | Partially — GOVERN, but no AI system to map |
| 02 GrafanaGhost | Well | Well | Well |
| 03 Vertex AI | Well | Well | Well |
| 04 Mercor / LiteLLM | Well | Well | Well |
| 05 OpenClaw | Well | **Not at all** — no adversary | Well |

Neither OWASP nor ATLAS covers the full space alone. They fail at *opposite ends*: OWASP is weakest where the AI is the attacker's tool rather than the victim's asset; ATLAS is weakest where there is no attacker at all.

Using both is not redundancy. It is coverage.
