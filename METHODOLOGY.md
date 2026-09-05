# Methodology

How incidents get into this repo, how they get mapped, and what deliberately stays out.

## 1. What qualifies as an incident

An entry must meet all four:

1. **Publicly reported** by a named source — a vendor advisory, a CVE, a research disclosure, or credible reporting. Not a rumour, not a private anecdote.
2. **Dated**, at least to the month. Undated claims can't be tracked against a timeline of what the field knew when.
3. **Involves an AI/ML system materially** — either as the thing attacked, the thing that failed, or the thing the attacker used. A conventional breach at an AI company is not an AI incident.
4. **Has enough public technical detail** to map to a specific technique. "Company X had an AI problem" is not mappable and does not go in.

### What stays out

- Vendor marketing framed as research, where the "incident" is a demo of the vendor's own product catching something.
- Purely hypothetical attacks with no observed instance — those belong in `redteam-log` as probe designs, not here as incidents.
- Model *capability* controversies (a model said something offensive) unless there is a concrete security or safety failure with an identifiable control gap.

## 2. The mapping process

Each incident is mapped in a fixed order, because the order prevents motivated reasoning:

**Step 1 — Narrative first.** Write what happened in plain language, from the public sources, before looking at any framework. If the narrative can't be written clearly, the public detail is too thin to map.

**Step 2 — OWASP class.** Which LLM Top 10 class(es) does the failure fall into? Multiple classes are normal and expected; single-class incidents are rarer than the taxonomy implies.

**Step 3 — ATLAS technique chain.** Which techniques did the adversary actually execute, in sequence? Verified against the live matrix at [atlas.mitre.org](https://atlas.mitre.org/), not from a secondary crosswalk — published crosswalks go stale (several still list four sub-techniques under `AML.T0010` when the live matrix has six).

If no technique fits, that is recorded as a finding, **not** forced into the nearest approximate match. A bad mapping is worse than an acknowledged gap.

**Step 4 — NIST AI RMF function.** Which function *owned* the failure — not which function mentions the topic, but which one, if performed properly, would have prevented or caught this specific incident:

- **GOVERN** — the failure was a missing policy, unclear accountability, or an un-vetted third party entering the pipeline.
- **MAP** — the risk was never identified as applying to this system in this context.
- **MEASURE** — the risk was known but never tested for.
- **MANAGE** — the risk was known and measured, but the response/control failed at runtime.

**Step 5 — The counterfactual control.** One specific control that, had it existed, would plausibly have stopped or materially limited this incident. This is the hardest step and the most useful: it converts a taxonomy exercise into something a risk owner can act on.

## 3. On the limits of this

Three honest caveats:

- **Public reporting is incomplete.** Incidents are mapped from what was disclosed, which is usually less than what happened. Mappings may be revised as more detail emerges; revisions are made in commit history rather than silently.
- **Attribution of a "primary" technique is a judgement call.** Real attack chains involve many techniques. The index lists the one most characteristic of the incident, with the full chain in the incident file.
- **The counterfactual control is an argument, not a proof.** It is one defensible answer, offered so it can be disagreed with — not the only answer.

## 4. Sources

Primary sources are cited in each incident file. The OWASP GenAI Security Project's quarterly exploit round-ups are used as a discovery source for candidate incidents; every candidate is then verified against its own primary reporting before being written up here.
