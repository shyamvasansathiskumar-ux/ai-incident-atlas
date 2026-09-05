# ai-incident-atlas

**Real AI security incidents, mapped across three frameworks that don't talk to each other.**

Shyam Kumar · CSE, Rajalakshmi Engineering College · started September 2026

---

## The problem this repo exists to solve

There are three serious frameworks for reasoning about AI risk, and they answer three different questions:

| Framework | The question it answers | Who reads it |
|---|---|---|
| **OWASP LLM Top 10** | *What class of thing went wrong?* | Engineers, AppSec |
| **MITRE ATLAS** | *What did the adversary actually do, step by step?* | Red teams, threat intel |
| **NIST AI RMF** | *Which organisational function should have caught it?* | Risk, compliance, leadership |

An incident report usually gets tagged with one of them. Almost never all three. So the engineer who knows it was "LLM01" can't tell the risk owner which governance function failed, and the risk owner writing the policy has no idea which concrete adversary technique the policy is supposed to stop.

This repo takes **publicly documented, dated, named AI security incidents** and maps each one across all three at once — then asks the question none of the frameworks ask on their own: *which control, if it had existed, would have actually stopped this?*

## Why this is worth doing now

In September 2026, the OWASP GenAI Security Project rebuilt its Top 10 rankings around real incident data — 6,639 reported cases — instead of relying purely on expert vote. That shift is the right instinct: incidents, not intuitions, should drive what we prioritise.

This repo applies the same instinct at a scale one person can actually verify, and adds the two mappings OWASP's own crosswalk stops short of: the specific ATLAS technique chain, and the NIST RMF function that owns the failure.

## Incident index

| # | Incident | Date | OWASP class | Primary ATLAS technique | RMF function that owned it |
|---|---|---|---|---|---|
| 01 | [Mexican government breach, Claude-assisted](incidents/01-mexican-govt-ai-assisted-breach.md) | Dec 2025 – Feb 2026 | LLM02, LLM06 | `AML.T0016.002` Obtain Capabilities: Generative AI | GOVERN |
| 02 | [GrafanaGhost indirect prompt injection](incidents/02-grafanaghost-indirect-injection.md) | Apr 2026 | LLM01, LLM02 | `AML.T0051.001` LLM Prompt Injection: Indirect | MAP → MEASURE |
| 03 | [Vertex AI "Double Agent" privilege abuse](incidents/03-vertex-ai-double-agent.md) | Mar–Apr 2026 | LLM03, LLM06 | `AML.T0053` AI Agent Tool Invocation | GOVERN → MANAGE |
| 04 | [Mercor / LiteLLM supply chain breach](incidents/04-mercor-litellm-supply-chain.md) | Mar 2026 | LLM03, LLM04 | `AML.T0010.001` AI Supply Chain Compromise: AI Software | GOVERN → MAP |
| 05 | [OpenClaw autonomous inbox deletion](incidents/05-openclaw-inbox-deletion.md) | Feb 2026 | LLM05, LLM06 | *(no clean ATLAS mapping — see finding)* | MANAGE |

## The finding that came out of doing this

Mapping five incidents surfaced a structural gap worth more than any individual mapping:

**MITRE ATLAS is an adversary framework. A growing share of real AI incidents have no adversary.**

Incident 05 is the clearest case — an agent ignored stop commands and deleted a live inbox. Nobody attacked it. There is no adversary technique to map, because there was no adversary. ATLAS models *what an attacker does*; it has no vocabulary for *what an authorised system does wrong on its own*.

This matters practically: if an organisation runs its AI threat modelling purely off ATLAS, the entire category of autonomous-action failures is invisible to the exercise — and that category is growing fastest as agents get real tool access. The OWASP Top 10 covers it (LLM06), and NIST RMF covers it (MANAGE), but the technique-level framework that red teams actually plan against does not.

Full write-up: [analysis/control-gap-findings.md](analysis/control-gap-findings.md)

## Method

Selection, mapping, and scoring rules are documented in [METHODOLOGY.md](METHODOLOGY.md) — including what does *not* qualify as an incident here, so the index can't quietly drift into a list of vendor blog posts.

Every incident in this repo is publicly reported and cited. Nothing here is hypothetical, and nothing is a scenario I invented to make a point.

## Related repos

- [`redteam-log`](https://github.com/shyamvasansathiskumar-ux/redteam-log) — the hands-on side: garak/PyRIT scans, CTF attempts, and the OWASP→ATLAS class mapping this repo applies to real cases
- [`ai-governance-portfolio`](https://github.com/shyamvasansathiskumar-ux/ai-governance-portfolio) — the written side: risk assessments and policy artifacts against the NIST AI RMF
- [`cybersecurity-fundamentals`](https://github.com/shyamvasansathiskumar-ux/cybersecurity-fundamentals) — the base layer underneath both

## Status

| Incident | Mapped | Controls analysed | Reviewed |
|---|---|---|---|
| 01 Mexican govt breach | ✅ | ✅ | ☐ |
| 02 GrafanaGhost | ✅ | ✅ | ☐ |
| 03 Vertex AI Double Agent | ✅ | ✅ | ☐ |
| 04 Mercor / LiteLLM | ✅ | ✅ | ☐ |
| 05 OpenClaw inbox deletion | ✅ | ✅ | ☐ |

Target: one new incident mapped per week. Depth over volume — a shallow index of fifty incidents would be worth less than five done properly.
