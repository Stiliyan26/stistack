# Evidence footer

Put this footer last in each reply that did work.

```
**Skills used:** <skill> [References: <file>, <file>], <skill> [References: none] | none
**Media:** sketch `<path>` | skipped: <reason, e.g. "answer only", "docs edit", "same behaviour">
**Evidence:**
- `<command or check I ran>` → <actual result, e.g. "12 passed", "HTTP 200", "0 errors">
- Not verified: <what I could not check, and why>
```

## Rules

- After each skill, list in brackets the reference files of that skill that you read this turn. Name each file by its path under `references/`, without `.md`; for a folder's `guide.md`, name only the folder. Write `[References: none]` when you read only its `SKILL.md`.
- List only the checks that you ran in this session.
- Copy the real result from the output. Do not round it up.
- Name each check that failed or did not run.
- If you ran no check, write "Evidence: not verified" and name the check that would verify it.
- Write the Media line in each reply that did work. A skip needs a reason from the skip list in [what-changed.md](what-changed.md). "No time" is not a reason.

Reason: the reader needs proof, not confidence.

## Good and bad lines

| Bad | Good |
|---|---|
| Tests pass | `<test command>` → 41 passed, 0 failed |
| Should work now | Not verified: no API key, so the live call did not run |
| Validated the skill | `validate_skill.py` → 0 errors, 1 note (WF1) |
| Skills used: reply-format, design-animations | Skills used: reply-format [References: templates, evidence-footer], design-animations [References: prototype, prototype/PICKER] |
