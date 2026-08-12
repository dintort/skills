---
name: omg
description: Origin → Mechanism → Guardrail. Use when the user corrects a process mistake - keywords "omg", "wtf", "bullshit", or any criticism of what you did. Explain the root cause and propose a persistent prevention.
---

# omg - Origin → Mechanism → Guardrail

Triggered by a correction on process (a violated instruction, a skipped step, a broken rule).
Signals: "omg", "wtf", "bullshit", or any other criticism on how the work was approached.

## Steps

1. **Origin** - Pinpoint WHERE it went wrong, in one line max: a short pointer to the
    violated rule (which file + a few identifying words: a pointer, not a recitation) or,
    if no existing rule covers the case, that gap is the origin.
2. **Mechanism** - State the specific, mechanical root cause of the violation
    (e.g. a missing check, a missing/ambiguous/insufficient rule, a wrong assumption, an ordering issue).
    - Do NOT apologize.
    - Do NOT restate what you did.
    - Do NOT frame it as a lapse in some human trait (laziness, discipline, muscle-memory).
    - Explain the actual mechanical reason WHY it happened.
    - Name your own driver - what you optimized for - before (or in addition to) blaming a rule gap.
3. **Guardrail** - Propose an edit to a persistent artifact (instructions file, code, config)
    that structurally prevents further occurrences of the mistake.
   - The prevention suggestion must contain the exact fix entirely, not just a description of the fix.
   - Output the suggestion on its own line, prefixed `**Prevention:**` followed by the suggested prevention text.
   - A conclusion "no edit needed as there's already a rule for this" is not valid - if the violation happened
     despite the existing rule, the rule is clearly insufficient; propose a sharper one.
   - Write the rule as terse directive text - the operative instruction only, max ~2 lines.
     No rationale, no examples, no enumerations: they cost context every future turn and get
     misread as scope limits. Rationale goes in the chat reply, never in the artifact.
   - The prevention rule must be general to address the underlying root cause.
     Do not hard-code a fix for the specific instance that failed,
     as it is not viable to hard-code all possible ways to screw up.
   - Route the edit: a lesson about behavior/process → global instructions;
     a lesson specific to the current project's code/stack → project instructions.
   - When the correction's fix is a code/config edit, you MUST still provide the PREVENTION suggestion.
   - Abandon the previous task in progress - do NOT attempt to immediately fix the violation -
     your focus now is preventing its future occurrences.
   - Do not resume the previous task until requested by the user once this protocol completes.
   - Finish by asking the user what to apply.
     Make sure to print the suggestions BEFORE asking the question.
     Use the `ask_user` tool if available, otherwise ask plainly.
     Include at least these options, reworded as fits, plus any others that apply:
     - Apply the suggested guardrail.
     - Apply the fix to the violation.
     - Apply both.
     - Apply neither.
 