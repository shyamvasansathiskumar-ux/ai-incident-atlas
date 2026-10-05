# ai-incident-atlas

**Real AI security incidents, mapped across three frameworks that don't talk to each other.**

Shyam Kumar · CSE, Rajalakshmi Engineering College · started September 2026

---

## Where this is at

Ten incidents mapped so far. Six of them have no attacker at all, which turned out to be the most interesting thing in the repo (see below). I'm a second-year student learning this in public, so treat the mappings as carefully researched but not authoritative. Every technique ID is checked against the ATLAS data (v5.6.0), but the judgement calls, such as which RMF function "owns" a failure or which control would have stopped it, are arguments I'm making, not settled fact. Corrections are welcome via issues.

## The problem this repo exists to solve

There are three serious frameworks for reasoning about AI risk, and they answer three different questions:

| Framework | The question it answers | Who reads it |
|---|---|---|
| **OWASP LLM Top 10** | *What class of thing went wrong?* | Engineers, AppSec |
| **MITRE ATLAS** | *What did the adversary actually do, step by step?* | Red teams, threat intel |
| **NIST AI RMF** | *Which organisational function should have caught it?* | Risk, compliance, leadership |

An incident report usually gets tagged with one of them, almost never all three. So the engineer who knows it was "LLM01" can't tell the risk owner which governance function failed, and the risk owner writing the policy has no idea which concrete adversary technique the policy is supposed to stop.

This repo takes **publicly documented, dated, named AI incidents** and maps each one across all three at once. Then it asks the question none of the frameworks ask on their own: *which control, if it had existed, would actually have stopped this?*

## Why this is worth doing now

On 6 August 2026 the OWASP GenAI Security Project published its 2026 LLM Top 10. For the first time the ranking used real incident data: 25% of the weighting came from 6,639 reported incidents and 75% from expert consensus. Incidents, not intuitions, are starting to drive what gets prioritised.

This repo applies the same idea at a scale one person can actually verify. It adds the two mappings OWASP's own crosswalk stops short of: the specific ATLAS technique chain, and the NIST RMF function that owns the failure.

## Incident index

| # | Incident | Date | OWASP class | Primary ATLAS technique | RMF function that owned it |
|---|---|---|---|---|---|
| 01 | [Mexican government breach, Claude-assisted](incidents/01-mexican-govt-ai-assisted-breach.md) | Dec 2025 – Feb 2026 | LLM02, LLM06 | `AML.T0016.002` Obtain Capabilities: Generative AI | GOVERN |
| 02 | [GrafanaGhost indirect prompt injection](incidents/02-grafanaghost-indirect-injection.md) | Apr 2026 | LLM01, LLM02 | `AML.T0051.001` LLM Prompt Injection: Indirect | MAP → MEASURE |
| 03 | [Vertex AI "Double Agent" privilege abuse](incidents/03-vertex-ai-double-agent.md) | Mar–Apr 2026 | LLM03, LLM06 | `AML.T0053` AI Agent Tool Invocation | GOVERN → MANAGE |
| 04 | [Mercor / LiteLLM supply chain breach](incidents/04-mercor-litellm-supply-chain.md) | Mar 2026 | LLM03, LLM04 | `AML.T0010.001` AI Supply Chain Compromise: AI Software | GOVERN → MAP |
| 05 | [OpenClaw autonomous inbox deletion](incidents/05-openclaw-inbox-deletion.md) | Feb 2026 | LLM05, LLM06 | *none: no adversary* | MANAGE |
| 06 | [Replit production database deleted during a code freeze](incidents/06-replit-production-database-deletion.md) | Jul 2025 | LLM06, LLM09 | *none: no adversary* | GOVERN → MANAGE |
| 07 | [Gemini CLI overwrites files after a failed command](incidents/07-gemini-cli-file-overwrite.md) | Jul 2025 | LLM06, LLM05 | *none: no adversary* | MEASURE |
| 08 | [Google Antigravity wipes a drive while clearing a cache](incidents/08-antigravity-drive-wipe.md) | Nov 2025 | LLM06, LLM05 | *none: no adversary* | MAP → MANAGE |
| 09 | [Claude Code deletes a home directory](incidents/09-claude-code-home-directory-deletion.md) | Dec 2025 | LLM05, LLM06 | *none: no adversary* | MANAGE |
| 10 | [Cursor agent deletes PocketOS production data and backups](incidents/10-pocketos-cursor-railway-deletion.md) | Apr 2026 | LLM06 | *none: no adversary* | GOVERN |

## The finding that came out of doing this

**MITRE ATLAS is an adversary framework. A growing share of real AI incidents have no adversary.**

It started with one case (05: an agent ignored stop commands and deleted a live inbox), so I went looking for more. Five more turned up from the last fifteen months: six products from five vendors, all destroying data or breaking an explicit human constraint, with nobody attacking anything.

Meanwhile ATLAS added `AML.T0101` *Data Destruction via AI Agent Tool Invocation* on 25 November 2025. It describes the outcome of all six incidents, and like every agent technique in the matrix it begins "Adversaries may...". The framework now has a word for the effect and still none for the cause when the cause is the agent.

The six cases sort into five distinct failure mechanisms: scope widening, unverified preconditions, constraint override, credential overreach and state misreporting. Every control that would have stopped them sits outside the model.

- Full analysis, with five proposed checklist entries (NA-01 to NA-05) to run alongside ATLAS: [analysis/no-adversary-failures.md](analysis/no-adversary-failures.md)
- The original four findings: [analysis/control-gap-findings.md](analysis/control-gap-findings.md)
- A NIST AI RMF assessment that applies this to one deployment pattern: [ai-governance-portfolio](https://github.com/shyamvasansathiskumar-ux/ai-governance-portfolio/blob/main/analyses/2026-10-ai-coding-agent-production-access.md)

## Method

Selection, mapping and scoring rules are documented in [METHODOLOGY.md](METHODOLOGY.md), including what does *not* qualify as an incident here, so the index can't quietly drift into a list of vendor blog posts.

Every incident in this repo is publicly reported and cited. Nothing here is hypothetical, and nothing is a scenario I invented to make a point.

## Related repos

- [`redteam-log`](https://github.com/shyamvasansathiskumar-ux/redteam-log): the hands-on side, with environment setup, scan-logging templates and the OWASP→ATLAS class mapping (all ten 2025 classes) that this repo applies to real cases.
- [`ai-governance-portfolio`](https://github.com/shyamvasansathiskumar-ux/ai-governance-portfolio): the written side, with a NIST AI RMF primer, reusable templates and the first full assessment.
- [`cybersecurity-fundamentals`](https://github.com/shyamvasansathiskumar-ux/cybersecurity-fundamentals): the base layer underneath both.
- [`kaappu`](https://github.com/shyamvasansathiskumar-ux/kaappu): a TOTP authenticator built from the RFCs, with a written threat model.

## Status

| Incident | Mapped | Controls analysed | Checked against ATLAS v5.6.0 |
|---|---|---|---|
| 01 Mexican govt breach | ✅ | ✅ | ☐ |
| 02 GrafanaGhost | ✅ | ✅ | ☐ |
| 03 Vertex AI Double Agent | ✅ | ✅ | ☐ |
| 04 Mercor / LiteLLM | ✅ | ✅ | ☐ |
| 05 OpenClaw inbox deletion | ✅ | ✅ | ✅ |
| 06 Replit | ✅ | ✅ | ✅ |
| 07 Gemini CLI | ✅ | ✅ | ✅ |
| 08 Google Antigravity | ✅ | ✅ | ✅ |
| 09 Claude Code | ✅ | ✅ | ✅ |
| 10 Cursor / PocketOS | ✅ | ✅ | ✅ |

I add incidents when I find ones with enough public technical detail to map properly. Depth over volume: a shallow index of fifty incidents would be worth less than ten done properly.

## A note on tooling

I use an LLM as a research and drafting assistant on this repo, the same way I'd use a search engine and a spellchecker. The framework mappings, the judgement calls and the findings above are mine, and I verify every technique ID against the ATLAS data directly rather than trusting a secondary source. I'm flagging it because I'd rather be upfront than have someone guess.
