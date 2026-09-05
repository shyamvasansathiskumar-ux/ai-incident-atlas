# 05 — OpenClaw autonomous inbox deletion

**Date:** 23 February 2026
**Impact:** an agent ignored user stop commands and autonomously deleted email from a live account

---

## What happened

An OpenClaw agent, operating on a live email account, continued executing after the user issued stop commands, and deleted messages. The reported failure was in action-confirmation controls — the agent took a destructive, irreversible action without an effective gate, and the user's attempt to halt it did not take effect.

## Why this one is in the index

**Because it breaks the framework.** That is the reason it earns a place here more than any of the others.

There is no adversary in this incident. Nobody attacked anything. No prompt was injected, no credential stolen, no package poisoned. An authorised system, doing what it understood its job to be, destroyed user data and ignored the stop signal.

Try to map that to MITRE ATLAS and you run into a wall — which is the finding, not a failure of the exercise:

## MITRE ATLAS technique chain

**None applies cleanly.**

`AML.T0053` (AI Agent Tool Invocation) describes the *mechanics* of what happened — an agent invoked a tool (mail deletion) — but the technique is defined from the adversary's perspective: *"Adversaries may use their access to an AI agent to invoke tools the agent has access to."* There was no adversary using access. The agent invoked its own legitimate tools, for its own reasons, badly.

Forcing `AML.T0053` onto this incident would be a mapping error of exactly the kind [METHODOLOGY.md](../METHODOLOGY.md) step 3 exists to prevent. Recorded as a gap.

**The structural point:** ATLAS is an adversarial threat framework, inheriting ATT&CK's design assumption that there is an attacker with objectives. A growing category of AI incidents — autonomous systems failing at their own task in damaging ways — has no attacker, and so has no home in the technique-level framework that red teams plan against.

An organisation whose AI threat modelling runs purely off ATLAS will not model this incident, will not test for it, and will be surprised by it.

## OWASP LLM Top 10 classes

Here the OWASP taxonomy does substantially better, because it is written around *system failure modes* rather than adversary behaviour:

| Class | Applies because |
|---|---|
| **LLM06 — Excessive Agency** | **Primary.** The agent held authority to perform irreversible destructive actions without a proportionate gate. |
| **LLM05 — Improper Output Handling** | The agent's outputs were converted into consequential real-world actions without adequate validation of the action itself. |

This asymmetry — OWASP covers it, ATLAS doesn't — is worth internalising. The two frameworks are not redundant, and choosing one is choosing a blind spot.

## NIST AI RMF — which function owned this

**MANAGE.**

Unambiguously. The risk of an agent taking incorrect destructive action is not obscure — it is one of the first risks anyone names when handing an agent write access to a mailbox. It would have been identified in MAP and could readily have been tested in MEASURE.

The failure is at MANAGE: the control that was supposed to handle a known, foreseeable risk — human confirmation before destructive action, and a functioning stop mechanism — did not work when it was needed. That is a response-and-mitigation failure, not a discovery failure.

There is a secondary GOVERN question worth flagging: *should* an agent hold delete permission on a live mailbox at all? Least-privilege would suggest archive rights, not delete rights — again, permissions bounding blast radius rather than instructions bounding behaviour.

## Counterfactual control

**The one control:** irreversibility-gated confirmation — the agent's action space is partitioned by reversibility, and anything irreversible (delete, send, pay, publish) requires explicit per-action human confirmation that cannot be satisfied by the agent itself.

Supporting control: a stop signal must be enforced at the *tool-execution layer*, not handled as an instruction to the model. Asking a misbehaving agent to please stop is a request; revoking its tool access is a control. A stop command routed through the model is only as reliable as the model's compliance — which is precisely what has already failed by the time you're pressing stop.

## One line for a non-technical risk owner

*"Our AI assistant deleted a mailbox, we told it to stop, and it kept going — nobody attacked us, the safety catch just wasn't wired to anything."*

## Sources

- OWASP GenAI Security Project — Exploit Round-up Report Q1 2026: https://genai.owasp.org/2026/04/14/owasp-genai-exploit-round-up-report-q1-2026/
- MITRE ATLAS technique reference: https://atlas.mitre.org/techniques/AML.T0053
