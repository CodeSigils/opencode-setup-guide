# OpenCode Skills - Comprehensive Guide

## Overview

Skills are reusable instruction bundles that encode team conventions, workflows, and best practices. They allow you to teach OpenCode about your team's specific ways of working, so you don't have to repeat the same instructions every session.

> **Learning Note**: Skills are different from directives. Directives are always-loaded rules; skills are loaded **on-demand**. See the [Setup Guide](./setup.md) for the detailed comparison.

---

## What Are Skills?

A skill is a reusable instruction bundle defined in a SKILL.md file. Think of it as an **onboarding document** for your AI assistant - it tells OpenCode how your team works, what conventions you follow, and when to use specific approaches.

### Real-World Analogy

| Concept | Real-World Analogy |
| :--- | :--- |
| **Skill** | Employee handbook |
| **Directive** | Company policy (always in effect) |
| **Agent** | Specialized department (debugging, research) |

### How Skills Differ from Directives

| Feature | Directives | Skills |
| :--- | :--- | :--- |
| **Load timing** | Every session automatically | On-demand when needed |
| **Scope** | General rules | Specific workflows |
| **Triggered by** | Session start | Agent recognition of need |
| **Persistence** | Via prompt_append | Via skill tool |
| **File size** | ~300 lines max | Flexible, can be longer |
| **Use case** | Universal rules | Team-specific conventions |

### When to Use Skills vs Directives

| Use Case | Use This |
| :--- | :--- |
| "Always verify docs first" | Directive |
| "Use Zod for validation" | Directive |
| "How we do Docker Compose" | Skill |
| "Our code review process" | Skill |
| "Testing patterns we follow" | Skill |

### Example: Custom Skill for Team Conventions

Here's a skill that teaches OpenCode your team's testing conventions:

```yaml
---
name: team-testing
description: Our team's testing conventions and patterns
license: MIT
compatibility: opencode
---

## What I do

- Enforce our testing patterns (describe/it blocks)
- Require meaningful test names
- Ensure proper cleanup in afterEach
- Validate mock usage

## When to use me

Use this skill whenever:
- Writing new tests
- Reviewing test files
- Fixing broken tests
- Adding test coverage

## Our Testing Rules

1. **File naming**: `*.test.ts` or `*.spec.ts`
2. **Structure**: describe blocks for feature, it for specific behavior
3. **Naming**: "should [expected behavior] when [condition]"
4. **Cleanup**: Always clean up mocks in afterEach
5. **Coverage**: Aim for 80%+ coverage on new code
```

---

## Where Skills Live

| Location | Path | Scope |
| :--- | :--- | :--- |
| Project | `.opencode/skills/` | Project-local |
| Global | `~/.config/opencode/skills/` | User-wide |

---

## Skills Comparison

### superpowers vs oh-my-openagent

These are complementary, not competing, technologies:

| Aspect | superpowers | oh-my-openagent |
| :--- | :--- | :--- |
| **Type** | Skills + methodology | Core AI coding agent |
| **Purpose** | Enforce processes | Code generation |
| **Stars** | 157k+ | 25k+ |
| **Works with** | Claude, Codex, Cursor | OpenCode only |
| **Approach** | Workflow methodology | Agent enhancements |
| **Origin** | DHH (Ruby on Rails creator) | OpenCode community |

**They complement each other:**

| Layer | Technology | Role |
| :--- | :--- | :--- |
| Core | oh-my-openagent | Base AI coding agent |
| Process | superpowers | Workflow methodology |
| Rules | directives.md | Persistent rules |
| Memory | opencode-mem | Semantic search |

### When to Use Each

| Situation | Recommendation |
| :--- | :--- |
| New to AI coding | Start with superpowers methodology |
| OpenCode power user | Focus on oh-my-openagent agents |
| Team standardization | Combine both for comprehensive approach |

---

## Built-in Skills (oh-my-opencode)

These skills come with the oh-my-openagent plugin and are ready to use:

| Skill | Purpose | When to Use |
| :--- | :--- | :--- |
| **playwright** | Browser automation | Testing, web scraping, UI verification |
| **git-master** | Atomic commits, rebase | Complex git operations, history cleanup |
| **frontend-ui-ux** | Design-first UI | Creating new UI components, layouts |
| **ai-slop-remover** | Remove AI code smells | Cleaning up AI-generated code |
| **review-work** | Post-impl review | After completing significant implementation |

### Using Built-in Skills

Simply reference them in your prompts:

```
Use the git-master skill to help me do an interactive rebase
Use the playwright skill to test this login flow
Use the ai-slop-remover to clean up this generated code
```

---

## Community Skills

The OpenCode community has created many useful skills. Here are the recommended ones:

### Recommended Skills

| Skill | Source | Purpose | GitHub |
| :--- | :--- | :--- | :--- |
| **code-checklist** | Custom | Your team's 17 directive rules | N/A |
| **context7-docs** | chandima | Official docs lookup via Context7 | chandima/opencode-config |
| **github-ops** | chandima | GitHub operations (PRs, issues) | chandima/opencode-config |
| **security-auditor** | chandima | Security scanning | chandima/opencode-config |
| **staff-pr-review** | spartandingo | PR reviews from staff engineer | spartandingo/opencode-skills |
| **docker-compose** | Community | Docker Compose patterns | (search opencode-config) |
| **test-patterns** | Community | Testing conventions | (search opencode-config) |

### Installing Community Skills

```bash
# Clone the community config
git clone https://github.com/chandima/opencode-config.git ~/projects/opencode-config

# Link skills to your global skills folder
ln -s ~/projects/opencode-config/skills/* ~/.config/opencode/skills/

# Verify
ls -la ~/.config/opencode/skills/
```

### superpowers (Optional Workflow)

For comprehensive workflow methodology (157k+ stars), add superpowers:

```bash
git clone https://github.com/obra/superpowers.git ~/projects/superpowers
ln -s ~/projects/superpowers/skills/* ~/.config/opencode/skills/
```

> **Note**: superpowers works with multiple AI assistants (Claude, Codex, Cursor), not just OpenCode. It's a methodology first, not OpenCode-specific.

---

## Creating Custom Skills

### Option 1: Manual Creation

Manual creation gives you full control:

```bash
# Create skill directory
mkdir -p ~/.config/opencode/skills/your-skill-name

# Create SKILL.md with proper format
cat > ~/.config/opencode/skills/your-skill-name/SKILL.md << 'EOF'
---
name: your-skill-name
description: Description of what this skill does
license: MIT
compatibility: opencode
---

## What I do

- First capability
- Second capability

## When to use me

Use this skill when [specific trigger situation].
EOF
```

### Option 2: Using skill-creator

The skill-creator tool helps scaffold skills:

```bash
# Install globally
npx opencode-skill-creator install --global

# Then ask OpenCode directly:
Create a skill that helps with Docker compose
```

### Best Practices for Custom Skills

| Practice | Why It Matters |
| :--- | :--- |
| Specific triggers | Clear when to load = better relevance |
| Concrete examples | AI understands with code samples |
| Team-specific | Capture YOUR team's conventions, not generic |
| Version control | Track changes to skills over time |
| Test prompts | Verify skill works as expected |

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

Control skill access in opencode.json - useful for teams who want to restrict certain skills:

```json
{
  "permission": {
    "skill": {
      "*": "allow",           // Allow all skills by default
      "internal-*": "deny"   // Deny internal/system skills
    }
  }
}
```

| Permission | Behavior |
| :--- | :--- |
| `"*": "allow"` | All skills can be loaded |
| `"*": "deny"` | No skills can be loaded |
| `"*": "ask"` | User must approve each skill load |
| Specific patterns | Override for specific skills |

---

## Troubleshooting

### Skills Not Loading

**Symptom**: Skill doesn't activate when expected

**Solutions**:
1. Check skill is in correct location: `~/.config/opencode/skills/`
2. Verify YAML frontmatter is valid (no typos)
3. Ensure naming follows regex: `^[a-z0-9]+(-[a-z0-9]+)*$`
4. Check file permissions: `ls -la` shows read access

### Skill Not Recognized

**Symptom**: "Unknown skill" error

**Solutions**:
1. Verify skill name matches folder name exactly
2. Check skill is not blocked in permissions
3. Try with full name: `skill({ name: "full-skill-name" })`

### Skill Conflicts with Directives

**Symptom**: Skill instructions ignored or overridden

**Solution**: Directives have priority. Keep skill-specific rules in skills, universal rules in directives.

---

## Quick Reference

| Need | Solution |
| :--- | :--- |
| List all available skills | `skill` tool description |
| Load specific skill | `skill({ name: "skill-name" })` |
| Create new skill | `mkdir + SKILL.md` |
| Test skill | Create test prompts |
| Find skill source | Check skills folder or community repos |

---

## Related

- [README](../README.md) - Setup guide
- [Setup Guide](../setup.md) - Comprehensive setup
- [Agents](../agents.md) - Agent configuration
- [Best Practices](../best-practices.md) - AI anti-patterns

---

## References

### Official
- [OpenCode Skills Documentation](https://dev.opencode.ai/docs/skills/)
- [Official Skill Examples](https://github.com/anomalyco/opencode/tree/main/skills)

### oh-my-openagent
- [GitHub Repository](https://github.com/code-yeongyu/oh-my-openagent)
- [oh-my-opencode Plugin](https://www.npmjs.com/package/oh-my-openagent)

### Community
- [chandima/opencode-config](https://github.com/chandima/opencode-config) - Community skills collection
- [spartandingo/opencode-skills](https://github.com/spartandingo/opencode-skills) - PR review skills
- [superpowers](https://github.com/obra/superpowers) - Workflow methodology (157k+ stars)
- [skill-creator](https://github.com/antongulin/opencode-skill-creator) - Skill scaffolding tool

---

*Last updated: April 2026*