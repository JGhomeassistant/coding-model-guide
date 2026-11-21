# Changelog

## 2026-11-20

- First published version of `MODEL_GUIDE.md`.
- Per-category defaults with explicit effort levels:
  - Planning: Grok 4.7 Extra High.
  - UI: Grok 4.7 High.
  - Implementation: Composer 2.5 for scoped work, Grok 4.7 High for ambiguous work.
  - Debugging: Grok 4.7 Extra High.
  - Quick edits: Composer 2.5.
  - Long-context refactors: Grok 4.7 High, in chunks.
- Escalation picks, each needing the owner's OK per task:
  - Gemini 4 Neon Medium for planning and debugging.
  - Opus 5.5 Medium for UI design, hard multi-file work, and refactors over 256k.
  - Sonnet 5.5 High for scoped implementation retries.
- Costs are measured per task (tokens actually used times list price), not per-token list price. The source is CursorBench 4.1 (2026-11-18), plus Terminal-Bench 4.0, the SWE-bench Pro subset, Bug Hunt Bench, LMArena WebDev, Design Arena, and AA-LCR.
- Hard rules added:
  - Run at standard speed.
  - Never run Sonnet above High.
  - No Extra High, Max, or Fable on external models without an OK.
  - Set the model and effort explicitly on every launch, and verify them.
- Next review: 2026-12-18.
