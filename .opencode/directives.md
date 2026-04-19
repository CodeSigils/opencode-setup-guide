# Always Check Official Documentation

## MANDATORY DIRECTIVE

Before implementing ANY feature, library integration, or solving NON-TRIVIAL problems:

1. **ALWAYS check official documentation FIRST**
   - Unknown library/API → context7_query-docs
   - Use codesearch for API examples
   - Use grep_app_searchGitHub for code patterns

2. **When to search:**
   - Unknown library/API → fire librarian immediately
   - Non-trivial problem → explore codebase patterns
   - External dependency → codesearch or grep_app

3. **Check community best practices:**
   - General "best practices X" → websearch
   - Code examples → grep_app_searchGitHub
   - How developers do X → codesearch

5. **Check trusted sources:**
   - Official docs (context7)
   - GitHub examples (grep_app)
   - Official repositories
   - Community discussions

**NEVER assume without verification.**

---

## Documentation vs Persistence

### ❌ WRONG: Loading full guides in prompt_append

Adding large reference docs (~500+ lines) to prompt_append.

**CORRECT**: Persist only rules (~300 lines max).

| What to Persist | What is Reference |
| :--- | :--- |
| directives.md | opencode-plugins-setup.md |
| (rules) | opencode-skills.md |
| | opencode-footguns.md |

### How to use reference docs:

- Search directly: `grep "pattern" ~/Notes/opencode-*.md`
- Memory search: `memory({ mode: "search", query: "..." })`
- Do NOT load in prompt_append

**Why**: Consumes tokens in every session. Reference docs are TOO LONG.

---

## GitHub Markdown Style

### General Rule

When writing ANY markdown documentation file:

1. **Line length**: Break lines at ~80 characters
   - Long descriptions → break into 2+ lines
   - Improves readability on narrow screens

2. **Code blocks**: Use proper syntax highlighting
   ```markdown
   ```javascript
   // code here
   ```
   ```

3. **Lists with content**: Each item on new line
   - Don't jam multiple items on one line

4. **Headers**: Clear hierarchy (## then ###)
   - No skipping levels

### GitHub Flavored Markdown Tables

### ❌ WRONG: Invalid table separator

Using dashes without alignment markers:
```
| Header |
|--------|
```

**CORRECT**: Use GFM schema with colons for alignment:
```
| Header |
| :----- |
```

| Column Type | Syntax |
| :--------- | :------ |
| Left | `| :--- |`
| Center | `| :--: |`
| Right | `| ---: |`

**Minimum required**: At least one `| :--- |` row.

---

## Learning from Mistakes

### ❌ WRONG: Manual npm install for OpenCode plugins

OpenCode plugins auto-download from npm when you add them to config:
```json
{ "plugin": ["plugin-name"] }
```

**DO NOT run** `npm install plugin-name` - it's redundant and wasteful.

**CORRECT**: Just add to config → auto-downloads on next startup.

**RECOMMENDED**: Use bun for local dependency management (faster, no version conflicts). If using npm/pnpm, remove `package-lock.json` to avoid conflicts with bun.lock.

---

## Anti-Duplication

### ❌ WRONG: Re-doing delegated work

After firing explore/librarian agents, manually greping for the same info.

**CORRECT**: Once delegated, wait for results or work on non-overlapping tasks.
- Background agents: use `background_output(task_id)`
- DO NOT manually search same topics while waiting

---

## Trust But Verify

### ❌ WRONG: Trusting subagent claims

Assuming "done" means actually complete.

**CORRECT**: Always independently verify:
1. Read EVERY changed file line by line
2. Run `lsp_diagnostics` on modified files
3. Run tests, verify exit code 0
4. Cross-check: subagent claims vs actual code

---

## Read Before Edit

### ❌ WRONG: Editing without reading

Using Edit tool without first reading the file.

**CORRECT**: ALWAYS read file first via Read tool, THEN edit.
- "I saw the error" → Did you READ the file to see it?

---

## Use Right Tool for Search

### ❌ WRONG: Using slower grep tool for large searches

Using built-in grep tool for large codebases.

**CORRECT**: Use ripgrep (`rg`) for large codebases:
- 10x faster than grep
- Ignores .gitignore automatically
- Better regex support

| Tool | When |
| :--- | :--- |
| `rg` | Large codebase, complex patterns |
| grep tool | Small files, quick checks |

Example speedup: `rg "pattern" .` vs `grep -r "pattern" .`

---

## Use session_id for Retries

### ❌ WRONG: Starting fresh on failures

Creating new task() session when retrying failed work.

**CORRECT**: Use `session_id="ses_..."` to preserve context.
- Subagent already has full context
- Saves 70%+ tokens
- Knows what already failed

---

## Stop at Verification Failure

### ❌ WRONG: Continuing after failed verification

Ignoring errors and continuing work anyway.

**CORRECT**: Stop and fix immediately.
- Build fails → Fix, don't continue
- Tests fail → Fix, don't continue
- lsp_diagnostics errors → Fix, don't continue

---

## Keep Documentation In Sync

### ❌ WRONG: Updating config but not docs

When directives.md or config files change, leaving ~/Notes/opencode-plugins-setup.md outdated.

**CORRECT**: Always update documentation AFTER making changes:
- Adding new directives → Update docs
- Changing models → Update docs
- Adding/modifying plugins → Update docs
- Any setup changes → Update docs

**Files to keep in sync:**
| File | Location |
| :--- | :--- |
| Directives | ~/.config/opencode/directives.md |
| Setup Doc | ~/Notes/opencode-plugins-setup.md |

---

## Prompt Efficiency

### ❌ WRONG: Long vague prompts

Asking AI to "implement feature X" in one massive prompt.

**CORRECT**: Short, specific prompts:
- One task at a time
- Clear expected output
- Reference existing patterns

**Token tip**: 3 short prompts < 1 long prompt in quality + tokens.

---

## Pre-Acceptance Checklist

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

---

## Context File Pattern

Create `.opencode-rules` files in project root:

```
.opencode-rules:
- Project overview and tech stack
- Files that should NEVER be modified
- Common gotchas to watch for
- Coding standards
```

This prevents tunnel vision - AI only sees fraction of codebase.

---

## AI Footguns - Critical Anti-Patterns

These 5 patterns cause the most production bugs in AI-generated code. ALWAYS check for these BEFORE delivering code.

### 1. Code Duplication

**Prevention**: Use `ast_grep` to search codebase before generating.
- AI generates what matches training patterns, not your existing utilities
- ALWAYS search first: `ast_grep` for similar functions

### 2. Phantom Validation (TypeScript ≠ Runtime)

**Problem**: TypeScript interfaces have ZERO runtime validation.
- Types vanish at compile time
- AI generates beautiful interfaces but no actual protection

**Prevention**: Use Zod for runtime validation.
```typescript
// ❌ USELESS at runtime
interface User { name: string; email: string }

// ✅ Use Zod for actual validation
const UserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email()
})
```

### 3. Memory Leaks (Missing Cleanup)

**Problem**: Subscribe but forget unsubscribe.
- Training data rarely shows cleanup code
- useEffect without return = leak

**Prevention**: ALWAYS include cleanup.
```typescript
useEffect(() => {
  const sub = service.subscribe()
  return () => sub.unsubscribe() // REQUIRED CLEANUP
}, [])
```

### 4. Auth Leaks (IDOR)

**Problem**: Authentication without authorization.
- Every authenticated user can access every resource
- No role-based access control

**Prevention**: Add role-based access control (RBAC), row-level security.
```typescript
// Check ownership BEFORE returning data
if (resource.ownerId !== currentUser.id && !currentUser.roles.includes('admin')) {
  throw new ForbiddenError()
}
```

### 5. Console.log Debugging

**Problem**: Uses `console.log` instead of proper logging.

**Prevention**: Use structured logger (pino, winston).
- No `console.log` in delivered code
- Use proper log levels (info, warn, error)

---

## Quick Validation Commands

Before delivering ANY code, verify:

| Check | Command |
| :--- | :--- |
| Duplication | `ast_grep` for similar functions |
| Runtime validation | `grep -r "z\."` or check for Zod |
| Memory leaks | `grep "subscribe"` → must have `unsubscribe` |
| Auth/IDOR | Check ownerId + role checks exist |
| Console.log | `grep "console.log"` in changed files |