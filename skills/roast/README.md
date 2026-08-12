# roast - Merciless Code Review

Runs a thorough, merciless multi-axis code review on the current branch against the default remote branch or a specified
base branch.

Generates a code review report as `ROAST-yyyyMMdd-HHmmss-*.md` in current directory and excludes it from Git tracking.

Once the review report is created, ask your AI agent e.g.:

- create a failing test to reproduce 1
- fix 2
- elaborate and explain 3

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

> **⚠️ Size does matter:** Use a large-context model, and/or narrow the diff.
> The skill reviews the whole diff in one thread, so the model holds the entire diff plus every source file it opens
> (a split review couldn't catch interactions between separately reviewed files).
> Once the context window overflows, earlier instructions get silently evicted
> and the review degrades without saying so.
> The skill warns when the diff is large enough to risk context overflow. This is a rough guesstimate:
> the actual capacity depends on your particular model's context window size.

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
