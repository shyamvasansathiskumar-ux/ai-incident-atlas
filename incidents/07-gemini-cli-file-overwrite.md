# 07 — Gemini CLI overwrites a user's files after a failed command

**Date:** 21 July 2025 (AI Incident Database #1178)
**Impact:** all but one file in a user's working directory destroyed

---

## What happened

Anuraag Gupta, a product manager, asked Google's Gemini CLI to reorganise some files. The agent tried to create a new directory. The command failed, but the agent carried on as if the directory existed and issued a series of move commands into it. With no directory there, each move overwrote the previous file. Everything except the last file was lost, and recovery attempts failed. The agent's own summary called it an "unacceptable, irreversible failure."

## Why this one is in the index

It isolates a single failure mechanism with no noise around it: **the agent did not check whether its previous step had worked.** No ambiguous instruction, no permission question, no attacker. One unverified precondition, then a chain of destructive actions built on it.

## MITRE ATLAS technique chain

**None applies.** As with [05](05-openclaw-inbox-deletion.md) and [06](06-replit-production-database-deletion.md), the closest techniques, `AML.T0053` and `AML.T0101`, are defined as things an adversary does. Here there is not even a misread instruction to point at: the agent was given a reasonable task and executed it on a false belief about the state of the file system.

## OWASP LLM Top 10 (2025) classes

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | **Primary.** The agent ran a chain of mutating commands with no check between them. |
| **LLM05 — Improper Output Handling** | Model-generated shell commands went to the operating system with no validation that the target existed. |

## NIST AI RMF — which function owned this

**MEASURE.**

Whether an agent verifies the result of its own actions is testable before release: give it a failing step and see whether it proceeds. MEASURE 2.6 asks that the system be "demonstrated to be safe" and able to "fail safely, particularly if made to operate beyond its knowledge limits." A failed `mkdir` is about the most basic out-of-plan condition there is.

## Counterfactual control

**The one control:** post-condition checks in the tool layer. Every mutating tool call returns an explicit success or failure that the harness, not the model, enforces: if the precondition for the next step is false, the chain stops. A move into a directory that doesn't exist is refused by the tool, whatever the model believes.

Supporting control: show a dry-run plan before any batch of file operations, and require confirmation when the plan overwrites anything.

## One line for a non-technical risk owner

*"The AI's first step failed and it didn't notice, so every step after that destroyed something."*

## Sources

- AI Incident Database, Incident 1178: https://incidentdatabase.ai/cite/1178
- OECD AI Incidents Monitor entry: https://oecd.ai/en/incidents/2025-07-25-03b9
