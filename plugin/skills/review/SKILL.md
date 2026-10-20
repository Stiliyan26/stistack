---
name: review
description: Reviews code or a plan, proves each problem, and fixes it. Reviewers fix every problem they find and report each fix in chat; no review files to read. Use on a branch or PR before merge (reviewers fix and commit, then interactive-explanation writes the PR page), also on a commit range, the whole app, a security pass or a plan (reviewers edit the plan files); or to set up a project's check tools.
---

# Review

You lead the review. Each feature gets one reviewer, a sub-agent. The
reviewer finds the problems in its feature, proves each one and fixes it.
You plan the run, verify the result and tell the user in chat what was
fixed.

## Pick the target

| Target | Flag for step 3 | Fixes go on |
| --- | --- | --- |
| A branch | `--base <branch>`, `main` by default | that branch |
| A pull request | `gh pr checkout <n>`, then `--base origin/<its base>` | that branch |
| Commits | `--range <a>..<b>`; one commit is `<sha>^..<sha>` | the current branch |
| The whole app | `--app` | a new branch `review/app-<date>` |
| The attack surface | `--security` with any target; reviewers also follow [security.md](references/security.md) | as for its target |
| Uncommitted work | `--uncommitted` | ask the user to commit first, then review it as a branch |
| A plan | `--plan <files>`: plan.md, the contract, the feature files | the plan files, in place |

Every target runs in fix mode: the reviewer fixes each problem it finds.
With no target named, take uncommitted work if any exists. Otherwise take
the branch against `main`.

A plan is fixed in place. The reviewer edits the plan file for a clear gap.
It does not invent a product choice the user never made. It writes its
recommendation into the plan, marked `my call (review)`, and lists it as
`left for you`. You ask the user that question in chat.

"Report only" from the user: the reviewers find and prove, and edit
nothing. Their findings go in the chat reply only.

Write no review files. The user never reads a file or ticks a box to finish
a review. Put the goal and the red checks in each reviewer's prompt. Put
screenshots and logs that a step needs in `temp/verification/review-<date>/`
(gitignored). Nobody has to read them.

Copy this checklist into the reply and tick it off:

```text
Review progress:
- [ ] 0 Checks run, red ones noted
- [ ] 1 Config read or written
- [ ] 2 Worry and depth asked or skipped
- [ ] 3 Plan approved, one reviewer per feature
- [ ] 4 Every reviewer replied or listed as not run
- [ ] 5 Cross-feature problems fixed or left for you
- [ ] 6 Checks green after the last fix
- [ ] 7 Explainer built for code, digest sent in chat
```

## 0. Checks

1. Run the check command (`commands.check` in `.claude/stack.json`).
2. Note which checks are red. Send them to the reviewers and put them in the
   digest. Continue the review.
3. If the project has no size, import, copied-code or dead-code check, say so
   in the digest. Offer [tools.md](references/tools.md).

Done when you know which checks are red.

## 1. Config

Read `.claude/review/config.json`. If it is missing, ask the questions in
[config.md](references/config.md). Offer the models this CLI can run. Write
the file.

Done when the file exists.

## 2. Ask

Ask in one message what worries the user most here. Ask "quick or full?"
only when the config does not settle it. Skip each question the request
already answers. Skip this step when the request says to go ahead.

Done when both questions are answered or skipped.

## 3. Plan

1. Run `node <skill-root>/scripts/triage.mjs --out temp/review/ <flag>`.
   `<skill-root>` is the folder of this `SKILL.md`. The script writes
   `plan.json` (one entry per feature, see [triage.md](references/triage.md)),
   `files.txt` and `diff.patch`. These are inputs for you, not for the user.
2. Write the goal in one paragraph: what the change is for, then the user's
   worry. Take it from the request, the PR text, the commits, the plan files
   and `CLAUDE.md`. Put it in each reviewer's prompt.
3. Show the plan block from [config.md](references/config.md). Wait for a
   yes. The user may change the number of reviewers, merge or drop features,
   or name another model.
4. With fewer reviewers than features, let the smallest features share a
   reviewer. Remove a feature only when the user drops it. With more features
   than `maxReviewers`, propose the merge yourself.
5. Do not wait when the request said to go ahead. Show the plan block as one
   chat line instead.

Done when the user said yes, or step 5 applies, and every feature in
`plan.json` has a reviewer.

## 4. Reviewers

Start one sub-agent per feature, all at once, on the config's
`reviewerModel`. Tell each to follow [reviewer.md](references/reviewer.md).
Give each:

- the target, the branch and `maxRounds`; say "report only" when the user
  did,
- its feature: name, files, lines, and its `source` from `plan.json` when
  that is a feature file,
- the goal, the red checks and the base ref for `git diff`,
- the path to [checklist.md](references/checklist.md),
- the path to the `write-code` skill's `SKILL.md`,
- whether it may start the app, and the commands from `CLAUDE.md`,
- its problem numbers: 1-99 for the first reviewer, 100-199 for the next,
- for a plan: the plan files and the checklist's "Plan mode" section.

If a reviewer fails or times out, list it as not run, with its feature.
Start it again only with the same prompt. If sub-agents are not available,
review the features yourself, one at a time, by `reviewer.md`. Say so in the
digest.

Done when every reviewer has replied or is listed as not run.

## 5. Cross-feature problems

A reviewer may name a problem whose fix lives in another feature. Send it
once to that feature's reviewer, with the same agent and model. If it is
still open after that, mark it `left for you`.

Done when each of these problems is fixed or `left for you`.

## 6. Verify

1. On the branch after the last fix, run the check command and the full test
   suite.
2. Run the project's own end-to-end check on each reviewed feature when the
   project has one.
3. If a check goes red, send the failure once to the reviewer of the feature
   it points at. Then return to step 6.1. If it is still red, stop and show the
   user the output in chat.
4. For a plan, read each changed plan file once more. Check that every
   `my call (review)` mark is in a reviewer's `left for you` list.

Done when every check is green, or listed as not run with the command that
would run it.

## 7. Explain and hand over

For a code target:

1. Run `interactive-explanation` on the branch as it is now. Use pr mode when
   a feature file exists. Otherwise use task mode.
2. Give it `tests`: one line per fixed problem with its proof, and the verify
   result.
3. Give it `risks.items`: each `left for you` problem, and each fix that is
   hard to undo.
4. Give it a line for the PR text: "Review: <n> fixed in <shas>, <m> left for
   you".
5. Let the explainer build the page and the PR text. Opening the PR is a
   separate step.

For a plan or "report only", skip 1 to 5.

6. Send the digest in chat, in this shape:

```text
Review: 9 found · 7 fixed · 1 left for you · 1 not run
Fixed
#7 editing a paid order charged the card twice — proof: orders.test.ts -t "edit paid" → 1 passed (a1b2c3d)
#9 refund total ignored the discount — proof: curl :3000/api/refunds/42 → {"total":18.00} (d4e5f6a)
Left for you
#12 any customer can read another customer's invoices — a permission rule. Check the owner in getInvoices (my pick), or add a row-level policy?
Not run
the e2e suite — no browser here; pnpm e2e e2e/orders.spec.ts
Verify: pnpm run check → green · pnpm test → 212 passed
```

Ask each `left for you` question in the same message. The user answers in
chat. They act on nothing else.

Done when the user has the digest and, for code, the page is built.

## Set up a project

| Need | Read |
| --- | --- |
| Check tools: size, nesting, cycles, layers, copied and dead code | [tools.md](references/tools.md) |
| The project's own rules in `.claude/review/` | [project-rules.md](references/project-rules.md) |
| Models, the questions, the plan block | [config.md](references/config.md) |
| Templates to copy into `.claude/review/` | [roles](assets/project-rules/roles.md), [scope](assets/project-rules/scope.md), [paging](assets/project-rules/paging.md), [conventions](assets/project-rules/conventions.md) |
