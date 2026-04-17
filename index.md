# OpenCode Documentation

Welcome to the OpenCode documentation hub - a comprehensive learning resource for mastering OpenCode as your AI coding assistant.

---

## What is OpenCode?

OpenCode is an autonomous AI coding agent that helps you write, debug, and maintain code. Unlike traditional coding assistants that require constant guidance, OpenCode is designed to work **independently** while following your team's conventions and best practices.

### Why OpenCode Differs from Other AI Tools

| Feature | Traditional AI Assistant | OpenCode |
| :--- | :--- | :--- |
| **Autonomy** | Requires constant prompts | Executes tasks independently |
| **Context** | Limited to single file | Searches entire codebase |
| **Tool Access** | Restricted set | Full tool suite (LSP, grep, AST, etc.) |
| **Session** | Stateless | Preserves context across sessions |
| **Customization** | Minimal | Highly configurable agents & skills |

### Core Philosophy

OpenCode follows the **"roll your boulder"** philosophy - like Sisyphus pushing the boulder up the hill, you work alongside OpenCode daily. It doesn't just generate code; it **reasons** about your codebase, follows your patterns, and delivers production-ready solutions.

---

## Key Concepts

Before diving into the guides, understand these fundamental concepts:

### 1. Agents vs Categories

OpenCode uses two complementary delegation systems:

**Agents** are specialized AI personalities for specific tasks:
- **Sisyphus** - Main orchestrator, handles most work
- **Oracle** - Debugging, architecture consultation
- **Librarian** - Documentation lookup, research
- **Explore** - Codebase search, pattern finding
- **Metis** - Pre-planning analysis
- **Momus** - Plan review and critique

**Categories** are task-type classifiers that route work to appropriate models:
- `visual-engineering` - UI/UX, frontend work
- `ultrabrain` - Hard logic, complex algorithms
- `deep` - Research-heavy autonomous tasks
- `quick` - Simple, trivial edits
- `artistry` - Creative problem-solving

### 2. Plugins Extend Functionality

OpenCode's plugin system allows you to extend core capabilities:

| Plugin | Purpose | Why It Matters |
| :--- | :--- | :--- |
| `oh-my-openagent` | Core enhancements | Adds custom agents, better prompts |
| `opencode-mem` | Semantic memory | Search past sessions, learn context |
| Community plugins | Various | Add specialized capabilities |

**Key Insight**: Plugins auto-download from npm. You don't run `npm install` - just add to config.

### 3. Skills Are On-Demand Instructions

Skills are reusable instruction bundles that encode team conventions. Unlike directives (always loaded), skills load **only when needed**.

Think of skills as **onboarding documents** for your AI - they tell OpenCode how your team works.

### 4. Directives vs Reference Docs

**Directives** (~300 lines) persist in every session via `prompt_append`. They're rules OpenCode follows always.

**Reference docs** (this entire set) are searched on-demand via grep or memory. They shouldn't be in prompt_append - too long, waste tokens.

### 5. Memory Plugin for Context

`opencode-mem` provides semantic search across your working history. It remembers:
- How you solved similar problems
- Your team's patterns
- Past decisions and their outcomes

---

## Quick Navigation

| Topic | Description | When to Read |
| :--- | :--- | :--- |
| **[Setup Guide](./setup.md)** | Comprehensive setup with plugins, models, directives | Start here for full configuration |
| **[Agent Configuration](./agents.md)** | Custom agent models, permissions, best practices | After setup - customize your AI |
| **[Skills System](./skills.md)** | Reusable instruction bundles | Want to encode team conventions? |
| **[Best Practices](./best-practices.md)** | Avoid common AI coding mistakes | Learn from others' mistakes |
| **[Web Access](./webui.md)** | Web UI, desktop app, Docker | Prefer browser interface? |

---

## The OpenCode Ecosystem

```
┌─────────────────────────────────────────────────────────┐
│                    OpenCode Core                        │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  │
│  │  Agents │  │   LSP   │  │  Tools  │  │ Memory  │  │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘  │
└─────────────────────────────────────────────────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
┌───────────────┐ ┌───────────────┐ ┌───────────────┐
│ oh-my-openagent│ │ opencode-mem  │ │   Skills      │
│ (Plugin)       │ │ (Plugin)      │ │ (On-demand)   │
└───────────────┘ └───────────────┘ └───────────────┘
```

### Layer Architecture

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| Core | OpenCode | Base AI coding agent |
| Plugin | oh-my-openagent | Enhanced agents & prompts |
| Process | superpowers (optional) | Workflow methodology |
| Rules | directives.md | Persistent rules |
| Memory | opencode-mem | Semantic search |

---

## Who Should Use These Docs?

### Primary Audience
- **Developers** switching from Cursor/Windsurf to OpenCode
- **Teams** wanting to standardize AI coding practices
- **Power users** seeking to customize their setup

### What You'll Learn
1. How OpenCode differs from other AI assistants
2. Complete installation and configuration
3. Customizing agents for your workflow
4. Building skills for team conventions
5. Avoiding common AI coding mistakes
6. Advanced features like memory and web UI

---

## Documentation Map

```
OpenCode Docs
├── index.md               - This file (overview & navigation)
├── setup.md               - Comprehensive setup (plugins, models, directives, memory)
├── agents.md              - Agent configuration & permissions
├── skills.md              - Skills system (create & use)
├── best-practices.md      - AI anti-patterns & patterns (learning resource)
└── webui.md               - Web access options
```

---

## Learning Path

For new users, we recommend this order:

```
1. index.md (this file)    → Understand concepts
   ↓
2. setup.md                → Set up your environment
   ↓
3. agents.md               → Customize agent behavior
   ↓
4. skills.md               → Add team conventions
   ↓
5. best-practices.md       → Learn from mistakes
   ↓
6. webui.md                → Explore access options (optional)
```

---

## Related Resources

### Official
- [OpenCode Documentation](https://dev.opencode.ai/docs/) - Official docs
- [OpenCode Download](https://opencode.ai/download) - Desktop apps

### Community
- [awesome-opencode](https://github.com/awesome-opencode/awesome-opencode) - Curated resources
- [OhMyOpenCode](https://github.com/code-yeongyu/oh-my-openagent) - Core plugin (25k+ stars)
- [superpowers](https://github.com/obra/superpowers) - Workflow methodology (157k+ stars)

### Support
- [Reddit Community](https://www.reddit.com/r/opencodeCLI/) - Ask questions
- [GitHub Issues](https://github.com/anomalyco/opencode/issues) - Bug reports

---

## Contributing to These Docs

These docs live in `~/Notes/opencode/`. To contribute:

1. Edit the relevant `.md` file
2. Follow the style guide:
   - Use GFM tables with `| :--- |` for headers
   - Break lines at ~80 characters
   - Include practical examples
   - Add references where relevant

---

*Last updated: April 2026*