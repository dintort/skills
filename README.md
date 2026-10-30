# Skills

A collection of useful skills for AI agents:

- **[nanocaveman](skills/nanocaveman/README.md)**: Compressed communication mode. Cuts token usage by omitting redundant fluff.
- **[omg](skills/omg/README.md)**: Origin → Mechanism → Guardrail. Explains the root cause of a process mistake and proposes a persistent prevention guardrail.
- **[roast](skills/roast/README.md)**: Merciless multi-axis code review on the current branch against the default remote branch or a specified base branch, tag, or commit. Checks for bugs, security vulnerabilities, performance issues, and more. Generates a Markdown report.

## Installation

```bash
npx skills add dintort/skills --global
```

### Plugin marketplace

claude.ai / Claude Desktop: Customize → Plugins → Add → Add marketplace → `dintort/skills`, enable "Sync automatically", install plugins.

Claude Code, all skills:

```
/plugin install all --marketplace dintort/skills
```

Claude Code, single skills:

```
/plugin install nanocaveman --marketplace dintort/skills
/plugin install omg --marketplace dintort/skills
/plugin install roast --marketplace dintort/skills
```
