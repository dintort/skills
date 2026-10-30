# omg - Origin → Mechanism → Guardrail

Protocol for corrections on process mistakes (a violated instruction, a skipped step, a broken rule).
Instead of apologizing or blaming attention/discipline, the agent:

1. **Origin** - pinpoints where it went wrong.
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

### Plugin marketplace

claude.ai / Claude Desktop: Customize → Plugins → Add → Add marketplace → `dintort/skills`, enable "Sync automatically", install `omg` plugin.

Claude Code:

```
/plugin install omg --marketplace dintort/skills
```

## Usage

```
/omg missing log CancellationException
```

> ⚠️ If installed as a plugin, the skill is prefixed with the plugin name: `/omg:omg`, or `/dintort:omg` via the all-skills `dintort` plugin.
