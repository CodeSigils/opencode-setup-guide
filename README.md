![OpenCode](https://www.docker.com/app/uploads/2026/01/OpenCode-DMR-figure-1-1.png)

# OpenCode Practical Setup Guide

A hands-on guide for configuring OpenCode as your AI coding assistant. This repo provides a production-ready configuration with persistent directives, custom skills, and best practices.

> ⚠️ This repository is for **learning purposes** and will be **updated continuously** as I explore more OpenCode features.

**Last Updated**: April 2026

---

## Quick Start

```bash
# Clone this repo
git clone https://github.com/YOUR_USERNAME/opencode-setup-guide.git ~/opencode-setup

# Link configuration
ln -sf ~/opencode-setup/.opencode/opencode.json ~/.config/opencode/opencode.json
ln -sf ~/opencode-setup/.opencode/oh-my-openagent.json ~/.config/opencode/oh-my-openagent.json
ln -sf ~/opencode-setup/.opencode/directives.md ~/.config/opencode/directives.md

# Link skills
ln -s ~/opencode-setup/skills/* ~/.config/opencode/skills/
```

See [SETUP.md](SETUP.md) for detailed installation steps.

---

## What's Included

| Component | Description |
| :--- | :--- |
| **17 Persistent Directives** | Rules that apply to every session |
| **Custom Skills** | Reusable instruction bundles |
| **Memory Plugin** | Semantic memory for AI context |
| **Agent Configurations** | Optimized models for different tasks |
| **Best Practices Guide** | Avoid common AI coding mistakes |

---

## Key Concepts

### Agents vs Categories

OpenCode uses two delegation systems:

**Agents** - Specialized AI personalities:
- **Sisyphus** - Main orchestrator (most tasks)
- **Oracle** - Debugging, architecture
- **Librarian** - Documentation lookup
- **Explore** - Codebase search

**Categories** - Task-type classifiers:
- `visual-engineering` - UI/UX, frontend
- `ultrabrain` - Hard logic, algorithms
- `deep` - Research-heavy tasks
- `quick` - Simple edits

### Plugins

Plugins extend functionality. Just add to config - no `npm install` needed:

| Plugin | Purpose |
| :--- | :--- |
| `oh-my-openagent` | Core enhancements, custom agents |
| `opencode-mem` | Semantic memory, search past sessions |

### Directives vs Skills

| | Directives | Skills |
| :--- | :--- | :--- |
| **Load timing** | Every session (via prompt_append) | On-demand |
| **Scope** | General rules | Specific workflows |
| **Purpose** | Universal rules (~300 lines max) | Team conventions |

---

## Documentation Guide

| Guide | Description | When to Read |
| :--- | :--- | :--- |
| [docs/setup.md](docs/setup.md) | Complete setup (plugins, models, directives) | Start here |
| [docs/agents.md](docs/agents.md) | Agent configuration & permissions | After setup |
| [docs/skills.md](docs/skills.md) | Skills system guide | Want to customize |
| [docs/best-practices.md](docs/best-practices.md) | 10 AI anti-patterns to avoid | Before starting work |
| [docs/webui.md](docs/webui.md) | Web UI, desktop app, Docker | Prefer browser |

### Learning Path (Recommended Order)

1. **docs/setup.md** → Set up your environment
2. **docs/agents.md** → Customize agent behavior
3. **docs/skills.md** → Add team conventions
4. **docs/best-practices.md** → Learn from AI mistakes
5. **docs/webui.md** → Optional, for browser access

---

## Requirements

- OpenCode installed
- Node.js 18+
- ripgrep (optional, better search)

---

## Important Disclaimers

### No Warranty

All content is provided **as-is** without any warranty:

- **Data Loss**: Verify backups before running commands
- **System Damage**: Test in development first
- **Security Issues**: Review code before execution
- **Financial Costs**: Check API pricing

### Your Responsibility

- Verify commands against official documentation
- Understand before running code
- Back up data before system changes
- Use your judgment - what works for me may not work for you

---

## Credits

- Built with [oh-my-opencode](https://github.com/code-yeongyu/oh-my-opencode)
- Skills from [chandima/opencode-config](https://github.com/chandima/opencode-config)
- Methodology from [obra/superpowers](https://github.com/obra/superpowers)

## Sources

- [OpenCode + Docker Model Runner: Private AI Coding](https://www.docker.com/blog/opencode-docker-model-runner-private-ai-coding/)

## License

MIT