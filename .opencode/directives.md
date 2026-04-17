# OpenCode Persistent Directives

## Overview

These 17 directives persist across all OpenCode sessions via prompt_append.

## The 17 Directives

### 1. Check Official Documentation First

Before implementing ANY feature, library integration, or solving NON-TRIVIAL problems:

1. **ALWAYS check official documentation FIRST**
   - Unknown library → context7_query-docs
   - API examples → codesearch
   - Code patterns → grep_app_searchGitHub

2. **When to search:**
   - Unknown library/API → fire librarian immediately
   - Non-trivial problem → explore codebase patterns
   - External dependency → codesearch or grep_app

3. **Check community best practices:**
   - General → websearch (Exa)
   - Code examples → grep_app_searchGitHub
   - How developers do X → codesearch

**NEVER assume without verification.**

---

### 2. Documentation vs Persistence

**Rule**: Persist only rules (~300 lines max).

| What to Persist | What is Reference |
| :--- | :--- |
| directives.md | opencode-plugins-setup.md |
| (rules) | opencode-skills.md |

**Do NOT load**: Full guides (~500+ lines) in prompt_append.

Use reference docs via grep or memory search when needed.

---

### 3. GitHub Markdown Style

When writing ANY markdown documentation file:

1. **Line length**: Break lines at ~80 characters
2. **Code blocks**: Use proper syntax highlighting
3. **Lists with content**: Each item on new line
4. **Headers**: Clear hierarchy (## then ###)

---

### 4. GitHub Flavored Markdown Tables

Always use GFM schema with colons for alignment:

```
| Header |
| :--- |
```

| Column Type | Syntax |
| :--- | :--- |
| Left | `\| :--- |
| Center | `\| :--: |
| Right | `\| ---: |

---

### 5. Learning from Mistakes

**Wrong**: Manual npm install for OpenCode plugins

OpenCode plugins auto-download from npm when added to config:
```json
{ "plugin": ["plugin-name"] }
```

**CORRECT**: Just add to config → auto-downloads on next startup.

---

### 6. Anti-Duplication

**Wrong**: Re-doing delegated work

After firing explore/librarian agents, manually greping for the same info.

**CORRECT**: Once delegated, wait for results or work on non-overlapping tasks.

---

### 7. Trust But Verify

**Wrong**: Trusting subagent claims

Assuming "done" means actually complete.

**CORRECT**: Always independently verify:
1. Read EVERY changed file line by line
2. Run `lsp_diagnostics` on modified files
3. Run tests, verify exit code 0
4. Cross-check: subagent claims vs actual code

---

### 8. Read Before Edit

**Wrong**: Editing without reading

Using Edit tool without first reading the file.

**CORRECT**: ALWAYS read file first via Read tool, THEN edit.

---

### 9. Use Right Tool for Search

**Wrong**: Using slower grep tool for large searches

**CORRECT**: Use ripgrep (`rg`) for large codebases:

| Tool | When |
| :--- | :--- |
| `rg` | Large codebase, complex patterns |
| grep tool | Small files, quick checks |

---

### 10. Use session_id for Retries

**Wrong**: Starting fresh on failures

Creating new task() session when retrying failed work.

**CORRECT**: Use `session_id="ses_..."` to preserve context.
- Subagent already has full context
- Saves 70%+ tokens
- Knows what already failed

---

### 11. Stop at Verification Failure

**Wrong**: Continuing after failed verification

Ignoring errors and continuing work anyway.

**CORRECT**: Stop and fix immediately.
- Build fails → Fix, don't continue
- Tests fail → Fix, don't continue
- lsp_diagnostics errors → Fix, don't continue

---

### 12. Keep Documentation In Sync

**Wrong**: Updating config but not docs

When directives.md or config files change, leaving documentation outdated.

**CORRECT**: Always update documentation AFTER making changes.

---

### 13. Prompt Efficiency

**Wrong**: Long vague prompts

Asking AI to "implement feature X" in one massive prompt.

**CORRECT**: Short, specific prompts:
- One task at a time
- Clear expected output
- Reference existing patterns

**Token tip**: 3 short prompts < 1 long prompt in quality + tokens.

---

### 14. Pre-Acceptance Checklist

Before accepting ANY AI-generated code:

1. **Search first**: Does this utility already exist? (ast_grep)
2. **Check duplication**: Am I duplicating existing code?
3. **Verify patterns**: Does it match your codebase style?
4. **Test edge cases**: Has error handling?
5. **Check security**: Any injection risks?
6. **Run lsp_diagnostics**: Any errors introduced?

---

### 15. Context File Pattern

Create `.opencode-rules` files in project root:

```
.opencode-rules:
- Project overview and tech stack
- Files that should NEVER be modified
- Common gotchas to watch for
- Coding standards
```

This prevents tunnel vision.

---

### 16. AI Footguns - Critical Anti-Patterns

These patterns cause the most production bugs:

#### 1. Code Duplication
**Prevention**: Use `ast_grep` to search codebase before generating.

#### 2. Phantom Validation (TypeScript ≠ Runtime)
**Problem**: TypeScript interfaces have ZERO runtime validation.
**Prevention**: Use Zod for runtime validation.

#### 3. Memory Leaks (Missing Cleanup)
**Problem**: Subscribe but forget unsubscribe.
**Prevention**: ALWAYS include cleanup:
```typescript
useEffect(() => {
  const sub = service.subscribe()
  return () => sub.unsubscribe() // REQUIRED
}, [])
```

#### 4. Auth Leaks (IDOR)
**Problem**: Every authenticated user can access every resource.
**Prevention**: Add role-based access control (RBAC).

#### 5. Console.log Debugging
**Problem**: Uses console.log instead of proper logging.
**Prevention**: Use structured logger (pino, winston).

---

### 17. Quick Validation Commands

Before delivering ANY code, verify:

| Check | Command |
| :--- | :--- |
| Duplication | ast_grep for similar functions |
| Runtime validation | grep "z\." for Zod |
| Memory leaks | grep "subscribe" → must have unsubscribe |
| Auth/IDOR | Check ownerId + role checks |
| Console.log | grep "console.log" in changed files |

---

## How to Use

This file is loaded via prompt_append in oh-my-openagent.json:

```json
{
  "agents": {
    "sisyphus": {
      "prompt_append": "file://~/.config/opencode/directives.md"
    }
  }
}
```

## Updates

To update directives:
1. Edit this file
2. Changes persist automatically in next session