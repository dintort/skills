---
name: roast
description: Merciless multi-axis code review on the current branch against the default remote branch or a specified base branch. Checks for bugs, security vulnerabilities, performance issues, and more. Generates a Markdown report.
---

# roast

Multi-dimensional code review with quality gates. Review covers five axes: correctness, readability, architecture, security, and performance.

# Review Guidelines

**CRITICAL:** Your role is READ-ONLY code reviewer. Do NOT modify any project source files during the review.

## The Five-Axis Review

Every review evaluates code across these dimensions:

### 1. Correctness

Does the code do what it claims to do?

- Does it match the spec or task requirements?
- Are edge cases handled (null, empty, boundary values)?
- Are error paths handled (not just the happy path)?
- Do the tests actually verify the behavior? Are they testing the right things?
- Is test coverage sufficient for changed behavior and likely regressions?
- Are there off-by-one errors, race conditions, or state inconsistencies?
- Are concurrency risks handled correctly (ordering, state updates, locking/transaction boundaries)?
- Is backward compatibility preserved where required?

### 2. Readability & Simplicity

Can another engineer (or agent) understand this code without explanation

- Are names descriptive and consistent with project conventions? (No `temp`, `data`, `result` without context)
- Is the control flow straightforward (avoid nested ternaries, deep callbacks)?
- Is the code organized logically (related code grouped, clear module boundaries)?
- Are there any "clever" tricks that should be simplified?
- **Could this be done in fewer lines?** (1000 lines where 100 suffice is a failure)
- **Are abstractions earning their complexity?** (Don't generalize until the third use case)
- Would comments help clarify non-obvious intent? (But don't comment obvious code.)
- Are there dead code artifacts: no-op variables (`_unused`), backwards-compat shims, or `// removed` comments?
- **Is a new conditional bolted onto an unrelated flow?** That's a design smell, not a nit — push the logic into its own helper, state, or policy instead of tangling an existing path.
- **Do repeated conditionals on the same shape appear?** They signal a missing model or dispatcher. A "temporary" branch is usually permanent debt.
- Suggest concrete improvements for readability, maintainability, naming, and refactoring.

### 3. Architecture

Does the change fit the system's design?

- Does it follow existing patterns or introduce a new one? If new, is it justified?
- Does it maintain clean module boundaries?
- Is there code duplication that should be shared?
- Are dependencies flowing in the right direction (no circular dependencies)?
- Is the abstraction level appropriate (not over-engineered, not too coupled)?
- **Does this refactor reduce complexity or just relocate it?** Count the concepts a reader must hold to follow the change. If a "cleaner" version leaves that count unchanged, it isn't cleaner — prefer the restructuring that makes whole branches, modes, or layers disappear over one that re-centralizes the same logic. Prefer deleting an abstraction to polishing it.
- **Is feature-specific logic leaking into a shared or general-purpose module?** Keep logic in its owning layer, reuse the existing canonical helper instead of a near-duplicate, and don't normalize architectural drift.
- **Are type boundaries explicit?** Question gratuitous `any`/`unknown`/optional/casts and silent fallbacks that paper over an unclear invariant — making the boundary explicit often makes the surrounding control flow simpler.

### 4. Security

- Does the change introduce vulnerabilities?
- Is user input validated and sanitized?
- Are secrets kept out of code, logs, and version control?
- Is authentication/authorization checked where needed?
- Are SQL queries parameterized (no string concatenation)?
- Are non-SQL injection vectors handled (command/template/deserialization/path/query/header injection)?
- Are outputs encoded to prevent XSS?
- Is sensitive data exposure prevented in responses, logs, and error paths?
- Are dependencies from trusted sources with no known vulnerabilities?
- Is data from external sources (APIs, logs, user content, config files) treated as untrusted?
- Are external data flows validated at system boundaries before use in logic or rendering?

### 5. Performance

- Does the change introduce performance problems?
- Any N+1 query patterns?
- Any unbounded loops or unconstrained data fetching?
- Any synchronous operations that should be async?
- Any unnecessary re-renders in UI components?
- Any missing pagination on list endpoints?
- Any large objects created in hot paths?
- Any memory leaks or resource lifecycle issues?

### Any other issues?

- Identify any other issues not specifically listed above.

## Structural Remedies

When you flag a structural problem, propose the move — not just the problem. A review that only says "this is complex" leaves the author guessing. Reach for a named restructuring:

- **Replace a chain of conditionals** with a typed model or an explicit dispatcher.
- **Collapse duplicate branches** into a single clearer flow.
- **Separate orchestration from business logic** so each reads on its own.
- **Move feature-specific logic** out of a shared module into the package that owns the concept.
- **Reuse the canonical helper** instead of a bespoke near-duplicate.
- **Make a type boundary explicit** so downstream branching disappears.
- **Delete a pass-through wrapper** that adds indirection without clarifying the API.
- **Extract a helper, or split a large file** into focused modules.

Prefer the remedy that removes moving pieces over one that spreads the same complexity around.

## Review Process

### Step 1: Understand the Context

Read the spec or task description to understand the intent (if provided)

```
- What is this change trying to accomplish?
- What spec or task does it implement?
- What is the expected behavior change?
```

### Step 2: Review the Tests First

Tests reveal intent and coverage:

```
- Do tests exist for the change?
- Do they test behavior (not implementation details)?
- Are edge cases covered?
- Do tests have descriptive names?
- Would the tests catch a regression if the code changed?
```

### Step 3: Review the Implementation

- CRITICAL: Read surrounding source files and relevant related files needed to understand the context, not only diff hunks. This applies regardless of how low-risk a file looks - a diff hunk's context window can end right before a nearby unchanged block, making present code look deleted or absent. Skimming hunks instead of opening the full current file leads to failures.
- CRITICAL: For every call site, import, or script invocation in the diff, read the callee. "I cannot tell without reading X" is NEVER acceptable when X is in the project —> do read X.
- CRITICAL: For every changed public/exported API whose behavior changed, search the codebase for external callers/consumers outside the diff and verify they still hold under the new behavior. Do not assume every consumer is already reflected in the diff — a compatible-looking change can silently break an unchanged caller.
- CRITICAL: Do NOT read any ROAST files left over from prior runs (past review reports or diffs). Do NOT delete them either unless explicitly asked.
- CRITICAL: Do NOT read roast/README.md - this is for humans, not for you.
- CRITICAL: Do NOT dig through commit history and other unrelated branches: do NOT use `git log` or `git blame` or any other history command.
- CRITICAL: Walk through the code with the five axes in mind:

```
For each file changed:
1. Correctness: Does this code do what the test says it should?
2. Readability: Can I understand this without help?
3. Architecture: Does this fit the system?
4. Security: Any vulnerabilities?
5. Performance: Any bottlenecks?
```

## Severity Classification

| Severity                | Meaning           | Author Action                                                         |
|-------------------------|-------------------|-----------------------------------------------------------------------|
| **🔥 Critical**         | Blocks merge      | Must address immediately (security, data loss, broken functionality)  |
| **🔴 High / 🟡 Medium** | Required change   | Must address before merge                                             |
| **🔵 Low / ⚪ Nit**      | Minor, optional   | Author may ignore — formatting, style preferences                     |
| **✨ Suggestion**       | Recommendation    | Consider improvement (naming, code style, optional optimization) |
| **💡 FYI**              | Informational only | No action needed — context for future reference                       |

## Dead Code Hygiene

Check for orphaned code - identify code that is now unreachable or unused

## Handling Disagreements

When resolving review disputes, apply this hierarchy:

1. **Technical facts and data** override opinions and preferences
2. **Style guides** are the absolute authority on style matters
3. **Software design** must be evaluated on engineering principles, not personal preference
4. **Codebase consistency** is acceptable if it doesn't degrade overall health

## Honesty in Review

- **Don't soften real issues.** "This might be a minor concern" when it's a bug that will hit production is dishonest. Be thorough, harsh, and merciless. It is better to surface a finding that later gets filtered out than to silently drop a real bug. Your goal is maximum coverage.
- **Quantify problems when possible.** "This N+1 query will add ~50ms per item in the list" is better than "this could be slow."
- **Push back on approaches with clear problems.** Sycophancy is a failure mode in reviews. If the implementation has issues, say so directly and propose alternatives.
- **Don't praise.** Skip compliments on what was done good and focus solely on identifying issues.

## Dependency Discipline

Part of code review is dependency review:

**Before adding any dependency:**
1. Does the existing stack solve this? (Often it does.)
2. How large is the dependency? (Check bundle impact.)
3. Is it actively maintained? (Check last commit, open issues.)
4. Does it have known vulnerabilities? (e.g. `npm audit`)
5. What's the license? (Must be compatible with the project.)

**Rule:** Prefer standard library and existing utilities over new dependencies. Every dependency is a liability.

**Upgrading an existing dependency** is a code change like any other, and the riskiest upgrades are the ones merged in bulk with a message like "bump deps." Review them with the same discipline:

1. **Read the changelog, not just the version number.** Semver is a promise the maintainer may not have kept — a "patch" can carry a behavioral change. For a major bump, read the migration notes and find what breaks.
2. **One dependency per change.** Upgrade and merge them individually (or in small related groups). When a bulk bump breaks the build, you've lost which package did it; a single-package change makes the cause obvious and the revert clean.
3. **Let the tests decide.** The upgrade is verified by a green suite before *and* after, not by "it installed." If coverage around the dependency's behavior is thin, that gap is the real finding — add a test first.
4. **Mind the transitive graph.** Most installed packages are ones nobody chose directly. Review the lockfile diff, not just `package.json`; a single direct bump can pull in dozens of indirect changes.
5. **Keep the lockfile honest.** Commit it, review its diff, and never hand-edit it. The lockfile is the thing that actually pins what ships.

## The Review Checklist

```markdown
### Context
- [ ] I understand what this change does and why

### Correctness
- [ ] Change matches spec/task requirements
- [ ] Edge cases handled
- [ ] Error paths handled
- [ ] Tests cover the change adequately

### Readability
- [ ] Names are clear and consistent
- [ ] Logic is straightforward
- [ ] No unnecessary complexity

### Architecture
- [ ] Follows existing patterns
- [ ] No unnecessary coupling or dependencies
- [ ] Appropriate abstraction level
- [ ] Refactors reduce complexity rather than relocate it
- [ ] No feature logic in shared modules; file stays within a healthy size

### Security
- [ ] No secrets in code
- [ ] Input validated at boundaries
- [ ] No injection vulnerabilities
- [ ] Auth checks in place
- [ ] External data sources treated as untrusted

### Performance
- [ ] No N+1 patterns
- [ ] No unbounded operations
- [ ] Pagination on list endpoints
```

## Red Flags

- Security-sensitive changes without security-focused review
- No regression tests with bug fix PRs
- Review comments without severity labels — makes it unclear what's required vs optional
- Accepting "I'll fix it later" — it never happens
- A refactor that moves code around without reducing the number of concepts
- A change that grows an already-large file instead of decomposing it
- New conditionals scattered into unrelated code paths (a missing abstraction)
- A bespoke helper that duplicates an existing canonical one, or feature logic placed in a shared module

**Presumptive blockers:** surface and propose the simpler design for each of these; escalate to Required only when the change actively makes structure worse: a refactor that relocates complexity instead of reducing it; a change that pushes a file past the size boundary with no decomposition; feature logic added to a shared module; a near-duplicate of an existing canonical helper; a silent fallback that hides an unclear invariant.

## Review Output Template

- Append to the review report as you go, do NOT wait till the end to write the whole report. CRITICAL: Do NOT sort by severity. List issues in the exact chronological order you find them.
- Use the following structured format for each finding.
- Use headings for issue headers.
- Use dashes for bullet points within each issue.

```markdown
# <Change Title>

## Issue #1: <Issue Title>
- **Severity:** [🔥 Critical / 🔴 High / 🟡 Medium / 🔵 Low / ⚪ Nit / ✨ Suggestion / 💡 FYI ]
- **Confidence:** [Your confidence level in this finding]
- **Files:**
    - [<Full file path1>#<function>]
    - [<Full file path2>#<function>]
    - ...
- **Description:** [Description of the issue]
- **How to Fix:** [Specific fix recommendation]


## Issue #2: <Issue Title>
...


```

# Execution

**CRITICAL - MANDATORY STEPS - Execute all instructions - DO NOT SKIP:**

1. Sync remote branches: execute `git fetch`.
2. Determine `BASE_BRANCH`: if the user specified a base branch (e.g. using words like "against"/"vs"/"compare"/"base"/etc.), then use the specified branch name. Otherwise, use `HEAD` (resolves to the remote's default branch via the `origin/HEAD` symbolic ref), e.g. `git --no-pager diff origin/HEAD...HEAD`. If the current branch is the default branch then BASE_BRANCH='release' (meaning you will review the changes made in the default branch against the 'release' branch - do NOT attempt to swap branches the other way around). If the 'release' branch does not exist or if it unclear what to review against, ask the user for the base branch to review against.
3. Determine the `yyyyMMdd-HHmmss` timestamp - get the actual current date and time by running a shell command - do not infer or guess.
4. Determine `DIFF_FILE` as `ROAST-{yyyyMMdd-HHmmss}-{id-title}.diff` — replace `yyyyMMdd-HHmmss` with the timestamp.
5. Replace `BASE_BRANCH` and `DIFF_FILE` in this command and execute: `git --no-pager diff origin/BASE_BRANCH...HEAD --shortstat && git --no-pager diff origin/BASE_BRANCH...HEAD --numstat -p > DIFF_FILE`.
6. Check diff size: if the changed line count (insertions + deletions from `--shortstat`) exceeds 5000, or `DIFF_FILE`'s byte size exceeds 1MB (e.g. `wc -c < DIFF_FILE`),
   then print a warning right away, before reading the full diff
   (can potentially truncate your context window, and you forget instructions):
  `⚠️ Large diff (N changed lines, M bytes) - review quality may degrade; consider using a large-context model and/or narrowing the diff.`
   Then continue the review regardless.
7. Read the diff file in full (using chunked reads if large). Do NOT use truncated/filtered/grepped reads as a substitute for full diff inspection. You MUST read the full diff yourself before reviewing.

**CRITICAL: Always review the entire diff directly in this thread. Never delegate review work to sub-agents and never split the diff across multiple agents, regardless of diff size.** A fragmented review structurally cannot see interactions between separately-reviewed files (e.g. a changed API and a distant, unreviewed caller of it) — it produces a report that looks complete while carrying a higher miss rate than a single reviewer holding the whole diff.

## Artifact Finalization

CRITICAL: MANDATORY STEPS - After review is complete, finalize artifacts:

1. **Context Check:** If your conversation was truncated during the review process, you MUST re-read this entire skill file to restore exact templates and guidelines before proceeding. Do not guess the format.
2. Create an MD file at the project root with the review results. Determine `short-change-title` (one-three words) and name the report file `ROAST-{yyyyMMdd-HHmmss}-{id-short-change-title}.md` using same timestamp as diff file. Do NOT output the review directly in the chat.
3. Check if `.git/info/exclude` already contains `ROAST-*` and if not, append it:
- bash: `grep -qxF 'ROAST-*' .git/info/exclude || echo 'ROAST-*' >> .git/info/exclude`
- PowerShell: `if (-not (Select-String -Path ".git\info\exclude" -Pattern "^ROAST-\*$" -Quiet)) { Add-Content -Path ".git\info\exclude" -Value "ROAST-*" }`
4. Un-track artifacts from git in case the IDE automatically staged them - use `git rm --cached --ignore-unmatch` for the diff file and the report file.

## Post review

- Once the review report is created, it is READ-ONLY - do NOT update it further in the conversation.
- Always refer to the issues by their absolute numbers - never renumber.
- Do not waste tokens on repeating outstanding unfixed items further in the conversation.

---
