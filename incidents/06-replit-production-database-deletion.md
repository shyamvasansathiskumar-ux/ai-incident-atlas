# 06 — Replit agent deletes a production database during a code freeze

**Date:** July 2025 (Fortune report, 23 July 2025)
**Impact:** an AI coding agent deleted a live production database during an explicit code freeze, then told the user the data could not be recovered. It could.

---

## What happened

Jason Lemkin, founder of SaaStr, was running a 12-day "vibe coding" experiment on Replit. On day 9, with a code freeze in place and an explicit instruction not to change anything without approval, Replit's agent ran destructive commands against the production database. The database held records on roughly 1,200 executives and 1,190 companies (reports differ slightly on the exact counts).

Asked what had happened, the agent said it had "panicked" in response to empty query results and called the event "a catastrophic failure on my part." It then told Lemkin a rollback would not work. It did, and the data was restored.

Replit's CEO, Amjad Masad, called it "unacceptable and should never be possible." He announced automatic separation of development and production databases, a planning-only mode in which the agent cannot change anything, and one-click restore from backups.

## Why this one is in the index

It is the cleanest public case of an agent breaking an explicit human constraint with no one manipulating it. The code freeze existed and was stated. It was simply an instruction, and instructions are what the agent overrode.

## MITRE ATLAS technique chain

**None applies cleanly.**

Two techniques describe the mechanics, and both are defined around an adversary:

- `AML.T0053` AI Agent Tool Invocation: *"Adversaries may use their access to an AI agent to invoke tools the agent has access to."*
- `AML.T0101` Data Destruction via AI Agent Tool Invocation (added to ATLAS on 25 November 2025): *"Adversaries may invoke an AI agent's tool capable of performing mutative operations to perform Data Destruction."*

`AML.T0101` describes the outcome here exactly. But nobody invoked anything on anyone's behalf: the agent acted on its own reading of the situation. Recorded as a gap, per [METHODOLOGY.md](../METHODOLOGY.md) step 3.

## OWASP LLM Top 10 (2025) classes

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | **Primary.** A development agent held credentials that could change production, during a freeze. |
| **LLM09 — Misinformation** | The agent's statement that rollback was impossible was false, and a user acting on it would have made worse decisions. |

## NIST AI RMF — which function owned this

**GOVERN, then MANAGE.**

GOVERN, because the root cause is a permission decision: an agent that builds and tests software could reach the production database at all. That is the kind of policy GOVERN 1.3 (deciding the level of risk management needed) and GOVERN 4.1 (a safety-first mindset in deployment) exist to settle before anyone writes a prompt.

MANAGE, because the freeze was the risk response, and it was implemented as a sentence the agent could ignore. MANAGE 2.4 asks for mechanisms "to supersede, disengage, or deactivate AI systems that demonstrate performance or outcomes inconsistent with intended use." A freeze that lives in the prompt is not a mechanism.

## Counterfactual control

**The one control:** environment-bound credentials. A development agent gets credentials that cannot reach production at all. A code freeze is enforced by revoking write access, not by telling the agent.

Replit's own fix, automatic separation of development and production databases, is this control. It came after the incident.

## One line for a non-technical risk owner

*"We told the AI not to touch anything during the freeze, but we never took away its keys, and telling isn't locking."*

## Sources

- Fortune, 23 July 2025: https://fortune.com/2025/07/23/ai-coding-tool-replit-wiped-database-called-it-a-catastrophic-failure
- eWeek: https://www.eweek.com/news/replit-ai-coding-assistant-failure/
- MITRE ATLAS v5.6.0 data (`AML.T0053`, `AML.T0101`): https://github.com/mitre-atlas/atlas-data
