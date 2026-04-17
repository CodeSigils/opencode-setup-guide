```
╔═══════════════════════════════════════════════════════════════════════╗
║                          AWESOME OPENCODE                             ║
║              A Production-Ready OpenCode Configuration                ║
╚═══════════════════════════════════════════════════════════════════════╝
```

# OpenCode Setup Guide

A comprehensive, production-ready OpenCode configuration that demonstrates 
some of the amazing OpenCode's capabilities.

## Features

This setup includes:

- **17 Persistent Directives** - Rules that persist across sessions
- **Custom Skills** - Reusable instruction bundles
- **Memory Plugin** - Semantic memory for AI context
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

### Skills
- code-checklist - Your personal rules
- context7-docs - Official docs lookup
- github-ops - GitHub operations
- security-auditor - Security scanning
- And more from chandima/config

### Documentation
- `docs/opencode-plugins-setup.md` - Complete setup guide
- `docs/opencode-skills.md` - Skills guide
- `SETUP.md` - Step-by-step installation

## Requirements

- OpenCode installed
- Node.js 18+
- ripgrep (optional, for better search)

## Documentation

See the `docs/` directory for:
- Complete setup instructions
- Skills usage guide
- Directives reference
- Best practices guide

## Credits

- Built with [oh-my-opencode](https://github.com/code-yeongyu/oh-my-opencode)
- Skills from [chandima/opencode-config](https://github.com/chandima/opencode-config)
- Methodology from [obra/superpowers](https://github.com/obra/superpowers)

## License

MIT