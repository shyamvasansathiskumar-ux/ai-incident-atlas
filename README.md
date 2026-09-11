# ai-incident-atlas

**Real AI security incidents, mapped across three frameworks that don't talk to each other.**

Shyam Kumar · CSE, Rajalakshmi Engineering College · started September 2026

---

## Where this is at

Five incidents mapped so far. I'm a second-year student learning this in public, so treat the mappings as carefully-researched but not authoritative — every technique ID is verified against the live ATLAS matrix, but the judgement calls (which RMF function "owns" a failure, which control would have stopped it) are arguments I'm making, not settled fact. Corrections welcome via issues.

## The problem this repo exists to solve

There are three serious frameworks for reasoning about AI risk, and they answer three different questions:

| Framework | The question it answers | Who reads it |
|---|---|---|
| **OWASP LLM Top 10** | *What class of thing went wrong?* | Engineers, AppSec |
| **MITRE ATLAS** | *What did the adversary actually do, step by step?* | Red teams, threat intel |
| **NIST AI RMF** | *Which organisational function should have caught it?* | Risk, compliance, leadership |

An incident report usually gets tagged with one of them. Almost never all three. So the engineer who knows it was "LLM01" can't tell the risk owner which governance function failed, and the risk owner writing the policy has no idea which concrete adversary technique the policy is supposed to stop.

This repo takes **publicly documented, dated, named AI security incidents** and maps each one across all three at once, then asks the question none of the frameworks ask on their own: *which control, if it had existed, would have actually stopped this?*

## Why this is worth doing now

In September 2026 the OWASP GenAI Security Project rebuilt its Top 10 rankings around real incident data (6,639 reported cases) instead of relying purely on expert vote. Incidents, not intuitions, driving what gets prioritised.

This repo applies the same idea at a scale one person can actually verify, and adds the two mappings OWASP's own crosswalk stops short of: the specific ATLAS technique chain, and the NIST RMF function that owns the failure.

## Incident index

| # | Incident | Date | OWASP class | Primary ATLAS technique | RMF function that owned it |
|---|---|---|---|---|---|
| 01 | [Mexican government breach, Claude-assisted](incidents/01-mexican-govt-ai-assisted-breach.md) | Dec 2025 – Feb 2026 | LLM02, LLM06 | `AML.T0016.002` Obtain Capabilities: Generative AI | GOVERN |
| 02 | [GrafanaGhost indirect prompt injection](incidents/02-grafanaghost-indirect-injection.md) | Apr 2026 | LLM01, LLM02 | `AML.T0051.001` LLM Prompt Injection: Indirect | MAP → MEASURE |
| 03 | [Vertex AI "Double Agent" privilege abuse](incidents/03-vertex-ai-double-agent.md) | Mar–Apr 2026 | LLM03, LLM06 | `AML.T0053` AI Agent Tool Invocation | GOVERN → MANAGE |
| 04 | [Mercor / LiteLLM supply chain breach](incidents/04-mercor-litellm-supply-chain.md) | Mar 2026 | LLM03, LLM04 | `AML.T0010.001` AI Supply Chain Compromise: AI Software | GOVERN → MAP |
| 05 | [OpenClaw autonomous inbox deletion](incidents/05-openclaw-inbox-deletion.md) | Feb 2026 | LLM05, LLM06 | *(no clean ATLAS mapping, see finding)* | MANAGE |

## The finding that came out of doing this

Mapping five incidents surfaced a structural gap worth more than any individual mapping:

**MITRE ATLAS is an adversary framework. A growing share of real AI incidents have no adversary.**

Incident 05 is the clearest case. An agent ignored stop commands and deleted a live inbox. Nobody attacked it. There is no adversary technique to map, because there was no adversary. ATLAS models *what an attacker does*; it has no vocabulary for *what an authorised system does wrong on its own*.

This matters practically: if an organisation runs its AI threat modelling purely off ATLAS, the entire category of autonomous-action failures is invisible to the exercise, and that category is growing fastest as agents get real tool access. The OWASP Top 10 covers it (LLM06) and NIST RMF covers it (MANAGE), but the technique-level framework that red teams actually plan against does not.

Full write-up: [analysis/control-gap-findings.md](analysis/control-gap-findings.md)

**Open question I haven't resolved:** whether this is a gap ATLAS *should* close, or whether adversary-modelling and safety-failure-modelling are genuinely different exercises that deserve separate frameworks. I lean toward the second but haven't found much written either way.

## Method

Selection, mapping, and scoring rules are documented in [METHODOLOGY.md](METHODOLOGY.md), including what does *not* qualify as an incident here, so the index can't quietly drift into a list of vendor blog posts.

Every incident in this repo is publicly reported and cited. Nothing here is hypothetical, and nothing is a scenario I invented to make a point.

## Related repos

- [`redteam-log`](https://github.com/shyamvasansathiskumar-ux/redteam-log) — the hands-on side: environment setup, scan-logging templates, and the OWASP→ATLAS class mapping (LLM01–03 done) that this repo applies to real cases. Toolchain is still being stood up; no scan output in there yet.
- [`ai-governance-portfolio`](https://github.com/shyamvasansathiskumar-ux/ai-governance-portfolio) — the written side: NIST AI RMF primer and reusable risk-assessment / policy templates.
- [`cybersecurity-fundamentals`](https://github.com/shyamvasansathiskumar-ux/cybersecurity-fundamentals) — the base layer underneath both. Two worked entries so far: subnetting, and an AES ECB-vs-CBC demonstration.

## Status

| Incident | Mapped | Controls analysed | Re-checked against live ATLAS |
|---|---|---|---|
| 01 Mexican govt breach | ✅ | ✅ | ☐ |
| 02 GrafanaGhost | ✅ | ✅ | ☐ |
| 03 Vertex AI Double Agent | ✅ | ✅ | ☐ |
| 04 Mercor / LiteLLM | ✅ | ✅ | ☐ |
| 05 OpenClaw inbox deletion | ✅ | ✅ | ☐ |

Adding incidents as I find ones with enough public technical detail to map properly. Depth over volume: a shallow index of fifty incidents would be worth less than five done properly.

## A note on tooling

I use an LLM as a research and drafting assistant on this repo, the same way I'd use a search engine and a spellchecker. The framework mappings, the judgement calls, and the finding above are mine, and I verify every technique ID against [atlas.mitre.org](https://atlas.mitre.org/) directly rather than trusting a secondary source. Flagging it because I'd rather be upfront than have someone guess.
