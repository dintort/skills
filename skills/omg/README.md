# omg - Origin → Mechanism → Guardrail

Protocol for corrections on process mistakes (a violated instruction, a skipped step, a broken rule).
Instead of apologizing or blaming attention/discipline, the agent:

1. **Origin** - identifies what went wrong.
2. **Mechanism** - states the root cause of the violation.
3. **Guardrail** - proposes an edit to a persistent artifact (instructions file, code, config)
   that makes the mistake structurally harder to repeat, then asks you what to apply.

The task in progress is abandoned and is not resumed until you explicitly say so,
to make sure the agent is solely focused on the OMG protocol.

## Installation

```bash
npx skills add dintort/skills --global --skill omg
```

Or simply copy the `omg` directory to your agents' skills at `~/.agents/skills/`.

## Usage

```
/omg missing log CancellationException
```
