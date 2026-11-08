# pi-review

`pi-review` adds a practical code review workflow to Pi via `/review` and `/end-review`
that is used by us at Earendil.

## Install

```bash
pi install git:github.com/vhxnif/pi-review
```

## What It Does

- Review **uncommitted changes**
- Review changes against a **base branch**
- Review a specific **commit**
- Review a GitHub **pull request** (checks it out locally via `gh`)
- Review one or more **folders/files** as a snapshot (not a diff)
- Produce prioritized findings with a clear verdict and actionable follow-ups
- It separates feedback to the agent from human callouts
- Optionally loop automatically: fix findings on the main branch, return to the review branch, and verify until clean

It also supports custom shared instructions that are loaded from `REVIEW_GUIDELINES.md`.

## Quick usage

```bash
/review
/review uncommitted
/review branch main
/review commit abc123
/review pr 123
/review pr https://github.com/owner/repo/pull/123
/review folder src docs
/review branch main --extra "focus on performance and error handling"
```

When a review session is active, finish it with:

```bash
/end-review
```

You can then return only, return + summarize, or return + queue fixing work.

## Auto review loop

When starting a review in a fresh branch, `/review` also offers an automatic mode:

```
After the review?
  Review only (finish with /end-review)
  Auto: fix findings and re-review until clean
```

Auto mode keeps the review branch isolated and loops against the main branch:

1. Review on the review branch.
2. Return to the main branch and apply the findings.
3. Return to the same review branch and verify each finding.
4. Repeat until no findings remain, the round limit is reached, or no progress is detected.

The review branch can use a different model from the main branch. Pick one in the
selector, or keep the current model. The main branch model is restored on every return.

You can also skip the prompts with flags:

```bash
/review uncommitted --auto
/review branch main --auto --review-model anthropic/claude-opus-4 --max-rounds 4
/review --auto --summary
```

Flags:

- `--auto` - run the fix/re-review loop (forces a fresh review branch)
- `--review-model <provider/modelId>` - model for the review branch (requires `--auto`; defaults to the current model)
- `--max-rounds <n>` - maximum review rounds (requires `--auto`; default `5`, range `1`-`20`)
- `--summary` - also generate branch summaries on each transition (requires `--auto`; off by default; findings are passed directly)

Auto mode stops safely when:

- the reviewer reports no findings,
- the round limit is reached,
- findings repeat and the fix round made no changes,
- a run fails or the review output is not parseable,
- you press `Esc` or run `/review-auto-stop`.

> **Warning:** auto mode lets the agent edit the shared working tree unattended. It
> does not commit or switch git branches. Commit or stash unrelated work before
> starting, so fixes stay scoped and easy to review.

The reviewer must end each round with a machine-readable block:

```
<!--REVIEW_RESULT:{"verdict":"needs-attention","findings":[{"id":"F1","priority":"P1","location":"src/file.ts:42","title":"short actionable issue","detail":"why it matters and what should change"}]}-->
```

Only the compact finding list is sent to the fix branch; the full review prose stays
on the review branch, so the fix prompt stays short and non-redundant.

If that block is missing, the loop stops instead of guessing.
