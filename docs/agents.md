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

1. **OpenCode** installed (v1.0.133+)
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
| **OpenCode Zen** | `opencode/big-pickle` | Free curated models |
| **OpenAI** | `openai/*` | GPT models via API |
| **Anthropic** | `anthropic/*` | Claude models via API |

### Recommended Configuration

This configuration uses **free models from OpenCode Zen** - no API keys needed:

```json
{
  "agents": {
    "sisyphus": { "model": "opencode/big-pickle" },
    "oracle": { "model": "opencode/big-pickle" },
    "librarian": { "model": "opencode/big-pickle" },
    "explore": { "model": "opencode/gpt-5-nano" },
    "metis": { "model": "opencode/big-pickle" },
    "momus": { "model": "opencode/big-pickle" }
  },
  "categories": {
    "visual-engineering": { "model": "opencode/big-pickle" },
    "ultrabrain": { "model": "opencode/big-pickle" },
    "deep": { "model": "opencode/big-pickle" },
    "quick": { "model": "opencode/gpt-5-nano" },
    "unspecified-low": { "model": "opencode/gpt-5-nano" },
    "unspecified-high": { "model": "opencode/big-pickle" },
    "artistry": { "model": "opencode/big-pickle" }
  }
}
```

### Agent Purpose Reference

| Agent | Model | Purpose | When to Use |
| :--- | :--- | :--- | :--- |
| **sisyphus** | big-pickle | Main orchestrator | Most tasks - the default worker |
| **oracle** | big-pickle | Debugging & architecture | Complex bugs, design decisions |
| **librarian** | big-pickle | Documentation lookup | Finding library docs, examples |
| **explore** | gpt-5-nano | Fast codebase search | Quick lookups, pattern finding |
| **metis** | big-pickle | Pre-planning analysis | Before complex implementations |
| **momus** | big-pickle | Plan review/critique | After significant work, before delivery |

### Model Selection Rationale

| Model | Why for Specific Agents |
| :--- | :--- |
| **big-pickle** | 12.3% error rate, fastest (1m 17s), researches first via Exa |
| **opencode/big-pickle** | 6.3% error rate - use when highest accuracy needed |
| **gpt-5-nano** | Fast and free - perfect for quick exploration tasks |

**Why big-pickle** (based on benchmarks):
- Fastest to complete tasks (1m 17s vs 1m 35s)
- **Research-first approach** - Uses Exa Code Search before coding
- 12.3% error rate - still very usable for most daily tasks

**When to use opencode/big-pickle instead:**
- Strict tasks requiring higher accuracy (6.3% vs 12.3%)
- Migration maps, schema generation, structured outputs

### Category Model Mapping

When you use `task(category="...")`, OpenCode routes to these models:

| Category | Model | Best For | Example Tasks |
| :--- | :--- | :--- | :--- |
| **visual-engineering** | opencode/big-pickle | Frontend, UI/UX | React components, CSS, layouts |
| **ultrabrain** | opencode/big-pickle | Hard logic | Algorithms, optimizations, complex refactoring |
| **deep** | opencode/big-pickle | Research | Investigating libraries, patterns, new technologies |
| **quick** | gpt-5-nano | Trivial tasks | Typos, small fixes, simple edits |
| **unspecified-low** | gpt-5-nano | Low effort | Simple changes |
| **unspecified-high** | opencode/big-pickle | Complex unknown | Ambiguous requirements |
| **artistry** | opencode/big-pickle | Creative | Unconventional solutions, novel approaches |

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
- `opencode/big-pickle` - Best overall (6.3% error rate on benchmarks)
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
- Ensure OpenCode v1.0.133+
- Validate JSON: `python3 -m json.tool ~/.config/opencode/oh-my-opencode.json`

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