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

This configuration uses **fallback chains** - primary model for quality, fallbacks for speed/reliability:

```json
{
  "agents": {
    "sisyphus": {
      "model": "opencode/minimax-m2.5-free",
      "fallback_models": ["opencode/big-pickle", "opencode/gpt-5-nano"]
    },
    "oracle": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    "librarian": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/gpt-5-nano"]
    },
    "explore": {
      "model": "opencode/gpt-5-nano",
      "fallback_models": ["opencode/big-pickle"]
    },
    "metis": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    "momus": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    "prometheus": {
      "model": "opencode/minimax-m2.5-free",
      "fallback_models": ["opencode/big-pickle"]
    },
    "atlas": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/gpt-5-nano"]
    },
    "hephaestus": { "model": "opencode/gpt-5-nano" },
    "multimodal-looker": {
      "model": "opencode/gpt-5-nano",
      "fallback_models": ["opencode/big-pickle"]
    },
    "sisyphus-junior": { "model": "opencode/gpt-5-nano" }
  },
  "categories": {
    "visual-engineering": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    "ultrabrain": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    "deep": {
      "model": "opencode/minimax-m2.5-free",
      "fallback_models": ["opencode/big-pickle"]
    },
    "artistry": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/gpt-5-nano"]
    },
    "quick": { "model": "opencode/gpt-5-nano" },
    "unspecified-low": { "model": "opencode/gpt-5-nano" },
    "unspecified-high": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    "writing": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/gpt-5-nano"]
    }
  }
}
```

### Agent Purpose Reference

| Agent | Primary Model | Fallback | Purpose | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **sisyphus** | minimax-m2.5-free | big-pickle, gpt-5-nano | Main orchestrator | Most tasks - the default worker |
| **oracle** | big-pickle | minimax-m2.5-free | Debugging & architecture | Complex bugs, design decisions |
| **librarian** | big-pickle | gpt-5-nano | Documentation lookup | Finding library docs, examples |
| **explore** | gpt-5-nano | big-pickle | Fast codebase search | Quick lookups, pattern finding |
| **metis** | big-pickle | minimax-m2.5-free | Pre-planning analysis | Before complex implementations |
| **momus** | big-pickle | minimax-m2.5-free | Plan review/critique | After significant work, before delivery |
| **prometheus** | minimax-m2.5-free | big-pickle | Planning | Complex task breakdown |
| **atlas** | big-pickle | gpt-5-nano | Todo orchestration | Task management |
| **hephaestus** | gpt-5-nano | - | Deep work | Intensive implementation |
| **multimodal-looker** | gpt-5-nano | big-pickle | Vision/media | Image, PDF analysis |
| **sisyphus-junior** | gpt-5-nano | - | Trivial tasks | Simple fixes |

### Model Selection Rationale

| Model | Why |
| :--- | :--- |
| **big-pickle** | Fast reasoning (1m 17s) - use for most agent tasks |
| **minimax-m2.5-free** | Highest accuracy (6.3% error rate) - critical tasks only |
| **gpt-5-nano** | Fastest - trivial tasks, exploration |

**Strategy: Fallback Chains**

The config uses fallback chains for resilience:

1. **Primary**: Best model for the task
2. **Fallback #1**: Faster alternative if primary is slow/unavailable
3. **Fallback #2**: Fastest (usually gpt-5-nano) as last resort

**Model Selection by Agent:**

| Agent | Primary | Rationale |
| :--- | :--- | :--- |
| **Sisyphus** | minimax | Quality critical - orchestrates everything |
| **Oracle** | big-pickle | Speed important for debugging |
| **Librarian** | big-pickle | Speed for docs lookup |
| **Explore** | gpt-5-nano | Fastest for search |
| **Metis/Momus** | big-pickle | Speed for review/planning |
| **Prometheus** | minimax | Quality for planning |
| **Deep** | minimax | Research needs accuracy |

### Category Model Mapping

When you use `task(category="...")`, OpenCode routes to these models with fallbacks:

| Category | Primary Model | Fallback | Best For | Example Tasks |
| :--- | :--- | :--- | :--- | :--- |
| **visual-engineering** | big-pickle | minimax-m2.5-free | Frontend, UI/UX | React components, CSS, layouts |
| **ultrabrain** | big-pickle | minimax-m2.5-free | Hard logic | Algorithms, optimizations |
| **deep** | minimax-m2.5-free | big-pickle | Research | Investigating libraries, patterns |
| **unspecified-high** | big-pickle | minimax-m2.5-free | Complex unknown | Ambiguous requirements |
| **artistry** | big-pickle | gpt-5-nano | Creative | Unconventional solutions |
| **quick** | gpt-5-nano | - | Trivial tasks | Single file fixes |
| **unspecified-low** | gpt-5-nano | - | Simple tasks | Minor changes |
| **writing** | big-pickle | gpt-5-nano | Documentation | Docs, prose |

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

### 2. Model Selection (Fallback Chain Pattern)

| Principle | Explanation |
| :--- | :--- |
| **Primary** | Quality model for the agent's main task |
| **Fallback** | Faster alternative if primary is slow/unavailable |
| **Last resort** | Always `gpt-5-nano` - fastest, never fails |
| **Chain strategy** | Quality → Speed → Survival |

**Recommended fallback chains**:
- Sisyphus: `minimax-m2.5-free` → `big-pickle` → `gpt-5-nano`
- Oracle/Librarian: `big-pickle` → `minimax-m2.5-free`
- Explore/Fast: `gpt-5-nano` → `big-pickle`

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

## Your Actual Configuration

Your config file at `~/.config/opencode/oh-my-openagent.jsonc` uses the following model chains:

```json
{
  "agents": {
    "sisyphus": {
      "model": "opencode/minimax-m2.5-free",
      "fallback_models": ["opencode/big-pickle", "opencode/gpt-5-nano"]
    }
  }
}
```

**Key patterns in your config:**
- `prompt_append` for sisyphus, oracle with `directives.md`
- Extensive fallback chains for resilience
- `gpt-5-nano` for all fast/trivial agents
- `big-pickle` for speed-critical reasoning

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
