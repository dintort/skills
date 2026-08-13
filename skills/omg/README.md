# omg - Origin → Mechanism → Guardrail

Protocol for corrections on process mistakes (a violated instruction, a skipped step, a broken rule).
Instead of apologizing or blaming attention/discipline, the agent:

1. Identifies what went wrong.
2. States the root cause of the violation.
3. Proposes a `Fix` and a `Prevention` edit to a persistent artifact (instructions file, code, config)
   that makes the mistake structurally harder to repeat.

## Installation

```bash
npx skills add dintort/skills --global --skill omg
```

Or simply copy the `omg` directory to your agents' skills at `~/.agents/skills/`.

## Usage

```
/omg missing log CancellationException
```
