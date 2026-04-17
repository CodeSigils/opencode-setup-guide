![OpenCode](https://www.docker.com/app/uploads/2026/01/OpenCode-DMR-figure-1-1.png)

# OpenCode Setup GUide

> ⚠️ This repository is for **learning purposes** and will be **updated continuously** as I explore more OpenCode features.

**Last Updated**: April 2026

---

A comprehensive, production-ready OpenCode configuration that demonstrates 
some of the amazing OpenCode's capabilities.

## Features

This setup includes:

- **17 Persistent Directives** - Rules that persist across sessions
- **Custom Skills** - Reusable instruction bundles
- **Memory Plugin** - Semantic memory for AI context
- **Anti-Patterns Guide** - 10 AI patterns to avoid
- **Best Practices** - Community-approved patterns
- **Comprehensive Guides** - Full documentation

## Quick Start

```bash
# Clone this repo
git clone https://github.com/YOUR_USERNAME/opencode-setup-guide.git ~/opencode-setup

# Link to your config
ln -s ~/opencode-setup/.opencode ~/.config/opencode/my-setup
ln -s ~/opencode-setup/skills/* ~/.config/opencode/skills/
```

## What's Included

### Configuration
- Plugin list (opencode-mem, oh-my-openagent)
- Model configurations (big-pickle, gpt-5-nano)
- 17 persistent directives
- Sisyphus settings
- Permission configuration

### Skills
- code-checklist - Your personal rules
- context7-docs - Official docs lookup
- github-ops - GitHub operations
- security-auditor - Security scanning
- And more from chandima/config

### Documentation (Enhanced)
- `index.md` - Documentation hub with learning path
- `docs/setup.md` - Comprehensive setup guide (~950 lines)
- `docs/agents.md` - Agent configuration & permissions
- `docs/skills.md` - Skills system guide
- `docs/best-practices.md` - AI anti-patterns & patterns
- `docs/webui.md` - Web UI access guide

## Requirements

- OpenCode installed
- Node.js 18+
- ripgrep (optional, for better search)

## Documentation

See the `docs/` directory and `index.md` for:

| Guide | Description |
| :--- | :--- |
| [index.md](index.md) | Documentation hub - start here |
| [docs/setup.md](docs/setup.md) | Comprehensive setup guide (~950 lines) |
| [docs/agents.md](docs/agents.md) | Agent configuration & permissions |
| [docs/skills.md](docs/skills.md) | Skills system guide |
| [docs/best-practices.md](docs/best-practices.md) | AI anti-patterns & patterns |
| [docs/webui.md](docs/webui.md) | Web UI access guide |
| [SETUP.md](SETUP.md) | Step-by-step installation |

## Important Disclaimers

### No Warranty

All content is provided **as-is** without any warranty. Specifically:

- **Data Loss**: Verify backups before running commands
- **System Damage**: Test in development environments first
- **Security Issues**: Review all code before execution
- **Financial Costs**: Check API pricing before use

### Your Responsibility

**Reader Beware**: As a reader, you must:

- **Verify Commands**: Double-check all commands against official documentation
- **Understand Before Running**: Don't run code you don't understand
- **Back Up Data**: Always have backups before system changes
- **Check Sources**: Cross-reference with official docs
- **Use Your Judgment**: What works for me might not work for you

### Personal Opinions

All views expressed here are personal opinions based on:

- Individual experimentation
- Specific use cases
- Limited testing
- My own environment

Your experience may differ. Always verify against your own research.

---

## Credits

- Built with [oh-my-opencode](https://github.com/code-yeongyu/oh-my-opencode)
- Skills from [chandima/opencode-config](https://github.com/chandima/opencode-config)
- Methodology from [obra/superpowers](https://github.com/obra/superpowers)

## License

MIT