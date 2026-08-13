---
name: omg
description: Origin → Mechanism → Guardrail. Use when the user corrects a process mistake - keywords "omg", "wtf". Explain the root cause and propose a persistent prevention.
---

# omg - Origin → Mechanism → Guardrail

Triggered by a correction on process (a violated instruction, a skipped step, a broken rule).
Signals: "omg", "wtf".

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
    - A conclusion "no edit needed as there's already a rule for this" is not valid - if the violation happened
      despite the existing rule, the rule is clearly insufficient; propose a sharper one.
    - Write the rule as terse directive text - the operative instruction only, max ~2 lines.
      No rationale, no examples, no enumerations: they cost context every future turn and get
      misread as scope limits. Rationale goes in the chat reply, never in the artifact.
    - The prevention rule must be general to address the underlying root cause.
      Do not hard-code a fix for the specific instance that failed,
      as it is not viable to hard-code all possible ways to screw up.
    - Route the edit: a lesson about behavior/process → global instructions;
      a lesson specific to the current project's code/stack → project instructions;
      a lesson about specific skill/prompt/tool/code/etc. → ask user if it should be applied to the source.
    - When the correction's fix is a code/config edit, you MUST still provide the PREVENTION suggestion.
    - Abandon the previous task in progress - do NOT attempt to immediately fix the violation:
      your SOLE focus now is preventing its future occurrences.
        - CRITICAL: Do not return the previous task until EXPLICITLY requested by the user once this protocol completes.
          User's correction to the suggested prevention measure IS NOT a completion of the protocol:
          you MUST keep working on the prevention measure until confirmed by user.
    - Output the suggestions formatted as:
      ```
      **Fix**
 
      - Where: <where it shoud be applied>
      - What: <exact content of the fix (not just summary)>
      
      **Prevention**
 
      - Where: <where it shoud be applied>
      - What: <exact content of the prevention measure (not just summary)>
      ```
    - Finish by asking the user what to apply.
      Make sure to print the suggestions BEFORE asking the question.
      Use the `ask_user` tool if available, otherwise ask plainly.
      Include at least these options, reworded as fits, plus any others that apply:
        - Apply the suggested guardrail.
        - Apply the fix to the violation.
        - Apply both.
        - Apply neither.
 