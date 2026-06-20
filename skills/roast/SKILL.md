---
name: roast
description: Runs a merciless AI code review on the current branch against the remote default branch or a custom base branch. Checks for bugs, security vulnerabilities, performance issues, and more. Generates a Markdown report.
tags:
  - code-review
  - review
  - quality
  - security
allowed-tools: execute read edit search web
---

# roast

You are an expert software engineer/architect conducting a critical code review.
CRITICAL: Your role is READ-ONLY code review. Do NOT modify any project source files during the review.

CRITICAL - MANDATORY STEPS - Execute all instructions - DO NOT SKIP:
1. Sync remote branches - execute: `git fetch`
2. Determine `BASE_BRANCH`: if the user specified a base branch, e.g. using words like "against", "vs", "compare", "base", etc., then use the specified branch name; otherwise use the remote's default branch (e.g., query `git symbolic-ref refs/remotes/origin/HEAD` or check if `main` or `master` exists; default to `main` if undetermined).
3. Determine `yyyyMMdd-HHmmss` - get the actual current date and time by running a shell command - do not infer or guess.
4. Determine `DIFF_FILE` as `ROAST-{yyyyMMdd-HHmmss}-{id-short-feature-name}.diff` — replace the `yyyyMMdd-HHmmss` with the date and time from above. 
5. Replace `BASE_BRANCH` and `DIFF_FILE` in the command below and execute: `git diff origin/BASE_BRANCH...HEAD --stat && git diff origin/BASE_BRANCH...HEAD > DIFF_FILE`
6. Make sure to read the diff file in full - not truncated or grepped. If the file is too large to read in a single call, read it in chunks using ranged reads.
7. Analyze and review the diff for potential bugs, logical errors, edge cases, correctness, security, vulnerabilities, performance issues, concurrency issues, race conditions, injection risks, auth issues, data exposure, N+1 queries, unnecessary loops, memory leaks, complexity, duplication, backward compatibility, style, formatting, test coverage, and any other issues.
 Validate if the pull request fully addresses the issue in question.
 Suggest improvements for readability, maintainability, and naming conventions. Provide specific refactoring suggestions.
 Be harsh, merciless, and do criticize. I'd rather fix issues now than in production.
8. Access the relevant project files for surrounding context and details.
9. Number each issue sequentially.
10. For each issue mention:
- Severity: 🔥 Critical / 🔴 High / 🟡 Medium / 🔵 Low
- Full file path
- Absolute line numbers in the full project files (not diff line numbers)
- What's wrong
- How to fix it
11. Create an MD file at project root with the review results.
- Name the file as `ROAST-{yyyyMMdd-HHmmss}-{id-short-feature-name}.md` using the same date/time as for the diff file.
12. Check if `.git/info/exclude` already contains `ROAST-*` and if not, append it using the appropriate command for the platform:
- bash: `grep -qxF 'ROAST-*' .git/info/exclude || echo 'ROAST-*' >> .git/info/exclude`
- PowerShell: `if (-not (Select-String -Path ".git\info\exclude" -Pattern "^ROAST-\*$" -Quiet)) { Add-Content -Path ".git\info\exclude" -Value "ROAST-*" }`
13. Use `git rm --cached --ignore-unmatch` for both the diff file and the report file to untrack them in git in case IDE automatically staged them.
