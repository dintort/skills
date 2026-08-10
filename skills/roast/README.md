# roast - Merciless Code Review

Runs a thorough, merciless multi-axis code review on the current branch against the default remote branch or a specified
base branch.

Generates a code review report as `ROAST-yyyyMMdd-HHmmss-*.md` and excludes it from Git tracking.

Once the review report is created, ask your AI agent e.g.:

- create a failing test to reproduce #1
- fix #2
- elaborate and explain #3

After you have addressed the found issues, start a new chat session and run the roast again - it could find issues that
it missed the first time or new issues introduced by the fixing. Rinse and repeat until satisfied.

## Installation

```bash
npx skills add dintort/skills --global --skill roast
```

Or simply copy the `roast` directory to your agents' skills at `~/.agents/skills/`.

## Usage

Switch to your feature branch, ensure it is up-to-date.
Commit your local changes if you want them to be included in the review.

Start the agent in the repository you want reviewed.

The skill runs `git fetch` to refresh the remote base branch ref, but does **not** run `git pull`
on your feature branch — so any local uncommitted or unpushed changes are preserved exactly as-is.

> **⚠️ Only committed changes are reviewed:** Uncommitted (staged or unstaged) changes are not included in the diff.

> **⚠️ Size does matter:** The skill always reviews the whole diff in a single thread — it never splits large diffs across sub-agents, since a split review structurally can't catch interactions between separately reviewed files. This means the model must hold the entire diff (plus the source files it opens for context) at once. Small-context models (e.g. below 200k tokens) can silently drop earlier instructions once a large diff fills their context — the skill warns when the diff exceeds ~5000 changed lines or 1MB, but on a small-context model even that warning may already be lost. For large diffs, narrow the PR if you can't use a bigger-context model.

In an agent chat choose a smart model with large context window (e.g. Sonnet with 1M) and invoke:

```
/roast
```

Or with a ticket description for more targeted feedback:

```
/roast PROJ-1234: Add input validation to payment form
Validate card number, expiry date and CVV before submission.
Users were able to submit the payment form with invalid card details, causing downstream errors.
Add validation: Luhn check for card number, future-date check for expiry, and 3-4 digit check for CVV.
Submit button is disabled until all fields pass validation. Error messages are shown inline.
```

By default, the review compares against the remote default branch (`origin/HEAD`). To use a different base branch:

```
/roast against dev
```

## ⚠️ DISCLAIMER

- AI-generated review. It is not meant to replace human code review, but it might provide some useful insight. Take it with a grain of salt.
- Use AI review in addition to human review, not instead of it.
- The author is fully responsible for the code regardless if it was generated and/or reviewed with AI.
- This code review prompt is very aggressive and produces some false-positives: do not take it as an ultimate directive to act upon - use your own critical judgment.

## Attribution

- Based on Addy Osmani's [code-review-and-quality skill](https://github.com/addyosmani/agent-skills/blob/main/skills/code-review-and-quality/SKILL.md) and [code-reviewer agent](https://github.com/addyosmani/agent-skills/blob/main/agents/code-reviewer.md).
