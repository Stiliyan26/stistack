# Backlog

Mechanisms that enforce a rule better than skill prose. Filed by `/reflect`.

## 2026-10-19 — replies lost their sketches after the move to Hydra Code

Cause: the Claude Dashboard system prompt carried a "Change reports" rule that made every sketch. Hydra Code's `hydraHint` had no such rule, and reply-format loads once per session, so its media step fades.

Done: `hydraHint` now carries a Media line (Hydra Code 0.1.1), and reply-format 0.25.0 adds a Media footer line.

- **Stop hook for missing media** (hooks). When the turn used Edit, Write, a Bash file write or Agent, and the last message has neither `![` nor "Media: skipped", block the stop with a reason. Not yet tested under Hydra.
- **UserPromptSubmit reminder** (hooks). Add the one-line media rule to every turn. Cheaper than the Stop hook, but does not enforce anything.
- **Check that embedded images still exist** (hooks). In the same Stop hook, check that each local `![...](path)` points to a file that exists outside `.playwright-mcp/` and `/tmp`.
