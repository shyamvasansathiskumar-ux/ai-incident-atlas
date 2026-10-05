# 10 — Cursor agent deletes PocketOS production database and its backups

**Date:** 24 April 2026 (AI Incident Database #1469)
**Impact:** production database and backup volumes deleted; downstream car-rental businesses disrupted

---

## What happened

PocketOS runs reservation and payment software for car-rental businesses. A Cursor coding agent, reportedly running Claude Opus 4.6, was working on PocketOS's **staging** environment. Through the hosting platform Railway, it deleted the **production** database and the backup volumes with it. The agent had access to a broadly scoped Railway API token that allowed both. The car-rental businesses using PocketOS lost their reservation and payment systems until Railway helped recover the data, and Railway then added safeguards.

## Why this one is in the index

It is incident [06](06-replit-production-database-deletion.md) with the stakes raised: the agent's work was on staging, the damage was in production, and **the backups were inside the same blast radius.** One credential could destroy both the data and the means of restoring it. This is also the first case in the index where people outside the deploying company (the rental businesses) were the ones harmed.

## MITRE ATLAS technique chain

**None applies.** `AML.T0101` describes the outcome. There is no adversary. The AIID entry's own wording, "systemic failures" across providers and deployment safeguards, describes a governance failure, not an attack.

## OWASP LLM Top 10 (2025) classes

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | **Primary.** A token issued for staging work could delete production and the backups. |

## NIST AI RMF — which function owned this

**GOVERN.**

GOVERN 6.1 asks for "policies and procedures ... that address AI risks associated with third-party entities." Here there are three: Cursor (the agent), Anthropic (the model) and Railway (the platform holding the data). The token's scope and the backups' location are decisions about how those third parties connect, and nobody owned them. MAP 4.1 applies too: the third-party components' risks were never mapped against what the token could reach.

## Counterfactual control

**The one control:** backups the agent's credentials cannot touch. Immutable or off-account copies are the difference between an outage and a loss. Even with a perfectly scoped token, the backup policy should assume the token will one day be used wrongly.

Supporting control: credentials bound to one environment. A staging token cannot name a production resource.

## One line for a non-technical risk owner

*"The AI was working on our test system, but its key opened production and the backups, so one mistake took out both the data and our way back."*

## Sources

- AI Incident Database, Incident 1469: https://incidentdatabase.ai/cite/1469
