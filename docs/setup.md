# OpenCode Setup - Comprehensive Guide

## Overview

This comprehensive guide covers the complete setup of OpenCode with plugins, custom models, persistent directives, skills, and anti-patterns system. It's designed to help you configure and use OpenCode effectively for production development.

> **Learning Tip**: This guide assumes you understand basic CLI tools and have used an AI coding assistant before. If you're new to AI coding, start with the [README](https://github.com/CodeSigils/opencode-setup-guide/blob/main/README.md) to understand core concepts first.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Plugin Installation](#2-plugin-installation)
3. [Model Configuration](#3-model-configuration)
4. [Persistent Directives](#4-persistent-directives)
5. [Skills System](#5-skills-system)
6. [Memory Plugin (opencode-mem)](#6-memory-plugin-opencode-mem)
7. [Search and Research Tools](#7-search-and-research-tools)
8. [Configuration Files Explained](#8-configuration-files-explained)
9. [Learning from Anti-Patterns](#9-learning-from-anti-patterns)
10. [Complete Installation Guide](#10-complete-installation-guide)
11. [Troubleshooting](#11-troubleshooting)
12. [Sources and References](#12-sources-and-references)
13. [Video Resources](#13-video-resources)
14. [Link Checker](#14-link-checker)
15. [Maintenance Tips](#15-maintenance-tips)

---

## 1. Introduction

### What is OpenCode?

OpenCode is an AI coding agent that helps you write, debug, and maintain code. Unlike traditional coding assistants, OpenCode is designed to be fully autonomous while following your team's conventions and best practices.

**Key distinction from other AI tools**: OpenCode doesn't just generate code - it **reasons** about your codebase, searches for existing patterns before creating new code, and delivers production-ready solutions with minimal hand-holding.

### Why This Setup?

This configuration provides the foundation for effective AI-assisted development:

| Feature | Purpose | Why It Matters |
| :--- | :--- | :--- |
| **Persistent Directives** | Rules that apply to every session | Ensures consistent behavior across all tasks |
| **Custom Skills** | Reusable instruction bundles | Encodes your team's conventions once |
| **Memory Plugin** | Semantic search for context | Learns from your past decisions |
| **Anti-Patterns Guide** | Avoid common AI mistakes | Prevents production bugs from AI code |
| **Best Practices** | Community-approved patterns | Leverages collective experience |

**Who is this for?**
- Developers migrating from Cursor/Windsurf
- Teams standardizing AI coding practices
- Power users wanting full customization

---

## 2. Plugin Installation

### 2.1 Understanding OpenCode Configuration

OpenCode uses a hierarchical configuration system. It looks for configuration files in multiple locations with the following priority order:

| Level | Path | Description | When to Use |
| :--- | :--- | :--- | :--- |
| **Project** | `.opencode/oh-my-openagent.json` | Project-specific settings | Team projects, shared configs |
| **User** | `~/.config/opencode/oh-my-openagent.json` | User-wide settings | Personal preferences, defaults |

**How priority works**: Project settings override user settings. This means you can set sensible defaults globally, then override them per-project for specific team requirements.

> **Important**: Both JSON and JSONC (JSON with Comments) formats are supported. JSONC allows comments and trailing commas, making it easier to write and maintain. Use `.jsonc` extension if you want comments.
> **Package Manager**: Use **bun** for local dependency management (faster, no version conflicts). If using npm/pnpm, remove `package-lock.json` to avoid conflicts with bun.lock.

### 2.2 Installing Plugins

**Crucial Concept - Don't Use npm install**: OpenCode plugins auto-download from npm when you add them to configuration. Running `npm install` is unnecessary and can cause version conflicts.

**Step-by-step**:

1. Create or edit `~/.config/opencode/opencode.json`
2. Add plugins to the `plugin` array
3. Restart OpenCode - it downloads automatically

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "opencode-mem",
    "oh-my-openagent@latest"
  ]
}
```

### Why This Works

The OpenCode system monitors the `plugin` array in your configuration. On the next startup, it automatically:
1. Resolves the package name to the latest version
2. Downloads from npm registry
3. Loads the plugin into the runtime

This is similar to how VS Code extensions work - you don't manually install them, you enable them in config.

### Available Plugins

| Plugin | npm Package | Purpose | When to Use |
| :--- | :--- | :--- | :--- |
| `opencode-mem` | `opencode-mem` | Semantic memory system | Always - provides context search |
| `oh-my-openagent` | `oh-my-openagent` | Core agent enhancements | Recommended - adds powerful agents |

**Note**: Version pinning is optional. Using `@latest` gets the newest stable version. Pin to specific version (e.g., `oh-my-openagent@2.1.0`) if you need stability.

---

## 3. Model Configuration

### 3.1 Understanding Agent vs Category Models

OpenCode uses two complementary systems for routing work to AI models:

1. **Agents** - Specialized AI personalities for specific tasks (Sisyphus, Oracle, Librarian, etc.)
2. **Categories** - Task-type classifiers that route work to appropriate models

Think of it this way:
- **Agents** are like specialists - you call them by name for specific expertise
- **Categories** are like job postings - you describe the task type and OpenCode matches it to a model

### 3.2 Configuring Agents (Free Tier Optimized)

Each agent can have its own model with fallback chains. This provides FREE reliability:

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-openagent/dev/assets/oh-my-openagent.schema.json",
  "agents": {
    // Main orchestrator: minimax-free (best) → big-pickle (fast) → nano (fallback)
    "sisyphus": {
      "model": "opencode/minimax-m2.5-free",
      "fallback_models": ["opencode/big-pickle", "opencode/gpt-5-nano"],
      "prompt_append": "file://~/.config/opencode/directives.md"
    },
    // Oracle: Big Pickle for speed-critical debugging
    "oracle": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/minimax-m2.5-free"]
    },
    // Librarian: Big Pickle for docs
    "librarian": {
      "model": "opencode/big-pickle",
      "fallback_models": ["opencode/gpt-5-nano"]
    },
    // Explore: GPT-Nano for fast search
    "explore": {
      "model": "opencode/gpt-5-nano",
      "fallback_models": ["opencode/big-pickle"]
    }
  },
  "categories": {
    "visual-engineering": { "model": "opencode/big-pickle" },
    "ultrabrain": { "model": "opencode/big-pickle" },
    "deep": { "model": "opencode/minimax-m2.5-free", "fallback_models": ["opencode/big-pickle"] },
    "quick": { "model": "opencode/gpt-5-nano" }
  }
}
```

### 3.2 Agent Descriptions

| Agent | Purpose | When to Invoke |
| :--- | :--- | :--- |
| `sisyphus` | Main orchestrator for task execution | Most tasks - default choice |
| `oracle` | Debugging and reasoning specialist | Complex bugs, architecture questions |
| `librarian` | Research and documentation lookup | Need to find docs or external info |
| `explore` | Codebase search and pattern finding | Need to understand existing code |
| `metis` | Pre-planning consultant | Before starting complex implementation |
| `momus` | Review and critique specialist | After completing significant work |
| `atlas` | Master orchestrator | Multi-system coordination |
| `prometheus` | Planning and task management | Breaking down large tasks |

### 3.3 Category Descriptions

Categories are invoked via `task(category="...")` and OpenCode automatically selects appropriate model:

| Category | Best For | Example Tasks |
| :--- | :--- | :--- |
| `visual-engineering` | Frontend, UI/UX, design | CSS, React components, layouts |
| `ultrabrain` | Hard logic, algorithms | Complex algorithms, optimizations |
| `deep` | Research-heavy tasks | Investigating new libraries, patterns |
| `artistry` | Creative problem-solving | Unconventional solutions |
| `quick` | Simple, trivial tasks | Typos, small fixes |
| `unspecified-high` | Complex unspecified tasks | Ambiguous requirements |

### 3.4 Recommended Models (Free Tier)

This configuration uses **3 FREE models** with fallback chains for reliability:

| Model | Tier | Best For | Error Rate |
| :--- | :--- | :--- | :--- |
| `minimax-m2.5-free` | **Best** | Main reasoning, orchestration | 6.3% |
| `big-pickle` | **Fast** | Oracle, Atlas, Writing | 12.3% |
| `gpt-5-nano` | **Quick** | Explore, Quick tasks | N/A |

**Recommended Fallback Chains**:

```json
// For complex tasks (Sisyphus default)
"model": "minimax-m2.5-free",
"fallback_models": ["big-pickle", "gpt-5-nano"]

// For fast tasks (Explore, Quick)
"model": "gpt-5-nano",
"fallback_models": ["big-pickle"]
```

**Why this strategy?**
- **minimax-m2.5-free**: Best quality, lowest error rate
- **big-pickle**: Fast execution as fallback
- **gpt-5-nano**: Ultimate fallback, always available
- **All FREE**: No API keys required

**Model sources**:
- **OpenCode Zen** (`minimax-m2.5-free`) - Free curated models
- **OpenCode built-in** (`big-pickle`, `gpt-5-nano`) - Free built-in models

### 3.5 What is Sisyphus?

**Sisyphus** is the main task orchestrator in OpenCode. It's responsible for:

- Breaking down tasks into manageable subtasks
- Delegating work to specialized subagents
- Coordinating parallel execution
- Managing task timeouts and concurrency

Think of Sisyphus as your project manager - it plans, delegates, and supervises all the work.

### 3.6 Sisyphus Settings

**Where to configure**: In `~/.config/opencode/oh-my-openagent.json`

```json
{
  "sisyphus": {
    "enabled": true,
    "max_concurrent_tasks": 2,
    "task_timeout": 300
  }
}
```

| Setting | Description | Recommended |
| :--- | :--- | :--- |
| `enabled` | Enable/disable Sisyphus | `true` |
| `max_concurrent_tasks` | Maximum parallel tasks Sisyphus can run | 2-3 |
| `task_timeout` | Timeout in seconds per task | 300 (5 min) |

**Why these settings?**
- `max_concurrent_tasks: 2`: Prevents overwhelming system resources while allowing parallelism
- `task_timeout: 300`: Most tasks should complete within 5 minutes; longer tasks may need human intervention

---

## 4. Persistent Directives

### 4.1 What Are Directives?

**Directives** are rules that persist in every session. Unlike skills (loaded on-demand), directives apply to all tasks automatically. They're the "always-on" instructions that shape how OpenCode behaves.

**Why use directives?**
- Enforce consistent behavior across all tasks
- Capture team conventions once
- Prevent common mistakes automatically

### 4.2 How Directives Work

Custom directives persist across sessions using `prompt_append` with a file URI. This loads instructions from an external file and appends them to agent system prompts, so they're always present in every conversation.

**Key concept**: Directives are loaded via the `prompt_append` config option, which appends file contents to the agent's system prompt. This means every message includes your rules.

Add the `prompt_append` option to any agent:

```json
{
  "agents": {
    "sisyphus": {
      "model": "minimax-m2.5-free",
      "prompt_append": "file://~/.config/opencode/directives.md"
    },
    "oracle": {
      "model": "minimax-m2.5-free",
      "prompt_append": "file://~/.config/opencode/directives.md"
    }
  }
}
```

### 4.2 Supported URI Formats

| Format | Example | Description |
| :--- | :--- | :--- |
| Absolute | `file:///abs/path/file.md` | Full path |
| Relative | `file://./relative/path.md` | Relative to project |
| Home | `file://~/home/dir/file.md` | Home-relative |

### 4.3 The 17 Directives

> **Source**: These directives are extracted from [`directives.md`](.opencode/directives.md) - a persistent rules file loaded via `prompt_append` in every OpenCode session. Follow these rules in all your work.

#### Directive 1: Check Official Documentation First

Before implementing ANY feature, library integration, or solving NON-TRIVIAL problems:

1. **ALWAYS check official documentation FIRST**
   - Unknown library → context7_query-docs for versioned docs
   - API examples → codesearch for programming patterns
   - Code patterns → grep_app_searchGitHub for GitHub examples

2. **When to search:**
   - Unknown library/API → fire librarian immediately
   - Non-trivial problem → explore codebase patterns
   - External dependency → codesearch or grep_app

3. **Check community best practices:**
   - General → websearch (Exa)
   - Code examples → grep_app_searchGitHub
   - How developers do X → codesearch

**NEVER assume without verification.**

#### Directive 2: Documentation vs Persistence

**Rule**: Persist only rules (~300 lines max).

| What to Persist | What is Reference |
| :--- | :--- |
| ``directives.md`` | setup.md |
| (rules) | skills.md |

**Do NOT load**: Full guides (~500+ lines) in prompt_append.

Use reference docs via grep or memory search when needed.

Why? Consumes tokens in every session. Reference docs are TOO LONG.

#### Directive 3: GitHub Markdown Style

When writing ANY markdown documentation file:

1. **Line length**: Break lines at ~80 characters
   - Long descriptions → break into 2+ lines
   - Improves readability on narrow screens

2. **Code blocks**: Use proper syntax highlighting

   ```json
   { "key": "value" }
   ```

3. **Lists with content**: Each item on new line
   - Don't jam multiple items on one line

4. **Headers**: Clear hierarchy (## then ###)
   - No skipping levels

#### Directive 4: GitHub Flavored Markdown Tables

Use GFM schema with colons for column alignment:

```
| Header |
| :--- |
```

| Column Type | Syntax |
| :--- | :--- |
| Left | `| :--- |`
| Center | `| :--: |`
| Right | `| ---: |`

**Minimum required**: At least one `| :--- |` row.

#### Directive 5: Learning from Mistakes

**Wrong**: Manual npm install for OpenCode plugins

OpenCode plugins auto-download from npm when you add them to config:

```json
{ "plugin": ["plugin-name"] }
```

**DO NOT run** `npm install plugin-name` - it's redundant and wasteful.

**CORRECT**: Just add to config → auto-downloads on next startup.

**Recommended**: Use bun for local management. If you must use npm, delete `package-lock.json` to avoid conflicts with bun.lock.

#### Directive 6: Anti-Duplication

**Wrong**: Re-doing delegated work

After firing explore/librarian agents, manually greping for the same info.

**CORRECT**: Once delegated, wait for results or work on non-overlapping tasks.

#### Directive 7: Trust But Verify

**Wrong**: Trusting subagent claims

Assuming "done" means actually complete.

**CORRECT**: Always independently verify:
1. Read EVERY changed file line by line
2. Run `lsp_diagnostics` on modified files
3. Run tests, verify exit code 0
4. Cross-check: subagent claims vs actual code

#### Directive 8: Read Before Edit

**Wrong**: Editing without reading

Using Edit tool without first reading the file.

**CORRECT**: ALWAYS read file first via Read tool, THEN edit.

#### Directive 9: Use Right Tool for Search

**Wrong**: Using slower grep tool for large searches

Using built-in grep tool for large codebases.

**CORRECT**: Use ripgrep (`rg`) for large codebases:

| Tool | When |
| :--- | :--- |
| `rg` | Large codebase, complex patterns |
| grep tool | Small files, quick checks |

#### Directive 10: Use session_id for Retries

**Wrong**: Starting fresh on failures

Creating new task() session when retrying failed work.

**CORRECT**: Use `session_id="ses_..."` to preserve context:
- Subagent already has full context
- Saves 70%+ tokens
- Knows what already failed

#### Directive 11: Stop at Verification Failure

**Wrong**: Continuing after failed verification

Ignoring errors and continuing work anyway.

**CORRECT**: Stop and fix immediately:
- Build fails → Fix, don't continue
- Tests fail → Fix, don't continue
- lsp_diagnostics errors → Fix, don't continue

#### Directive 12: Keep Documentation In Sync

**Wrong**: Updating config but not docs

When directives.md or config files change, leaving documentation outdated.

**CORRECT**: Always update documentation AFTER making changes.

#### Directive 13: Prompt Efficiency

**Wrong**: Long vague prompts

Asking AI to "implement feature X" in one massive prompt.

**CORRECT**: Short, specific prompts:
- One task at a time
- Clear expected output
- Reference existing patterns

**Token tip**: 3 short prompts < 1 long prompt in quality + tokens.

#### Directive 14: Pre-Acceptance Checklist

Before accepting ANY AI-generated code:

1. **Search first**: Does this utility already exist?
   ```bash
   ast_grep or grep for similar functions
   ```
2. **Check duplication**: Am I duplicating existing code?
3. **Verify patterns**: Does it match your codebase style?
4. **Test edge cases**: Has error handling?
5. **Check security**: Any injection risks?
6. **Run lsp_diagnostics**: Any errors introduced?

#### Directive 15: Context File Pattern

Create `.opencode-rules` files in project root:

```
.opencode-rules:
- Project overview and tech stack
- Files that should NEVER be modified
- Common gotchas to watch for
- Coding standards
```

This prevents tunnel vision - AI only sees fraction of codebase.

#### Directive 16: AI Footguns - Critical Anti-Patterns

These patterns cause the most production bugs in AI-generated code:

**1. Code Duplication**
- Prevention: Use `ast_grep` to search codebase before generating
- AI generates what matches training patterns, not your existing utilities

**2. Phantom Validation (TypeScript ≠ Runtime)**
- Problem: TypeScript interfaces have ZERO runtime validation
- Types vanish at compile time
- Prevention: Use Zod for runtime validation

**3. Memory Leaks (Missing Cleanup)**
- Problem: Subscribe but forget unsubscribe
- Training data rarely shows cleanup code
- Prevention: ALWAYS include cleanup

**4. Auth Leaks (IDOR)**
- Problem: Every authenticated user can access every resource
- No role-based access control
- Prevention: Add role-based access control (RBAC)

**5. Console.log Debugging**
- Problem: Uses console.log instead of proper logging
- Prevention: Use structured logger (pino, winston)

#### Directive 17: Check Community Best Practices

**Wrong**: Implementing without checking community patterns

**CORRECT**: Always check:
- Use websearch for "best practices X"
- Use grep_app_searchGitHub for common patterns
- Search "how do X developers typically handle Y"

---

## 5. Skills System

### 5.1 What Are Skills?

Skills are reusable instruction bundles defined in SKILL.md files. They encode team conventions, workflows, and best practices. Skills let you capture expertise once so the AI never repeats the same explanations.

### 5.2 How Skills Differ from Directives

| Feature | Directives | Skills |
| :--- | :--- | :--- |
| Load timing | Every session | On-demand |
| Scope | General rules | Specific workflows |
| Triggered by | Session start | Agent recognition |
| Persistence | Via prompt_append | Via skill tool |

### 5.3 Skills File Structure

Each skill lives in its own folder with SKILL.md inside.

SKILL.md must start with YAML frontmatter:

```yaml
---
name: skill-name
description: Brief description of what this skill does
license: MIT
compatibility: opencode
---

## What I do

- First capability
- Second capability

## When to use me

Use this skill when [specific trigger situation].
```

### 5.4 Where Skills Live

| Location | Path | Scope |
| :--- | :--- | :--- |
| Project | `.opencode/skills/` | Project-local |
| Global | `~/.config/opencode/skills/` | User-wide |

### 5.5 Recommended Skills

| Skill | Source | Purpose |
| :--- | :--- | :--- |
| code-checklist | Custom | 17 directive rules |
| context7-docs | chandima | Official docs lookup |
| github-ops | chandima | GitHub operations |
| security-auditor | chandima | Security scanning |

### 5.6 Installing Skills

```bash
git clone https://github.com/chandima/opencode-config.git ~/projects/opencode-config
ln -s ~/projects/opencode-config/skills/* ~/.config/opencode/skills/
```

### 5.7 Using Skills

Reference the skill in your prompts:

```
Use the security-audit skill to check this code
Use the context7-docs skill to find React docs
Use the github-ops skill to list recent PRs
```

---

## 6. Memory Plugin (opencode-mem)

### 6.1 Installation

The plugin is automatically installed via config. Just add to `~/.config/opencode/opencode.json`:

```json
{
  "plugin": ["opencode-mem"]
}
```

### 6.2 Configuration

Create `~/.config/opencode/opencode-mem.jsonc`:

```jsonc
{
  // Storage location for memories
  "storagePath": "~/.opencode-mem/data",

  // User identification (optional)
  "userNameOverride": "USER_NAME",

  // Embedding model (local, free)
  "embeddingModel": "Xenova/nomic-embed-text-v1",

  // Memory search scope
  "memory": {
    "defaultScope": "project"
  },

  // Web UI access
  "webServerEnabled": true,
  "webServerPort": 4747,

  // Auto-capture settings
  "autoCaptureEnabled": true,
  "opencodeProvider": "anthropic",
  "opencodeModel": "claude-haiku-4-5-20251001",

  // Notification preferences
  "showAutoCaptureToasts": true,
  "showUserProfileToasts": true,
  "showErrorToasts": true,

  // Memory limits
  "userProfileAnalysisInterval": 10,
  "maxMemories": 10,

  // Memory compaction
  "compaction": {
    "enabled": true,
    "memoryLimit": 10
  },

  // Chat message injection
  "chatMessage": {
    "enabled": true,
    "maxMemories": 3,
    "excludeCurrentSession": true,
    "injectOn": "first"
  }
}
```

### 6.3 Features

- **SQLite + USearch**: Vector database for semantic search
- **Web UI**: Access at http://127.0.0.1:4747
- **Auto-capture**: Automatically stores conversations
- **User profile learning**: Learns your preferences over time
- **Multiple embedding models**: 12+ options available

### 6.4 Usage

```javascript
// Add information to memory
memory({ mode: "add", content: "Project uses microservices architecture" })

// Search memory
memory({ mode: "search", query: "architecture decisions" })

// List memories
memory({ mode: "list", limit: 10 })

// View user profile
memory({ mode: "profile" })
```

---

## 7. Search and Research Tools

### 7.1 Available Tools

OpenCode provides multiple search tools for different needs:

#### Codebase Search

| Tool | Best For | Speed |
| :--- | :--- | :--- |
| `rg` (ripgrep) | Large codebases | Fastest (10x) |
| `grep` | Small files | Medium |
| `ast_grep_search` | AST patterns | Medium |
| `glob` | File finding | Fast |
| `lsp_find_references` | Symbol references | Fast |

#### Web Search

| Tool | Best For | 
| :--- | :--- |
| `grep_app_searchGitHub` | Real-world code examples |
| `codesearch` | Programming APIs, SDKs, libraries |
| `websearch` / `websearch_web_search_exa` | General web search |
| `context7_query-docs` | Official library docs (React, Next.js, etc.) |

### 7.2 Efficient Tool Selection

| Need | Most Efficient Tool |
| :--- | :--- |
| Code in GitHub | grep_app_searchGitHub |
| How to use API | codesearch |
| Official docs | context7_query-docs |
| General how-to | websearch |

### 7.3 Quick Search Commands

Use these before writing code:

```bash
# Find similar code
rg "pattern" .

# Search GitHub for examples
grep_app_search query="useState React"

# Get official docs
context7 query-docs library="react" topic="hooks"

# General search
websearch "best practices React authentication"
```

---

## 8. Configuration Files Explained

### 8.1 File Locations Summary

| File | Location | Purpose |
| :--- | :--- | :--- |
| `opencode.json` | `~/.config/opencode/` | Plugin list |
| `oh-my-openagent.json` | `~/.config/opencode/` | Agents and models |
| ``directives.md`` | `~/.config/opencode/` | Persistent rules |
| `opencode-mem.jsonc` | `~/.config/opencode/` | Memory config |
| `best-practices.md` | `~/Notes/opencode/` | Anti-patterns ref |

### 8.2 Configuration Hierarchy

```
~/.config/opencode/
├── opencode.json         # Plugin list (this overrides project)
├── oh-my-openagent.json # Agent configs
├── directives.md        # Persistent rules
├── opencode-mem.jsonc   # Memory settings
├── best-practices.md    # Anti-patterns
└── skills/              # Custom skills
```

Project-level settings override user-level settings when in a project directory.

---

## 9. Learning from Anti-Patterns

### 9.1 Research Findings

Recent research (2025-2026) reveals significant differences between AI-generated and human-written code:

| Metric | AI Code | Human Code |
| :--- | :--- | :--- |
| Code duplication | 12.3% | 8.3% |
| Security flaws | 36-40% | Varies |
| Logic errors | +75% more | Baseline |
| Revert rate | 55% higher | Baseline |

### 9.2 The 10 Anti-Patterns

| # | Anti-Pattern | Issue | Prevention |
| :--- | :--- | :--- | :--- |
| 1 | Code duplication | AI doesn't search codebase | Use ast_grep first |
| 2 | Phantom validation | TypeScript ≠ runtime | Use Zod |
| 3 | Happy path bias | Missing error handling | Add try/catch |
| 4 | Cache stampede | Race conditions | Add locking |
| 5 | Memory leaks | Missing cleanup | Always unsubscribe |
| 6 | Auth leaks (IDOR) | No authorization | Add RBAC |
| 7 | Tunnel vision | Limited context | Provide files |
| 8 | God components | 500-line files | Break apart |
| 9 | Dead code | Never removed | Minimal first |
| 10 | Console.log | Wrong logging | Use logger |

### 9.3 Quick Validation Commands

Before delivering ANY code, verify:

| Check | Command |
| :--- | :--- |
| Duplication | `ast_grep` for similar functions |
| Runtime validation | `grep "z\."` or check for Zod |
| Memory leaks | `grep "subscribe"` → must have `unsubscribe` |
| Auth/IDOR | Check ownerId + role checks |
| Console.log | `grep "console.log"` in changed files |

---

## 10. Complete Installation Guide

### 10.1 Prerequisites

- OpenCode installed
- Node.js 25+ (or bun 1.3+)
- ripgrep (optional but recommended)
- mise (optional, version manager)

### 10.2 Step-by-Step Installation

```bash
# 1. Create config directory
mkdir -p ~/.config/opencode

# 2. Copy configuration files
cp opencode.json ~/.config/opencode/
cp oh-my-openagent.json ~/.config/opencode/
cp ``directives.md`` ~/.config/opencode/

# 3. Clone community skills
git clone https://github.com/chandima/opencode-config.git ~/projects/opencode-config
ln -s ~/projects/opencode-config/skills/* ~/.config/opencode/skills/

# 4. Create memory config
cat > ~/.config/opencode/opencode-mem.jsonc << 'EOF'
{
  "storagePath": "~/.opencode-mem/data",
  "embeddingModel": "Xenova/nomic-embed-text-v1",
  "webServerEnabled": true,
  "webServerPort": 4747,
  "autoCaptureEnabled": true
}
EOF

# 5. Restart OpenCode
# Close and reopen your terminal or session
```

### 10.3 Verification

```bash
# Check directives are loaded
opencode run "list my directives"

# Check skills are available
opencode run "what skills do you have"

# Check memory is working
opencode run "add test memory"
```

---

## 11. Troubleshooting

### Directives Not Loading

Check the path in oh-my-openagent.json:

```json
"prompt_append": "file://~/.config/opencode/directives.md"
```

Verify the file exists:

```bash
ls -la ~/.config/opencode/directives.md
```

### Skills Not Available

Verify skills are linked:

```bash
ls -la ~/.config/opencode/skills/
```

### Memory Not Working

Check opencode-mem is in plugins:

```bash
grep opencode-mem ~/.config/opencode/opencode.json
```

### Model Not Available

Some models may not be available in your region. Check the official model list or use an alternative.

---

## 12. Sources and References

### Official Documentation

- Official Docs: https://ohmyopenagent.com/docs
- oh-my-opencode: https://github.com/code-yeongyu/oh-my-opencode
- opencode-mem: https://github.com/tickernelz/opencode-mem

### Community Resources

- Skills: https://github.com/chandima/opencode-config
- Superpowers: https://github.com/obra/superpowers

### Additional Reading

For deeper understanding of AI coding tools and patterns, read the comprehensive analysis at: https://www.glukhov.org/ai-devtools/opencode/oh-my-opencode/

For deeper understanding of AI coding tools and patterns, read the comprehensive analysis at: https://www.glukhov.org/ai-devtools/opencode/oh-my-opencode/

For a curated list of OpenCode resources, configurations, and community tools, see: https://github.com/awesome-opencode/awesome-opencode

---

## Quick Reference Card

| Need | Solution |
| :--- | :--- |
| Install plugin | Add to `plugin` array in config |
| Change model | Update `model` in agent config |
| Add directive | Use `prompt_append` with file:// URI |
| Search memory | Use `memory({ mode: "search" })` |
| Check footguns | Use grep or memory search |
| Update docs | Edit ~/Notes/opencode/setup.md |

---

## 13. Video Resources

### Recommended YouTube Tutorials

| Video | Channel | Duration | Level |
| :--- | :--- | :--- | :--- |
| [OpenCode Tutorial for Beginners: Learn 90% in 25 Minutes](https://www.youtube.com/watch?v=QzqaZshQcJI) | Brandon Melville | 24:03 | Beginner |
| [15 Minutes to Fix Your AI Dev Workflow](https://www.youtube.com/watch?v=EOIzFMdmox8) | Darren Builds AI | 14:30 | Advanced |
| [OpenCode: FASTEST AI Coder + Opensource](https://www.youtube.com/watch?v=hJm_iVhQD6Y) | WorldofAI | 10:34 | Beginner |
| [Setup OpenCode on Ubuntu Linux](https://www.youtube.com/watch?v=dRkg_eV1Ifg) | ProgrammingKnowledge | 15:28 | Beginner |
| [OpenCode: FAST, Open Source and Intuitive AI Coder](https://www.youtube.com/watch?v=yyBm_K8xpAk) | Code With Nathan | ~15:00 | Beginner |
| [OpenCode CRASH Course - Complete Tutorial](https://www.youtube.com/watch?v=WXffHkvfRpM) | Data Science Basics | 1:00:46 | Beginner |
| [How to use OpenCode](https://www.youtube.com/watch?v=Jvonogc8MPg) | Riccardo Bellomi | 11:45 | Beginner |

### Video Summaries

**Learn 90% of OpenCode in 25 Minutes**
- Covers installation, core concepts (agents, rules, extensions)
- Demo creating Flask web app
- Skills and commands explanation
- Best for beginners

**15 Minutes to Fix Your AI Dev Workflow**
- Multi-agent architecture
- Sub-agents setup (code review, testing, docs)
- Context-aware coordination
- Best for advanced users

### Online Courses

| Course | Platform | Duration | Level |
| :--- | :--- | :--- | :--- |
| [The Complete AI Coding Course (2025)](https://www.udemy.com/course/the-complete-ai-coding-course-2025-cursor-ai-v0-vercel/) | Udemy | 11h 48m | Beginner |
| [Vibe Coding with Claude Code & OpenCode](https://www.udemy.com/course/ai-driven-development-with-claude-code-cli-and-opencode/) | Udemy | 1h 50m | Beginner |
| [OpenCode: The Complete Guide](https://www.educative.io/courses/learn-opencode) | Educative | 2h | Intermediate |

**Udemy Course Highlights:**

- "The Complete AI Coding Course" - Covers Cursor, Claude Code, OpenCode, v0, Replit
- 12+ hours of content, updated July 2025
- 17,696 students, 4.4 rating
- "Vibe Coding with OpenCode" - Hands-on project building

### GitHub Learning Resources

| Repository | Stars | Description |
| :--- | :--- | :--- |
| [wesammustafa/OpenCode-Everything-You-Need-to-Know](https://github.com/wesammustafa/OpenCode-Everything-You-Need-to-Know) | Latest | Comprehensive guide: Zen, TUI, agents, skills, MCP |
| [opencode-ai/opencode](https://github.com/opencode-ai/opencode) | 12K+ | Official Go implementation |
| [sudarshan-koirala/llm-resources](https://github.com/sudarshan-koirala/llm-resources/blob/main/opencode.md) | Latest | OpenCode resource collection |

---

## 14. Link Checker

### Manual Link Check

```bash
# Check all links in markdown files
npx markdown-link-check README.md --level failure

# Or use linkinator
npx linkinator "https://github.com/CodeSigils/opencode-setup-guide" --recurse --skip "(google|localhost)"
```

### Add to Pre-commit Hook

Create `.github/check-links.sh`:

```bash
#!/bin/bash
# Check all markdown links
npx markdown-link-check docs/*.md --level failure || exit 1
```

Make executable: `chmod +x .github/check-links.sh`

Add to `.github/hooks/pre-commit`:

```bash
#!/bin/bash
./.github/check-links.sh
```

---

## 15. Maintenance Tips

- **Update quarterly**: Check for new versions, features, and video content
- **Verify links monthly**: Use link checker or manual spot-check
- **Check OpenCode version**: `opencode --version`
- **Sync with repo**: Pull latest from `opencode-setup-guide`

---

Last updated: April 2026