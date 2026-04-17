# OpenCode Skills - Comprehensive Guide

## Overview

Skills are reusable instruction bundles that encode team conventions, workflows, and best practices.

---

## What Are Skills?

A skill is a reusable instruction bundle defined in a SKILL.md file.
Think of it as an onboarding document for your AI assistant.

### How Skills Differ from Directives

| Feature | Directives | Skills |
| :--- | :--- | :--- |
| Load timing | Every session | On-demand |
| Scope | General rules | Specific workflows |
| Triggered by | Session start | Agent recognition |

---

## Skills File Structure

### SKILL.md Format

Every skill must have YAML frontmatter:

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

### Naming Rules

- 1-64 characters
- Lowercase letters and numbers
- Single hyphens as separators
- Regex: ^[a-z0-9]+(-[a-z0-9]+)*$

---

## Where Skills Live

| Location | Path | Scope |
| :--- | :--- | :--- |
| Project | `.opencode/skills/` | Project-local |
| Global | `~/.config/opencode/skills/` | User-wide |

---

## Skills Comparison

### superpowers vs oh-my-openagent

| Aspect | superpowers | oh-my-openagent |
| :--- | :--- | :--- |
| Type | Skills + methodology | Core AI coding agent |
| Purpose | Enforce processes | Code generation |
| Stars | 157k+ | 25k+ |
| Works with | Claude, Codex, Cursor | OpenCode only |

**They complement each other:**

| Layer | Technology |
| :--- | :--- |
| Core | oh-my-openagent |
| Process | superpowers |
| Rules | directives.md |
| Memory | opencode-mem |

---

## Built-in Skills (oh-my-opencode)

| Skill | Purpose |
| :--- | :--- |
| playwright | Browser automation |
| git-master | Atomic commits, rebase |
| frontend-ui-ux | Design-first UI |
| ai-slop-remover | Remove AI code smells |
| review-work | Post-impl review |

---

## Community Skills

### Recommended Skills

| Skill | Source | Purpose |
| :--- | :--- | :--- |
| code-checklist | Custom | Your rules |
| context7-docs | chandima | Docs lookup |
| github-ops | chandima | GitHub ops |
| security-auditor | chandima | Security audit |
| staff-pr-review | spartandingo | PR reviews |

---

## Creating Custom Skills

### Option 1: Manual

```bash
mkdir -p ~/.config/opencode/skills/your-skill-name
```

Option 2: Using skill-creator

```bash
npx opencode-skill-creator install --global
```

Then ask OpenCode:

```
Create a skill that helps with Docker compose
```

---

## Installation

### Clone Community Skills

```bash
git clone https://github.com/chandima/opencode-config.git ~/projects/opencode-config
ln -s ~/projects/opencode-config/skills/* ~/.config/opencode/skills/
```

### superpowers (Optional)

```bash
git clone https://github.com/obra/superpowers.git ~/projects/superpowers
ln -s ~/projects/superpowers/skills/* ~/.config/opencode/skills/
```

---

## Permission Configuration

Control skill access in opencode.json:

```json
{
  "permission": {
    "skill": {
      "*": "allow",
      "internal-*": "deny"
    }
  }
}
```

---

## Quick Reference

| Need | Solution |
| :--- | :--- |
| List skills | skill tool description |
| Load skill | skill({ name: "skill-name" }) |
| Create skill | mkdir + SKILL.md |
| Test | Create test prompts |

---

## References

- Official: https://dev.opencode.ai/docs/skills/
- oh-my-opencode: https://github.com/code-yeongyu/oh-my-opencode
- superpowers: https://github.com/obra/superpowers
- skill-creator: https://github.com/antongulin/opencode-skill-creator

---

Last updated: April 2026