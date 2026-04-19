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

This guide includes the **oh-my-openagent** plugin - a popular enhancement that adds powerful custom agents to OpenCode.

| Component | Description |
| :--- | :--- |
| **oh-my-openagent** | Plugin with custom agents (Sisyphus, Oracle, Librarian, etc.) |
| **17 Persistent Directives** | Rules that apply to every session |
| **Custom Skills** | Reusable instruction bundles |
| **Memory Plugin** | Semantic memory for AI context |
| **Agent Configurations** | Optimized models for different tasks |
| **Best Practices Guide** | Avoid common AI coding mistakes |

### What is oh-my-openagent?

[oh-my-openagent](https://github.com/code-yeongyu/oh-my-opencode) (25k+ GitHub stars) extends OpenCode with specialized agents:

| Agent | Purpose |
| :--- | :--- |
| **Sisyphus** | Main orchestrator - breaks down tasks, delegates to subagents |
| **Oracle** | Debugging, architecture consultation |
| **Librarian** | Documentation lookup, research |
| **Explore** | Codebase search, pattern finding |
| **Metis** | Pre-planning analysis |
| **Momus** | Plan review and critique |

### Open-Weight Models - No API Keys Needed

This configuration uses **free open-weight models** from OpenCode Zen - no paid API keys required:

| Model | Purpose | Benchmark |
| :--- | :--- | :--- |
| **minimax-m2.5-free** | Primary reasoning (Sisyphus default) | 6.3% error rate, best accuracy |
| **big-pickle** | Complex reasoning (Oracle default) | 12.3% error rate, deep analysis |
| **gpt-5-nano** | Quick tasks (fallback, built-in) | N/A |

**Model fallbacks** (configured per agent):
- **Sisyphus** (main orchestrator): minimax-m2.5-free → big-pickle → gpt-5-nano
- **Oracle** (debugging/architecture): big-pickle → minimax-m2.5-free
- **Librarian/Explore** (search): big-pickle → gpt-5-nano → minimax-m2.5-free

Based on [LLM benchmarks](https://www.glukhov.org/ai-devtools/opencode/llms-comparison/), minimax-m2.5-free is the most accurate free model (6.3% error rate).

**When to use each model:**
- **minimax-m2.5-free** - Complex reasoning, planning, implementation
- **big-pickle** - Architecture decisions, debugging, deep analysis
- **gpt-5-nano** - Quick lookups, simple fixes, fast exploration

**Why free models?**
- Free to use - no API costs
- Privacy - code stays local
- No vendor lock-in
- Self-hosting supported

---

## Key Concepts

### oh-my-openagent Agents vs Categories

OpenCode uses two delegation systems. The agents below come from the **oh-my-openagent** plugin (included in this setup):

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
- markdownlint-cli (for linting)

### Quality Assurance

This repo includes two ways to lint markdown files:

**1. On-demand with skill:**
```bash
npx markdownlint "**/*.md"
```

**2. Pre-commit hook:**
The `.git/hooks/pre-commit` automatically lints markdown files on commit. Bypass with:
```bash
git commit --no-verify
```

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

### Official

- [OpenCode Website](https://opencode.ai) - Main site with downloads
- [OpenCode Documentation](https://dev.opencode.ai/docs) - Official docs
- [OpenCode GitHub](https://github.com/anomalyco/opencode) - Source code
- [OpenCode Download](https://opencode.ai/download) - Desktop apps

### Tutorials & Guides

- [OpenCode Tutorial - Getting Started](https://opencode-tutorial.com/en/getting-started) - Your first commands
- [OpenCode Tutorial 2026](https://www.nxcode.io/resources/news/opencode-tutorial-2026) - Complete setup guide
- [OpenCode Setup Guide - ComputingForGeeks](https://computingforgeeks.com/setup-opencode-ai-coding-agent/) - Full reference (TUI, agents, MCP, LSP)
- [The Definitive Guide to OpenCode](https://reading.sh/the-definitive-guide-to-opencode-from-first-install-to-production-workflows-aae1e95855fb) - Comprehensive guide by JP Caparas
- [FreeCodeCamp: Integrate AI into Terminal](https://www.freecodecamp.org/news/integrate-ai-into-your-terminal-using-opencode/)
- [Custom Agents Guide](https://docs.bswen.com/blog/2026-03-30-opencode-custom-agents/)

### Community

- [OpenCode + Docker Model Runner](https://www.docker.com/blog/opencode-docker-model-runner-private-ai-coding/)
- [OpenCode LLMs Comparison](https://www.glukhov.org/ai-devtools/opencode/llms-comparison/)
- [Community Docs (open-code.ai)](https://open-code.ai) - Unofficial docs
- [Beginner Guide](https://help.apiyi.com/en/opencode-ai-coding-agent-beginner-guide-2026-en.html)
- [OpenCode vs Claude Code vs Cursor vs Windsurf](https://www.shareuhack.com/en/posts/cursor-vs-claude-code-vs-windsurf-2026) - Comparison guide
- [awesome-opencode](https://github.com/awesome-opencode/awesome-opencode) - Curated resources

## License

MIT