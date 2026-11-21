# Coding Model Guide for Cursor Agents

- **Last reviewed:** 2026-11-20
- **Next review:** 2026-12-18 (monthly). Re-check sooner if a new Grok or Composer release ships, Gemini 4 Carbon's price change takes effect (2026-12-01), OpenAI model access in Cursor ends, or the external allowance runs out early.
- **Applies to:** every coding agent working in Cursor, both cloud agents and the IDE.
- **Precedence:** this guide overrides any model table in a plan, spec, or AGENTS.md. If they conflict, follow this guide and flag the conflict to the owner.

---

## 1. Hard rules (read first)

1. **Use included models by default.** Composer 2.5 and Grok 4.7 draw from the plan's included Cursor Models pool. Third-party models (Claude, Gemini, GPT) bill a small, limited external allowance at API list price.
2. **Run at standard speed.** Do not use Fast mode unless the owner asks for speed. Grok 4.7 Fast costs 2x the standard rate, and Composer 2.5 Fast costs 6x on input.
3. **Set the model and effort explicitly on every launch, then verify them.** After launching, confirm in the run details that the agent is on the intended model and effort. Subagents have been reported to fall back to Auto (unverified whether this is fixed). Report any mismatch to the owner.
4. **Every external-model escalation needs the owner's OK for that specific task.** State the model, the effort, the reason, and the estimated cost using the per-task figures below.
5. **Never run Sonnet 5.5 above High.** At matched quality, Opus 5.5 Medium or High costs the same or less.
6. **Never use Extra High (xhigh) or Max effort on an external model, or any Fable model, without a specific OK from the owner.**
7. **Never name an external model for a subagent unless it is approved.** Named third-party subagents bill the external allowance even when the parent runs on an included model.
8. **Compare models by measured cost per completed task at matched quality.** Never compare by per-token price or by effort label; effort levels are calibrated differently for each model.
9. **Keep long-context work under 256k input on Grok 4.7.** Input above 256k bills at 2x. Split refactors into chunks.
10. **Report spend.** Tell the owner when a run is unusually long or costly, or when the external allowance is draining early in the cycle.

## 2. Defaults by category

| Category | Default (included) | Escalate to (owner OK per task) | When to escalate | Gap vs default |
|---|---|---|---|---|
| Planning / architecture | Grok 4.7 **Extra High** | Gemini 4 Neon **Medium** (fallback: Opus 5.5 **Medium**) | New architecture, unfamiliar codebase, decisions costly to reverse | Neon Medium +9.1 pts; Opus Medium +1.4 pts (a tie) on CursorBench 4.1 |
| GUI / front-end design | Grok 4.7 **High** (UI inside an existing design system) | Opus 5.5 **Medium** | New visual design, landing pages, anything judged on looks | Large: Grok 4.7 trails by 96 to 180 Elo on the UI arenas |
| Bulk implementation | Composer 2.5 (standard speed) for scoped work; Grok 4.7 **High** for ambiguous multi-file work | Scoped: Sonnet 5.5 **High**. Hard multi-file: Opus 5.5 **Medium** | Only after two failed attempts on included models | Large on hard ambiguous tasks; not measured on scoped tasks |
| Debugging / code review | Grok 4.7 **Extra High** | Gemini 4 Neon **Medium** (Anthropic alternative: Sonnet 5.5 **High**) | Bug still open after two tries; pre-merge review of important PRs | Large: +18.7 bugs fixed out of 105 |
| Quick edits / small tasks | Composer 2.5 (standard speed) | None | Not applicable | Not measured |
| Long-context refactors | Grok 4.7 **High**, chunks under 256k | Opus 5.5 **Medium** (1M context, no surcharge) | Only when one pass truly needs more than 256k | Moderate: 76.7% vs 84.7% on AA-LCR |

Composer 2.5 has no effort setting, only standard and Fast speeds.

## 3. How cost is counted

- **Effective dollars:** the tokens a model actually used per task, times list price (cache included), as the benchmarks report. A model that is cheap per token but burns many more tokens shows its true, higher cost here.
- **Tasks per $20:** $20 divided by cost per task. This shows how fast the external allowance burns, and applies only to third-party models.
- **Included models** (Grok 4.7, Composer 2.5) cost $0 of the external allowance. Their dollar figures are list-price equivalents that apply only once the included pool runs out. Cursor does not publish the pool's size.
- **$ per solved task** (cost divided by score) favors cheap low-effort settings that solve less, so compare at matched score as well.

## 4. Evidence by category

### 4.1 Planning / architecture

No benchmark isolates planning with cost data. The proxies are CursorBench 4.1 (overall; it includes planning problems) and the Artificial Analysis Intelligence Index.

| Model + effort | CursorBench 4.1 | $/task | Tokens/task | $ per solved | Tasks per $20 | AA Index (cost per index task) |
|---|---|---|---|---|---|---|
| Grok 4.7 High (included) | 46.0% | $6.88 | 65,850 | $14.96 | pool | n/a |
| Grok 4.7 Extra High (included) | 48.6% | $10.19 | 90,268 | $20.97 | pool | n/a |
| Opus 5.5 Low | 42.7% | $1.43 | 19,497 | $3.35 | 14.0 | 42 ($0.55) |
| Opus 5.5 Medium | 50.0% | $2.85 | 39,736 | $5.70 | 7.0 | 51 ($1.34) |
| Opus 5.5 High | 54.5% | $3.87 | 56,162 | $7.10 | 5.2 | 54 ($1.82) |
| Sonnet 5.5 High | 43.8% | $1.41 | 43,088 | $3.22 | 14.2 | 47 ($0.88) |
| Sonnet 5.5 Extra High | 49.9% | $3.13 | 112,299 | $6.27 | 6.4 | 52 ($2.01) |
| Sonnet 5.5 Max | 54.4% | $8.63 | 342,273 | $15.86 | 2.3 | 56 ($5.46) |
| Gemini 4 Neon Medium | 57.7% | $3.07 | 47,059 | $5.32 | 6.5 | n/a |
| Gemini 4 Neon High | 63.7% | $6.54 | 88,596 | $10.27 | 3.1 | n/a |

Sources: [CursorBench 4.1](https://cursor.com/cursorbench) (updated 2026-11-18); AA Index figures (mid-October) via [Lindy](https://www.lindy.ai/blog/sonnet-5-5-vs-opus-5-5), citing Artificial Analysis.

### 4.2 GUI / front-end design

No UI benchmark publishes tokens, cost per task, or (for most rows) effort level, so the cost side of this category cannot be measured and the escalation pick is inferred.

| Model | LMArena WebDev Elo | Design Arena UI Elo | Gap vs Grok 4.7 (WebDev / Design) | Output $/M |
|---|---|---|---|---|
| Claude Opus 5.5 | 1818 | 1369 | +180 / +96 | $20 |
| Claude Sonnet 5.5 | 1771 | 1378 | +133 / +105 | $10 |
| Gemini 4 Carbon | 1769 | 1342 | +131 / +69 | $10 |
| Gemini 4 Neon | 1763 | 1359 | +125 / +86 | $15 |
| Grok 4.7 (included) | 1638 | 1273 | baseline | $6 |
| Claude Haiku 5.5 | 1596 | 1259 | -42 / -14 | $0.50 |

Sources: [LMArena WebDev](https://arena.ai/leaderboard/code/webdev) via [llmbenchmark.ai](https://llmbenchmark.ai/benchmarks/lmarena-webdev); [Design Arena](https://llmbenchmark.ai/benchmarks/design-arena-ui) (both 2026-11-20).

### 4.3 Bulk implementation

**Terminal-Bench 4.0** (Anthropic's runs in Claude Code; Sonnet re-priced after its 2026-10-07 cache cut):

| Model + effort | Score | $/attempt | $ per solved | Attempts per $20 |
|---|---|---|---|---|
| Sonnet 5.5 Low | 20.0% | $0.62 | $3.10 | 32.3 |
| Sonnet 5.5 Medium | 28.8% | $0.68 | $2.36 | 29.4 |
| Sonnet 5.5 High | 43.0% | $1.46 | $3.40 | 13.7 |
| Sonnet 5.5 Extra High | 61.5% | $4.34 | $7.06 | 4.6 |
| Sonnet 5.5 Max | 70.6% | $10.44 | $14.79 | 1.9 |
| Opus 5.5 Low | 38.5% | $1.29 | $3.35 | 15.5 |
| Opus 5.5 Medium | 57.6% | $2.94 | $5.10 | 6.8 |
| Opus 5.5 High | 64.2% | $3.88 | $6.04 | 5.2 |
| Opus 5.5 Extra High | 66.4% | $7.35 | $11.07 | 2.7 |
| Opus 5.5 Max | 64.8% | $11.24 | $17.35 | 1.8 |

Source: [claudefa.st](https://claudefa.st/blog/models/claude-sonnet-vs-opus), from Anthropic's launch charts.

**SWE-bench Pro subset** (Anthropic; not comparable to the public leaderboard; Sonnet Low and Medium not reported):

| Model + effort | Solved | $ per solved task | Solved tasks per $20 |
|---|---|---|---|
| Sonnet 5.5 High | 87.6% | $0.11 | 182 |
| Opus 5.5 Low | 87.4% | $0.12 | 167 |
| Opus 5.5 Medium | 92.8% | $0.22 | 91 |
| Opus 5.5 High | 95.3% | $0.30 | 67 |
| Opus 5.5 Extra High (463-task subset) | 96.5% | $0.75 | 27 |

Source: [Anthropic, Optimizing for cost and intelligence](https://platform.claude.com/docs/en/about-claude/models/optimizing-for-cost-and-intelligence).

**Other data points:**
- FrontierCode v1.1: Opus 5.5 Medium scored 54.6% for $0.80, above Sonnet 5.5's best of 52.1% (Extra High) for $1.59 ([claudefa.st](https://claudefa.st/blog/models/claude-sonnet-vs-opus)).
- Composer 2.5 and Grok 4.7: no implementation benchmark publishes cost by effort. Composer 2.5 scores 79.6% on SWE-bench Verified in Vals AI's harness ([Benchmark Registry](https://benchmarkregistry.org/models/50004)). On CursorBench 4.0's harder tasks, Composer 2.5 scored 27.7% at $0.68 per task, against 43.9% for Grok 4.7 High ([Stackness](https://stackness.dev/blog/composer-2-5-vs-grok-4-7-vs-opus-5-5-in-cursor-which-slot-each-model-keeps-priced-per-task)).

**Takeaway:** for scoped work, Sonnet 5.5 High is cheapest per solved task. For hard multi-file work, Opus 5.5 Medium wins; Sonnet beats it only by spending about 48% more. Keep bulk volume on included models, because external bulk work at $1.41 to $2.94 per task empties $20 in 7 to 14 tasks.

### 4.4 Debugging / code review

**Bug Hunt Bench** (105 planted bugs in two production TypeScript repos, graded blind, run in each vendor's own CLI rather than Cursor; updated 2026-11-19):

| Model + effort | Bugs fixed (of 105) | Cost per full run | $ per fixed bug | Fixes per $20 |
|---|---|---|---|---|
| Grok 4.7 Medium (included) | 26.7 | $16.76 (floor) | $0.63 | pool |
| Grok 4.7 High (included) | 19.3 | $12.29 (floor) | $0.64 | pool |
| Grok 4.7 Extra High (included) | 29.0 | $22.96 (floor) | $0.79 | pool |
| Sonnet 5.5 Medium | 23.3 | $11.11 | $0.48 | 41.9 |
| Sonnet 5.5 High | 34.3 | $19.85 | $0.58 | 34.6 |
| Sonnet 5.5 Extra High | 36.0 | $42.86 | $1.19 | 16.8 |
| Sonnet 5.5 Max | 51.3 | $154.03 | $3.00 | 6.7 |
| Opus 5.5 Low | 22.3 | $8.34 | $0.37 | 53.5 |
| Opus 5.5 Medium | 30.3 | $15.68 | $0.52 | 38.6 |
| Opus 5.5 High | 31.7 | $22.25 | $0.70 | 28.5 |
| Opus 5.5 Extra High | 36.0 | $34.98 | $0.97 | 20.6 |
| Opus 5.5 Max | 41.7 | $58.53 | $1.40 | 14.2 |
| Gemini 4 Neon Low | 29.7 | $5.29 | $0.18 | 112.3 |
| Gemini 4 Neon Medium | 47.7 | $12.84 | $0.27 | 74.3 |
| Gemini 4 Neon High | 57.0 | $29.11 | $0.51 | 39.2 |
| Gemini 4 Carbon Low | 46.0 | $7.67 | $0.17 | 119.9 |
| Gemini 4 Carbon Medium | 56.0 | $24.95 | $0.45 | 44.9 |
| Haiku 5.5 Max | 21.5 | $5.83 | $0.27 | 73.8 |

Source: [Bug Hunt Bench](https://github.com/phuryn/bug-hunt-bench). Grok costs are reconstructed lower bounds ("floor"). Carbon was run at its introductory price, which doubles on 2026-12-01.

### 4.5 Quick edits

No benchmark measures small single-file edits with cost data. Composer 2.5 is the cheapest included model ($0.50 input / $2.50 output per million tokens at list) ([Cursor Docs](https://cursor.com/docs/models)).

### 4.6 Long-context refactors

No cost, token, or effort data is published for this category.

| Model | AA-LCR | ProgramBench (long-context coding) | Context in Cursor |
|---|---|---|---|
| Claude Opus 5.5 | 84.7% | 91.2% | up to 1M, same per-token rate |
| Claude Sonnet 5.5 | 82.7% | 79.7% | up to 1M, same per-token rate |
| Gemini 4 Carbon | 79.3% | n/a | n/a |
| Grok 4.7 (included) | 76.7% | n/a | 256k; up to 500k at 2x |
| Composer 2.5 (included) | n/a | n/a | 200k |

Sources: [AA-LCR via llmbenchmark.ai](https://llmbenchmark.ai/benchmarks/aa-long-context-reasoning); ProgramBench from Anthropic's Sonnet 5.5 launch via [claudefa.st](https://claudefa.st/blog/models/claude-sonnet-vs-opus); Composer context via [Stackness](https://stackness.dev/blog/composer-2-5-vs-grok-4-7-vs-opus-5-5-in-cursor-which-slot-each-model-keeps-priced-per-task).

## 5. Opus 5.5 Medium vs Sonnet 5.5

**Verdict: the evidence partly supports Opus 5.5 Medium as the sweet spot.**

- **Supports it:** for mid-to-high quality work, Opus 5.5 Medium matches Sonnet 5.5 Extra High for less.
  - CursorBench 4.1: 50.0% for $2.85 and 39.7k tokens, against 49.9% for $3.13 and 112k tokens.
  - Terminal-Bench 4.0: 57.6% for $2.94; Sonnet needs Extra High (61.5%, $4.34) to beat it.
  - FrontierCode: 54.6% for $0.80, against Sonnet's best of 52.1% for $1.59.
  - AA Index: 51 for $1.34, against 52 for $2.01.
- **Contradicts it:** for scoped implementation and bug-fixing, Sonnet 5.5 High is as good or better for less.
  - SWE-bench Pro subset: Sonnet High solves 87.6% at $0.11 per solved task, equal to Opus Low.
  - Bug Hunt Bench: Sonnet High fixes 34.3 bugs for $19.85, against Opus Medium's 30.3 for $15.68 and Opus High's 31.7 for $22.25.
- **Untestable for UI:** the UI leaderboards publish no cost or effort data.
- **Bigger finding:** on the two benchmarks that include it, Gemini 4 Neon Medium beats both Claude models for about the same money. It launched 2026-11-18, so recheck it next review.

## 6. Full CursorBench 4.1 table (model × effort)

| Model | Effort | Score | $/task | Tokens/task | $ per solved | Tasks per $20 |
|---|---|---|---|---|---|---|
| Grok 4.7 (included) | Low | 31.5% | $2.73 | 14,493 | $8.67 | pool |
| Grok 4.7 (included) | Medium | 42.5% | $5.52 | 41,173 | $12.99 | pool |
| Grok 4.7 (included) | High | 46.0% | $6.88 | 65,850 | $14.96 | pool |
| Grok 4.7 (included) | Extra High | 48.6% | $10.19 | 90,268 | $20.97 | pool |
| Sonnet 5.5 | Low | 31.8% | $0.44 | 13,798 | $1.38 | 45.5 |
| Sonnet 5.5 | Medium | 34.3% | $0.63 | 19,144 | $1.84 | 31.7 |
| Sonnet 5.5 | High | 43.8% | $1.41 | 43,088 | $3.22 | 14.2 |
| Sonnet 5.5 | Extra High | 49.9% | $3.13 | 112,299 | $6.27 | 6.4 |
| Sonnet 5.5 | Max | 54.4% | $8.63 | 342,273 | $15.86 | 2.3 |
| Opus 5.5 | Low | 42.7% | $1.43 | 19,497 | $3.35 | 14.0 |
| Opus 5.5 | Medium | 50.0% | $2.85 | 39,736 | $5.70 | 7.0 |
| Opus 5.5 | High | 54.5% | $3.87 | 56,162 | $7.10 | 5.2 |
| Opus 5.5 | Extra High | 56.7% | $6.54 | 104,901 | $11.53 | 3.1 |
| Opus 5.5 | Max | 58.1% | $11.88 | 213,070 | $20.45 | 1.7 |
| Gemini 4 Neon | Low | 45.5% | $1.26 | 19,580 | $2.77 | 15.9 |
| Gemini 4 Neon | Medium | 57.7% | $3.07 | 47,059 | $5.32 | 6.5 |
| Gemini 4 Neon | High | 63.7% | $6.54 | 88,596 | $10.27 | 3.1 |
| Gemini 4 Neon | Extra High | 65.4% | $10.67 | 136,273 | $16.31 | 1.9 |
| Gemini 4 Carbon | Low | 55.0% | $8.64 | 48,526 | $15.71 | 2.3 |
| Gemini 4 Carbon | Medium | 60.4% | $7.22 | 103,189 | $11.95 | 2.8 |
| Gemini 4 Carbon | High | 61.5% | $8.52 | 114,518 | $13.85 | 2.3 |
| Haiku 5.5 | Low | 27.8% | $0.08 | 23,673 | $0.29 | 250.0 |
| Haiku 5.5 | Medium | 34.2% | $0.17 | 43,441 | $0.50 | 117.6 |
| Haiku 5.5 | High | 37.5% | $0.31 | 77,196 | $0.83 | 64.5 |
| Haiku 5.5 | Extra High | 45.2% | $0.56 | 149,314 | $1.24 | 35.7 |
| Haiku 5.5 | Max | 48.4% | $1.14 | 344,890 | $2.36 | 17.5 |
| Fable 5.2 | Low | 53.4% | $4.26 | 40,384 | $7.98 | 4.7 |
| Fable 5.2 | Medium | 54.2% | $5.01 | 47,678 | $9.24 | 4.0 |
| Fable 5.2 | High | 56.5% | $10.51 | 108,213 | $18.60 | 1.9 |
| Fable 5.2 | Extra High | 58.8% | $17.84 | 194,918 | $30.34 | 1.1 |
| Fable 5.2 | Max | 61.3% | $48.11 | 541,003 | $78.48 | 0.4 |
| GPT-5.6 Sol | Medium | 28.7% | $1.97 | 10,554 | $6.86 | 10.2 |
| GPT-5.6 Sol | High | 33.2% | $3.00 | 16,319 | $9.04 | 6.7 |
| GPT-5.6 Sol | Max | 40.9% | $7.60 | 40,466 | $18.58 | 2.6 |

Source: [CursorBench 4.1](https://cursor.com/cursorbench). Composer 2.5 is not on 4.1; on 4.0 it scored 27.7% at $0.68 per task. Unverified: Grok 4.7's cost per task looks high for its token counts, so treat its dollar figures as approximate.

## 7. Per-token list prices in Cursor (per million tokens)

| Model | Input | Cache read | Output | Billed from |
|---|---|---|---|---|
| Composer 2.5 | $0.50 | $0.20 | $2.50 | included pool (Fast: $3 / $15) |
| Grok 4.7 | $2 | $0.50 | $6 | included pool (Fast 2x; input above 256k bills 2x) |
| Claude Haiku 5.5 | $0.10 | $0.01 | $0.50 | external (5x above 100k input) |
| Claude Sonnet 5.5 | $2 | $0.10 | $10 | external |
| Claude Opus 5.5 | $4 | $0.20 | $20 | external |
| Gemini 4 Neon | $3 | $0.15 | $15 | external |
| Gemini 4 Carbon | $2 (rising to $4 on 2026-12-01) | $0.10 (rising to $0.20) | $10 (rising to $20) | external |
| Claude Fable 5.2 | $8 | $0.20 | $40 | external |

Source: [Cursor Docs, Models & Pricing](https://cursor.com/docs/models), read 2026-11-20. OpenAI has extended Cursor's access to GPT-5.6 models only temporarily, with no end date set ([OpenAI](https://openai.com/index/our-decision-on-cursor-following-its-acquisition-by-spacex/)), so don't depend on them.

## 8. Caveats

- Benchmarks run in different harnesses: Cursor's agent (CursorBench), Claude Code (Anthropic's charts), and each vendor's CLI (Bug Hunt Bench). Results inside Cursor may differ.
- Score differences of a point or two are within noise.
- CursorBench is run by Cursor, which co-trains Grok; it still ranks Grok below Claude and Gemini.
- Gemini 4 Neon launched 2026-11-18, and its evidence is limited to CursorBench 4.1, Bug Hunt Bench, and the UI arenas.
- Cursor does not publish the size of the included pool.
