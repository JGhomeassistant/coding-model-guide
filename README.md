# coding-model-guide

Cost-for-quality model and effort defaults for Cursor coding agents, reviewed monthly.

[`MODEL_GUIDE.md`](MODEL_GUIDE.md) is the single source of truth for which model and effort level a coding agent should use for each kind of work: planning, UI, implementation, debugging, quick edits, and long-context refactors. It puts the hard rules first, then the per-category defaults, the escalation rules, and the benchmark evidence. The evidence covers cost per task, tokens per task, and how many tasks $20 of external-model allowance buys, with a source link for every figure.

Every project repo links here instead of keeping its own model table, so a monthly update reaches every project at once.

- Raw guide (for agents): https://raw.githubusercontent.com/JGhomeassistant/coding-model-guide/main/MODEL_GUIDE.md
- History: [`CHANGELOG.md`](CHANGELOG.md)

## Add it to a project

Paste the snippet below into the project's `AGENTS.md`, or save it as `.cursor/rules/model-guide.mdc`. A `.mdc` rule needs the front matter shown; for `AGENTS.md`, leave the front matter out.

```markdown
---
description: Model and effort selection for coding agents
alwaysApply: true
---

## Model selection

Before choosing a model or effort level for any task, including planning, subagents, and cloud-agent launches, read the current guide:
https://raw.githubusercontent.com/JGhomeassistant/coding-model-guide/main/MODEL_GUIDE.md

- Follow the guide over any model table in a plan, spec, or this repo. If they conflict, follow the guide and tell the owner.
- Set the model and effort explicitly on every launch, and verify them in the run details after launching.
- Use included models by default, at standard speed (Fast off).
- Any external model (Claude, Gemini, GPT) needs the owner's OK for that specific task. State the model, the effort, the reason, and the estimated cost from the guide.
- If the guide can't be fetched, use included models only (Composer 2.5 or Grok 4.7) and tell the owner.
```

## Review cadence

The guide is reviewed monthly. Each review updates `MODEL_GUIDE.md` (the "Last reviewed" and "Next review" dates) and adds an entry to `CHANGELOG.md`.
