# OpenCode Agent Configuration Guide

## Overview

This guide covers agent configuration in OpenCode - how to customize AI models, permissions, and behavior for different tasks. It's part of the [Setup Guide](./setup.md) but provides deeper detail on agent-specific settings.

> **Prerequisite**: This guide assumes you've completed basic setup from the [Setup Guide](./setup.md). If you haven't, start there to understand the configuration file structure.

---

## Why Configure Agents?

OpenCode's power lies in its **agent specialization**. Instead of one AI doing everything, different agents handle different tasks:

| Benefit | Explanation |
| :--- | :--- |
| **Expertise** | Specialized models for debugging vs. research |
| **Cost control** | Small models for simple tasks, large for complex |
| **Speed** | Fast models for exploration, slower for reasoning |
| **Reliability** | Predictable behavior per task type |

---

## Prerequisites

1. **OpenCode** installed (v1.14.17+)
2. **oh-my-openagent** plugin installed (add to `plugin` in opencode.json)
3. Basic understanding of JSON configuration

---

## Configuration Files

### User-wide Configuration

These apply to all your OpenCode sessions:

| File | Location | Purpose |
| :--- | :--- | :--- |
| `oh-my-openagent.json` | `~/.config/opencode/` | Agent models, categories, settings |
| `opencode.json` | `~/.config/opencode/` | Permissions and plugins |

### Project-specific (Optional)

Override user settings per-project:

| File | Location | Purpose |
| :--- | :--- | :--- |
| `oh-my-openagent.json` | `.opencode/` | Project-specific overrides |

**Priority**: Project settings override user settings. This enables teams to share configurations while allowing individual customization.

---

## Agent Models

### Understanding the Model System

OpenCode supports multiple model sources. Each agent can use a different model based on its task requirements:

| Source | Prefix | Description |
| :--- | :--- | :--- |
| **OpenCode built-in** | `opencode/*` | Optimized models (gpt-5-nano) |
| **OpenCode Zen** | `minimax-m2.5-free` | Free open-weight models |
| **OpenAI** | `openai/*` | GPT models via API |
| **Anthropic** | `anthropic/*` | Claude models via API |

### Recommended Configuration

This configuration uses **free models from OpenCode Zen** - no API keys needed:

```json
{
  "agents": {
    "sisyphus": { "model": "minimax-m2.5-free" },
    "oracle": { "model": "minimax-m2.5-free" },
    "librarian": { "model": "minimax-m2.5-free" },
    "explore": { "model": "opencode/gpt-5-nano" },
    "metis": { "model": "minimax-m2.5-free" },
    "momus": { "model": "minimax-m2.5-free" }
  },
  "categories": {
    "visual-engineering": { "model": "minimax-m2.5-free" },
    "ultrabrain": { "model": "minimax-m2.5-free" },
    "deep": { "model": "minimax-m2.5-free" },
    "quick": { "model": "opencode/gpt-5-nano" },
    "unspecified-low": { "model": "opencode/gpt-5-nano" },
    "unspecified-high": { "model": "minimax-m2.5-free" },
    "artistry": { "model": "minimax-m2.5-free" }
  }
}
```

### Agent Purpose Reference

| Agent | Model | Purpose | When to Use |
| :--- | :--- | :--- | :--- |
| **sisyphus** | minimax-m2.5-free | Main orchestrator | Most tasks - the default worker |
| **oracle** | minimax-m2.5-free | Debugging & architecture | Complex bugs, design decisions |
| **librarian** | minimax-m2.5-free | Documentation lookup | Finding library docs, examples |
| **explore** | gpt-5-nano | Fast codebase search | Quick lookups, pattern finding |
| **metis** | minimax-m2.5-free | Pre-planning analysis | Before complex implementations |
| **momus** | minimax-m2.5-free | Plan review/critique | After significant work, before delivery |

### Model Selection Rationale

| Model | Why for Specific Agents |
| :--- | :--- |
| **minimax-m2.5-free** | 6.3% error rate (most accurate free model), slower but reliable |
| **opencode/gpt-5-nano** | Fast and free - perfect for quick exploration tasks |
| **minimax-m2.5-free** | 6.3% error rate, most accurate - use for critical tasks |

**Why minimax-m2.5-free** (based on benchmarks):

- **Most accurate** - 6.3% error rate (2x better than big-pickle at 12.3%)
- **Reliable** - Better for reasoning-heavy tasks
- **Trade-off** - Slower (1m 35s vs 1m 17s)

**When to use gpt-5-nano instead:**

- Quick lookups, fast pattern finding
- Non-critical exploration tasks

### Category Model Mapping

When you use `task(category="...")`, OpenCode routes to these models:

| Category | Model | Best For | Example Tasks |
| :--- | :--- | :--- | :--- |
| **visual-engineering** | minimax-m2.5-free | Frontend, UI/UX | React components, CSS, layouts |
| **ultrabrain** | minimax-m2.5-free | Hard logic | Algorithms, optimizations, complex refactoring |
| **deep** | minimax-m2.5-free | Research | Investigating libraries, patterns, new technologies |
| **unspecified-high** | minimax-m2.5-free | Complex unknown | Ambiguous requirements |
| **artistry** | minimax-m2.5-free | Creative | Unconventional solutions, novel approaches |

---

## Sisyphus Configuration

### What is Sisyphus?

**Sisyphus** is the main task orchestrator - your primary interface with OpenCode. It breaks down tasks, delegates to subagents, manages parallel execution, and supervises all work.

### Sisyphus Settings

```json
{
  "sisyphus": {
    "enabled": true,
    "max_concurrent_tasks": 2,
    "task_timeout": 300
  }
}
```

| Setting | Description | Recommended | Why |
| :--- | :--- | :--- | :--- |
| `enabled` | Enable/disable Sisyphus | `true` | Sisyphus is the main orchestrator |
| `max_concurrent_tasks` | Maximum parallel tasks | 2-3 | Prevents resource exhaustion |
| `task_timeout` | Timeout per task (seconds) | 300 (5 min) | Most tasks complete in 5 min |

### Hook Customization

Hooks are automated behaviors that run at certain times. Some are useful, others create noise.

### Disabled Hooks (Recommended)

| Hook | Why Disabled |
| :--- | :--- |
| `comment-checker` | Enforces style comments that create noise |
| `agent-usage-reminder` | Shows usage statistics - distracting |
| `startup-toast` | Displays welcome message on startup |

### Experimental Features

```json
{
  "preemptive_compaction_threshold": 0.85,  // Trigger compaction at 85% context
  "auto_resume": true                       // Auto recover from errors
}
```

| Feature | Purpose | Recommended |
| :--- | :--- | :--- |
| `preemptive_compaction_threshold` | Compress context before hitting limit | `0.85` (85%) |
| `auto_resume` | Recover from errors automatically | `true` |

---

## Best Practices (Community Recommendations)

### 1. Permission Strategy: "Seatbelt Pattern"

The "Seatbelt Pattern" is the community-recommended approach to permissions:

- **Default all commands to `"ask"`** - Conservative baseline
- **Explicitly deny dangerous commands** - Prevent catastrophes
- **Allow safe read-only tools** - Enable productivity
- **Keep package installs as `"ask"`** even if the manager itself is allowed

### 2. Model Selection (Open-Weight Preference)

| Principle | Explanation |
| :--- | :--- |
| **Primary model** | Use free models from OpenCode Zen for heavy reasoning |
| **Cost optimization** | Use `gpt-5-nano` for trivial/quick tasks |
| **Open-weight first** | Prefer open models over closed APIs |

**Recommended free models**:
- `minimax-m2.5-free` - Most accurate (6.3% error rate on benchmarks)
- `gpt-5-nano` - Fast and free for simple tasks
- `nemotron-3-super-free` - Strong alternative (9.0% error rate)

Based on [LLM benchmarks](https://www.glukhov.org/ai-devtools/opencode/llms-comparison/) for OpenCode.

### 3. Hook Customization

- **Disable** hooks that create noise (`startup-toast`, `comment-checker`)
- **Keep enabled** `auto_resume` for session recovery
- **Use** `preemptive_compaction` to manage context window

### 4. Project-specific Configuration

- Add `.opencode/oh-my-opencode.json` to repos for team consistency
- Project config takes precedence over user-wide config

---

## Quick Reference Commands

```bash
# Check available models
opencode models

# Verify installation
opencode --version

# Force config reload (if needed)
opencode --reload
```

---

## Troubleshooting

### Config not loading?

- Ensure OpenCode v1.14.17+
- Validate JSON: `python3 -m json.tool ~/.config/opencode/oh-my-open-code.json`

### Model not available?

- Check: `opencode models` shows all available
- Ensure model name matches exactly (case-sensitive)

### Permission denied?

- Edit `~/.opencode/opencode.json` permissions section
- Use `"ask"` for commands you want to approve per-session

---

## Related

- [README](../README.md) - Setup guide
- [Setup Guide](../setup.md) - Comprehensive setup
- [Skills](../skills.md) - Skills system
- [Web UI](../webui.md) - Web access options

---

## References

- [OhMyOpenCode Documentation](https://ohmyopencode.com/documentation/)
- [OhMyOpenCode Reddit Community](https://www.reddit.com/r/opencodeCLI/)
- [Installation Guide](https://ohmyopencode.com/installation/)
- [Configuration Reference](https://ohmyopencode.com/configuration/)
