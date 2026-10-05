# 09 — Claude Code deletes a user's home directory

**Date:** December 2025 (Reddit report, covered by Simon Willison on 9 December 2025)
**Impact:** most of a user's Mac home directory deleted

---

## What happened

A user on r/ClaudeAI reported that Claude Code, Anthropic's command-line coding agent, ran:

```
rm -rf tests/ patches/ plan/ ~/
```

The first three paths were project folders the agent meant to clean up. The last, `~/`, is the shell's shorthand for the user's entire home directory: documents, keys, configuration, everything. Simon Willison's write-up put it plainly: "See that `~/` at the end? That's your entire home directory."

The report doesn't establish which permission mode the user was running, so this entry makes no claim about whether a confirmation prompt was shown or bypassed.

## Why this one is in the index

It is the same mechanism as [08](08-antigravity-drive-wipe.md), scope widening, from a different vendor three weeks later. Two independent agents produced the same class of error, a correct destructive command with one over-broad path. That is the evidence that this is a property of the setup (model output piped into a shell) rather than a quirk of one product.

## MITRE ATLAS technique chain

**None applies.** Same reasoning as 06 to 08.

## OWASP LLM Top 10 (2025) classes

| Class | Applies because |
|---|---|
| **LLM05 — Improper Output Handling** | **Primary.** Model output was handed to `rm -rf` and shell expansion turned `~/` into the home directory. |
| **LLM06 — Excessive Agency** | The agent's shell had the user's full file permissions. |

LLM05 comes first here, unlike 08, because the whole failure fits in one token of output that no layer inspected.

## NIST AI RMF — which function owned this

**MANAGE.** The risk of an agent running a destructive command against the wrong path is well known in the agent-tooling community. The gap is the absence of a runtime control at the point of execution.

## Counterfactual control

**The one control:** a path guard on destructive commands, enforced after shell expansion. Any `rm`, `rmdir`, `mv` or overwrite whose resolved target is the home directory, the file-system root, or anything outside the workspace is blocked, whatever the permission mode.

Supporting control: run agents in a sandbox (a container or a separate user account) whose home directory isn't yours.

## One line for a non-technical risk owner

*"The AI's cleanup command had one extra character, and that character meant 'everything I own'."*

## Sources

- Simon Willison, 9 December 2025: https://simonwillison.net/2025/Dec/9/claude/
- Gigazine coverage, 16 December 2025: https://gigazine.net/gsc_news/en/20251216-claude-code-cli-mac-deleted
