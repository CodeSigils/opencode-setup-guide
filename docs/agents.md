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
| **OpenCode built-in** | `opencode/*` | Optimized models (big-pickle, gpt-5-nano) |
| **Ollama Cloud** | `ollama-cloud/*` | Remote Ollama instances |
| **OpenAI** | `openai/*` | GPT models via API |
| **Anthropic** | `anthropic/*` | Claude models via API |

### Recommended Configuration

This configuration balances capability with cost efficiency:

```json
{
  "agents": {
    "sisyphus": { "model": "ollama-cloud/qwen3-next:80b" },
    "oracle": { "model": "ollama-cloud/qwen3-next:80b" },
    "librarian": { "model": "ollama-cloud/deepseek-v3.2" },
    "explore": { "model": "opencode/gpt-5-nano" },
    "metis": { "model": "ollama-cloud/qwen3-next:80b" },
    "momus": { "model": "ollama-cloud/qwen3-next:80b" }
  },
  "categories": {
    "visual-engineering": { "model": "ollama-cloud/gemma3:27b" },
    "ultrabrain": { "model": "ollama-cloud/qwen3-next:80b" },
    "deep": { "model": "ollama-cloud/qwen3-next:80b" },
    "quick": { "model": "opencode/gpt-5-nano" },
    "unspecified-low": { "model": "opencode/gpt-5-nano" },
    "unspecified-high": { "model": "ollama-cloud/qwen3-next:80b" },
    "artistry": { "model": "ollama-cloud/qwen3-next:80b" }
  }
}
```

### Agent Purpose Reference

| Agent | Model | Purpose | When to Use |
| :--- | :--- | :--- | :--- |
| **sisyphus** | qwen3-next:80b | Main orchestrator | Most tasks - the default worker |
| **oracle** | qwen3-next:80b | Debugging & architecture | Complex bugs, design decisions |
| **librarian** | deepseek-v3.2 | Documentation lookup | Finding library docs, examples |
| **explore** | gpt-5-nano | Fast codebase search | Quick lookups, pattern finding |
| **metis** | qwen3-next:80b | Pre-planning analysis | Before complex implementations |
| **momus** | qwen3-next:80b | Plan review/critique | After significant work, before delivery |

### Model Selection Rationale

| Model | Why for Specific Agents |
| :--- | :--- |
| **qwen3-next:80b** | Best open reasoning model - ideal for complex orchestration, debugging, planning |
| **deepseek-v3.2** | Excellent at documentation tasks - better at finding and summarizing info |
| **gpt-5-nano** | Fast and free - perfect for quick exploration tasks that don't need deep reasoning |
| **gemma3:27b** | Good vision capabilities - useful for frontend/UI tasks |

### Category Model Mapping

When you use `task(category="...")`, OpenCode routes to these models:

| Category | Model | Best For | Example Tasks |
| :--- | :--- | :--- | :--- |
| **visual-engineering** | gemma3:27b | Frontend, UI/UX | React components, CSS, layouts |
| **ultrabrain** | qwen3-next:80b | Hard logic | Algorithms, optimizations, complex refactoring |
| **deep** | qwen3-next:80b | Research | Investigating libraries, patterns, new technologies |
| **quick** | gpt-5-nano | Trivial tasks | Typos, small fixes, simple edits |
| **unspecified-low** | gpt-5-nano | Low effort | Simple changes |
| **unspecified-high** | qwen3-next:80b | Complex unknown | Ambiguous requirements |
| **artistry** | qwen3-next:80b | Creative | Unconventional solutions, novel approaches |

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
| **Primary model** | Use Ollama Cloud models for heavy reasoning |
| **Cost optimization** | Use `gpt-5-nano` for trivial/quick tasks |
| **Open-weight first** | Prefer open models over closed APIs |

**Recommended models**:
- `qwen3-next:80b` - Best open reasoning model
- `gemma3:27b` - Good for vision/multimodal
- `deepseek-v3.2` - Excellent for documentation
- `gpt-5-nano` - Fast and free for simple tasks

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

- [Index](../index.md) - Documentation hub
- [Setup Guide](../setup.md) - Comprehensive setup
- [Skills](../skills.md) - Skills system
- [Web UI](../webui.md) - Web access options

---

## References

- [OhMyOpenCode Documentation](https://ohmyopencode.com/documentation/)
- [OhMyOpenCode Reddit Community](https://www.reddit.com/r/opencodeCLI/)
- [Installation Guide](https://ohmyopencode.com/installation/)
- [Configuration Reference](https://ohmyopencode.com/configuration/)