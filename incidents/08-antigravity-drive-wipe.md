# 08 — Google Antigravity wipes a whole drive while clearing a cache

**Date:** 27 November 2025 (AI Incident Database #1433)
**Impact:** an entire D: drive partition deleted; recovery largely failed

---

## What happened

A developer asked Google's Antigravity agent IDE to clear a project's cache folder. The agent executed `rmdir /s /q d:\`, which deletes the root of the D: drive, quietly and recursively, without asking. The intended target was one folder inside the project. Most of the data could not be recovered. Google said it was aware and investigating.

## Why this one is in the index

**Scope widening.** The task was narrow and legitimate. The command was the right command with the wrong path. The difference between `d:\project\.cache` and `d:\` is a few characters of model output, and nothing between the model and the shell checked them.

There is also a timing detail worth recording. MITRE added `AML.T0101` Data Destruction via AI Agent Tool Invocation to ATLAS on 25 November 2025, two days before this incident. It describes exactly what happened to this drive, and it opens with *"Adversaries may invoke..."*. The framework gained a word for this outcome in the same week the outcome happened without an adversary.

## MITRE ATLAS technique chain

**None applies.** `AML.T0101` matches the effect and not the cause. See the [no-adversary analysis](../analysis/no-adversary-failures.md).

## OWASP LLM Top 10 (2025) classes

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | **Primary.** The agent could delete anything on the machine, when the task needed delete rights over one folder. |
| **LLM05 — Improper Output Handling** | A model-written path went straight into a destructive shell command. |

## NIST AI RMF — which function owned this

**MAP, then MANAGE.**

MAP 1.1 asks that intended purposes and settings be understood and documented. "Clear a project cache" implies a boundary, the project directory, which the tool never enforced. Then MANAGE: there was no mechanism between model output and execution to catch a destructive command outside that boundary.

## Counterfactual control

**The one control:** workspace confinement in the tool layer. Destructive file operations are only executable inside the project root, checked on the resolved absolute path after expansion, so `..`, `~` and drive roots can't slip through. Anything outside the root needs a human, every time.

## One line for a non-technical risk owner

*"We asked it to empty one folder and nothing stopped it emptying the whole drive, because the folder boundary only existed in our heads."*

## Sources

- AI Incident Database, Incident 1433: https://incidentdatabase.ai/cite/1433
- MITRE ATLAS v5.6.0 data, `AML.T0101` (created 2025-11-25): https://github.com/mitre-atlas/atlas-data
