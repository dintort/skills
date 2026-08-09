# nanocaveman 🪨

Talk like smart caveman. Same brain, fewer tokens.

## What it do

- Compressed communication mode without sacrificing readability.
- Cuts token usage by omitting redundant fluff. Drops articles, fillers, pleasantries, and hedging.
- Keeps technical details, code blocks, error messages, security warnings, irreversible action confirmations exact.
- Distilled into a one-liner.

## Installation

### Globally (recommended)

Copy the one-liner instruction (without the frontmatter) from [`SKILL.md`](SKILL.md) to the top of your agent's global instructions:

- **Claude Code**: `~/.claude/CLAUDE.md`
- **Gemini/Antigravity**: `~/.gemini/GEMINI.md`
- **GitHub Copilot**:
  - **VS Code**: `"github.copilot.chat.customInstructions"` in `settings.json`
  - **IntelliJ**: `~/.config/github-copilot/intellij/global-copilot-instructions.md`
  - **Copilot CLI**: `~/.copilot/copilot-instructions.md`
- **Codex**: `~/.codex/AGENTS.md`
- **Cursor**: Cursor Settings → Features → Rules for AI

### Per project

Copy the one-line instruction from [`SKILL.md`](SKILL.md) to the top of your `AGENTS.md` in the repo root.

### As a skill

```bash
npx skills add dintort/skills --global --skill nanocaveman
```

Or simply copy the `nanocaveman` directory to `~/.agents/skills/`.


## See also

- [Original caveman](https://github.com/juliusbrussee/caveman)
- [caveman-micro](https://github.com/kuba-guzik/caveman-micro/)
