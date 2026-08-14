# omg - Origin → Mechanism → Guardrail

Protocol for corrections on process mistakes (a violated instruction, a skipped step, a broken rule).
Instead of apologizing or blaming attention/discipline, the agent:

1. Stops the task in progress and does not resume it until you say so,
   to make sure it is solely focused on the OMG protocol.
2. Identifies what went wrong.
3. States the root cause of the violation.
4. Proposes a `Guardrail` edit to a persistent artifact (instructions file, code, config)
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
