# 03 — Vertex AI "Double Agent" privilege abuse

**Date:** disclosed 31 March – 1 April 2026
**Impact:** demonstrated credential extraction and access to restricted internal resources via default agent permission scoping in Google Cloud Vertex AI

---

## What happened

Researchers showed that a malicious agent deployed into Google Cloud Vertex AI could abuse the platform's **default permission scoping** to extract credentials and reach internal resources it should not have had access to. The core issue was not a code vulnerability in the usual sense — it was that the default identity an agent runs under carried more privilege than the agent's purpose required.

## Why this one matters

This is the incident that best illustrates why "Excessive Agency" is a *permissions* problem, not a *model behaviour* problem.

Every mitigation aimed at the model — better system prompts, refusal training, output filtering — is irrelevant here. The agent didn't need to be tricked. It simply used the access it was legitimately granted, and that access was too broad by default.

The phrase worth taking from this: **an agent's blast radius is defined by its IAM role, not by its instructions.** You can write the most carefully constrained system prompt in the world and it will not shrink the permission set the runtime handed the agent.

The second observation is about defaults specifically. This wasn't a misconfiguration by a careless user — it was the *platform default* being generous. Defaults are governance decisions made on behalf of everyone who doesn't change them, and most people don't change them.

## OWASP LLM Top 10 classes

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | **Primary.** The agent held permissions far exceeding its functional need. |
| **LLM03 — Supply Chain** | The attack presumes a malicious/compromised agent entering the environment — an agent-supply-chain precondition. |

## MITRE ATLAS technique chain

| Technique | Role in this incident |
|---|---|
| `AML.T0010.005` — AI Supply Chain Compromise: AI Agent Tool | The precondition: a malicious agent/tool is introduced into the environment. |
| `AML.T0053` — AI Agent Tool Invocation | **Primary.** The agent invokes tools and APIs available to it, reaching beyond intended scope. |
| `AML.T0055` — Unsecured Credentials | Credentials reachable from the agent's execution context are extracted. |
| `AML.T0012` — Valid Accounts | Extracted credentials are then used as legitimate access to internal resources. |

`AML.T0010.005` is a recent addition to the matrix and is exactly the sub-technique this incident needs — a good example of why mapping against the live matrix beats mapping against a cached crosswalk.

## NIST AI RMF — which function owned this

**GOVERN, then MANAGE.**

- **GOVERN** owns it primarily, and the reason is the word *default*. Deciding what privilege an agent runs with by default is a policy decision about acceptable risk, made once, inherited by every deployment afterwards. That is squarely GOVERN — accountability and policy — not a runtime concern.
- **MANAGE** is secondary: at runtime, the control that would have limited the damage (scoped, short-lived, least-privilege credentials per agent) is a MANAGE-layer response.

Note this is the mirror image of Incident 02. There, the risk was never *identified* (MAP). Here, the risk of over-broad permissions is extremely well understood in conventional security — it just wasn't *governed* for a new class of principal. The knowledge existed; the policy hadn't caught up to the new actor type.

## Counterfactual control

**The one control:** per-agent least-privilege identity with short-lived credentials — every agent gets its own scoped service identity provisioned for its specific task, not a shared or inherited default role, and credentials that expire on a timescale shorter than a useful attack.

Supporting control: deny agent execution contexts direct read access to credential stores. If the agent cannot read the credential, extracting it stops being an option regardless of what else it can invoke.

## One line for a non-technical risk owner

*"We gave every AI agent the same office master key by default, and one of them turned out to be working for someone else."*

## Sources

- OWASP GenAI Security Project — Exploit Round-up Report Q1 2026: https://genai.owasp.org/2026/04/14/owasp-genai-exploit-round-up-report-q1-2026/
- MITRE ATLAS technique reference: https://atlas.mitre.org/techniques/AML.T0053
