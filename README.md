# pi-review

`pi-review` adds a practical code review workflow to Pi via `/review` and `/end-review`
that is used by us at Earendil.

Unlike a one-shot review prompt, it creates a dedicated review mode where you can stay
focused on the review itself: inspect findings, ask follow-up questions, challenge false
positives, and tell the agent how you want to handle the issues it found. When you are
done, `/end-review` can take you back to where you started, summarize the review, or
queue follow-up work to fix the findings you agreed on.

## Install

```bash
pi install git:github.com/earendil-works/pi-review
```

## What It Does

- Create a dedicated **review mode** that keeps review discussion separate from regular work
- Let you **talk through findings with the agent** before deciding what should happen next
- Review **uncommitted changes**
- Review changes against a **base branch**
- Review a specific **commit**
- Review a GitHub **pull request** (checks it out locally via `gh`)
- Review one or more **folders/files** as a snapshot (not a diff)
- Produce prioritized findings with a clear verdict and actionable follow-ups
- It separates feedback to the agent from human callouts

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

At that point you can:

- **Return only** to go back to your original position
- **Return and summarize** to bring the review conclusions back with you
- **Return and fix findings** to queue follow-up work based on the review discussion
