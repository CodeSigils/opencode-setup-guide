# OpenCode Best Practices - AI Anti-Patterns Guide

## Overview

Research from 2025-2026 reveals AI-generated code contains **significantly more defects** than human-written code. Studies show AI code has:
- **12.3%** code duplication (vs 8.3% human-only)
- More security vulnerabilities (IDOR, injection)
- More memory leaks (missing cleanup)
- More runtime errors (no validation)

This guide documents common "foot guns" - patterns that cause production bugs - and how to prevent them.

> **Why this matters**: Understanding these patterns helps you write better prompts, catch issues before they reach production, and use OpenCode more effectively.

---

## The Research Behind This Guide

### Key Findings

| Finding | Source |
| :--- | :--- |
| AI code has 2-3x more defects | CodeRabbit Study 2025 |
| 12.3% duplication rate in AI code | Pockit Analysis |
| Console.log in 90%+ of AI code | Variant Systems |
| Happy path dominates training (85%) | Multiple sources |

### Why AI Produces Defective Code

| Reason | Explanation |
| :--- | :--- |
| **Training data bias** | Most examples are happy-path, single-user |
| **Pattern matching** | AI replicates training patterns, not your code |
| **Limited context** | Can't see your entire codebase |
| **No runtime** | Produces TypeScript types with zero runtime validation |

---

### 1. Code Duplication

**The Problem**: AI generates code without searching your codebase first. It produces what matches its training patterns, not your existing utilities.

| Data Point | Value |
| :--- | :--- |
| AI-generated duplication | 12.3% of lines |
| Human-only | 8.3% |

**Why it happens**:
- AI models trained on public code don't know your utilities
- Pattern matching vs understanding your specific codebase
- Faster to generate than search

**Real-world impact**: Duplicated code means more bugs to fix in multiple places, inconsistent behavior, and harder maintenance.

**Prevention checklist**:
```bash
# Always search first
ast_grep for similar functions before generating
grep for existing utilities
```

**Better prompt template**:
```
Create a function [description], but FIRST search our codebase for existing utilities that do similar things.
```

---

### 2. Phantom Validation (TypeScript Types ≠ Runtime)

**The Problem**: AI generates beautiful TypeScript interfaces with ZERO runtime validation. Types vanish at compile time - they're just documentation.

```typescript
// ❌ USELESS at runtime - just documentation
interface User { 
  name: string; 
  email: string 
}

// ✅ Actually validates at runtime
import { z } from 'zod'
const UserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email()
})
```

**Why it happens**:
- TypeScript interfaces look like validation to AI
- Training data has many interfaces, few runtime validators
- Developer intent appears satisfied with types

**Real-world impact**: Invalid data passes through your API, crashes in production, requires hotfixes.

**Prevention**: Add to your directives or prompts:
- "Use Zod/Valibot for runtime validation"
- "Validate ALL user input at runtime"

**Better prompt template**:
```
Create TypeScript types that include RUNTIME validation using Zod. Not just interfaces - actual validation schemas.
```

---

### 3. The Happy Path Bias

**The Problem**: AI generates code that "works when everything goes right" - but production is full of edge cases, network failures, and invalid data.

| Distribution | Training Data | Production Code |
| :--- | :--- | :--- |
| Happy path | 85% | 20% |
| Error handling | 10% | 35% |
| Edge cases | 3% | 25% |

**Why it happens**:
- Training data is mostly happy-path examples
- Error handling is "boring" - not in training data
- Concise code is rewarded, verbose error handling isn't

**Detection checklist** - always verify these in AI code:
- [ ] `pool.connect()` has `release()` in `finally`?
- [ ] `fetch()` has retry with exponential backoff?
- [ ] `addEventListener()` has cleanup/removeEventListener?
- [ ] Database queries use parameterized inputs?
- [ ] File operations check for existence first?
- [ ] Network calls handle timeout scenarios?

**Better prompt template**:
```
Create a handler that handles ALL error cases:
- Network failures (timeout, 5xx errors)
- Invalid input (malformed data, missing fields)
- Resource exhaustion (connection pool full)
- Concurrent access (race conditions)
Use try/catch with specific error handling, not bare catches.
```

---

### 4. The Cache Stampede

**The Problem**: Cache code works perfectly in testing but collapses under production load. Multiple requests hit the cache simultaneously, all miss, all call the database.

**Why it happens**:
- Training data dominated by single-user examples
- Race conditions are hard to test locally
- "Simple cache" looks correct but isn't production-ready

**Real-world impact**: Database overload, service degradation, cascading failures.

**Prevention**: Add to your prompts:
- "Add cache with locking/deduplication"
- "Use mutex or single-flight pattern for cache misses"

### 5. Memory Leaks (Missing Cleanup)

**The Problem**: AI implements `subscribe` but forgets `unsubscribe`. Memory grows until service crashes.

**Why it happens**:
- Training data rarely shows cleanup code
- focus() on happy path, not lifecycle
- React useEffect without return is common

**Prevention**: ALWAYS verify cleanup exists:

```typescript
// ✅ Always include cleanup
useEffect(() => {
  const sub = service.subscribe()
  return () => sub.unsubscribe() // CLEANUP REQUIRED
}, [])

// ✅ Event listeners too
useEffect(() => {
  window.addEventListener('resize', handler)
  return () => window.removeEventListener('resize', handler)
}, [])
```

**Detection**:
```bash
# Find subscriptions without cleanup
grep -r "\.subscribe()" --include="*.ts"
grep -r "addEventListener" --include="*.ts"
```

### 6. Auth Context Leaks (IDOR Vulnerabilities)

**The Problem**: Every authenticated user can access every resource. Authentication exists but authorization is missing.

**Why it happens**:
- AI generates "logged-in only" endpoints
- "Authenticated" is not the same as "authorized"
- Training data has simplified auth patterns

**Real-world impact**: Data breaches, privacy violations, compliance failures.

**Prevention**: Always specify authorization:
- "Add role-based access control (RBAC)"
- "Add row-level security"
- "Verify user owns resource before returning"

**Security check**:
```bash
# Look for dangerous patterns
grep -r "WHERE.*id" --include="*.sql"
# Should be: WHERE id = $user_id
```

---

---

### 7. Tunnel Vision

**The Problem**: AI only sees a fraction of your codebase. It recreates existing logic because it can't see what you've already built.

**Why it happens**:
- Default tools load limited context (often ~250 lines)
- AI can't search your entire codebase in one prompt
- Creates duplicate implementations to "save time"

| Tool | Default Context |
| :--- | :--- |
| Cursor | ~250 lines |
| Claude Code | ~500 lines |
| OpenCode | Configurable, but limited by token budget |

**Prevention strategies**:
1. **Provide full context files** - Include relevant existing code in prompts
2. **Explicitly search first** - Ask "search for existing implementations"
3. **Use `.opencode-rules` file** - Describe your key utilities
4. **Reference patterns** - "Follow our utils/httpClient pattern"

```markdown
<!-- .opencode-rules in project root -->
Our key utilities:
- apiClient.ts - Our HTTP client with retry logic
- useQuery.ts - Our React Query wrapper
- formatDate.ts - Date formatting using date-fns
```

### 8. God Components

**The Problem**: One massive 500-line component instead of smaller, focused ones.

**Why it happens**:
- Single prompt = single output
- Refactoring requires multiple steps AI won't take
- "Get it working" > "get it right"

**Prevention**: Break into iterative prompts:
```
First: Create the basic structure
Then: Extract into smaller components
Finally: Add proper composition
```

### 9. Dead Code & Over-Engineering

**The Problem**: "Might be useful" code that never gets used. 90-100% of AI-generated comments are unnecessary.

| Anti-Pattern | Prevalence |
| :--- | :--- |
| Comments everywhere | 90-100% |
| Over-specification | 80-90% |
| Unused exports | Common |

**Prevention**: Add to prompts:
- "Minimal implementation first"
- "Delete unused code"
- "Remove unnecessary comments"

### 10. Console.log Debugging

**The Problem**: Uses `console.log` instead of proper logging. 90%+ of AI code uses console.log.

**Why it happens**:
- It's the simplest way to show output
- Works in dev environment
- Real logging requires setup

**Prevention**:
```bash
# Find all console.log - these are debugging leftovers
grep -r "console.log" --include="*.ts"
```

**Better alternative**: Use structured logger (pino, winston, etc.)

---

---

## Better Prompts (Templates)

Use these templates to prevent common issues. Each specifies exactly what you expect:

### Template 1: Safe API Handler

```
Create a handler that:
- Uses parameterized queries ONLY (no string concatenation)
- Includes error handling with try/catch
- Has input validation (Zod or similar)
- Returns proper HTTP codes (200, 400, 500)
- NO console.log - use proper logger
- Handles timeout and network failures
```

### Template 2: Safe Database Query

```
Write a query that:
- Uses parameterized placeholders (? or $1) - NEVER interpolate
- Validates input with Zod before query
- Has try/catch with proper error handling
- Returns meaningful error messages (not exposing internals)
- Releases resources in finally block if needed
```

### Template 3: Safe Frontend Component

```
Create a React component that:
- Uses existing UI patterns from OUR codebase (look first)
- Includes loading/error states (never just happy path)
- Has proper cleanup in useEffect (return function)
- Is properly typed with TypeScript (not just any)
- NO console.log - remove before finalizing
- Is responsive (works on mobile)
```

### Template 4: Safe State Management

```
Create state management that:
- Handles race conditions (deduplicate requests)
- Has cleanup on unmount (prevent memory leaks)
- Persists only necessary state
- Has proper TypeScript types
- Uses existing patterns from codebase
```

---

## Token Efficiency Tips

Getting the most out of your context window:

| Technique | Tokens Saved | How |
| :--- | :--- | :--- |
| **Short specific prompts** | 30-50% | "Fix auth bug" vs "please carefully analyze the authentication system and fix any bugs you find" |
| **Reuse session_id** | 70%+ | Continue conversations instead of starting fresh |
| **Reference existing code** | Context savings | "Follow the pattern in utils/api.ts" instead of explaining the pattern |
| **Incremental changes** | Fewer tokens | Small, focused changes vs big rewrites |
| **Skip boilerplate** | 20-40% | Don't include files that don't need modification |

### Session Continuity Pattern

```typescript
// ✅ Good: Continue with session_id
task(session_id="ses_abc123", prompt="Fix the auth error")

// ❌ Bad: Start fresh
task(prompt="Fix the auth error")  // Loses context
```

---

## Quick Reference Card

Use this to quickly check AI-generated code:

| Check | Command | What to Look For |
| :--- | :--- | :--- |
| Duplication | `ast_grep` or `grep` | Same logic exists? |
| Auth issues | `grep "WHERE.*id"` | Missing authorization? |
| Console.log | `grep "console.log"` | Debugging leftovers? |
| Missing cleanup | `grep "subscribe"` | Leaky subscriptions? |
| Runtime validation | `grep "z\."` | Has Zod/valibot? |
| Parameters | `grep "WHERE"` | SQL injection risk? |
| Error handling | Look for try/catch | Bare catches? |

---

---

## Sources and Further Reading

### Research Papers & Studies
- [CodeRabbit Study](https://coderabbit.ai) - AI code quality analysis 2025
- [Pockit Analysis](https://pockit.tools/blog/debugging-ai-generated-code-hidden-production-bugs-patterns-guide/) - Production bug patterns

### Blog Posts
- [Toolpod: Preventing Cursor Tunnel Vision](https://toolpod.dev/blog/preventing-cursor-tunnel-vision-ai-coding)
- [Variant Systems: Vibe Code Anti-Patterns](https://variantsystems.io/blog/vibe-code-anti-patterns)
- [Medium: Lint Against the Machine](https://medium.com/@montes.makes/lint-against-the-machine-a-field-guide-to-catching-ai-coding-agent-anti-patterns-3c4ef7baeb9e)

### Related Documentation
- [README](../README.md) - Setup guide
- [Setup Guide](../setup.md) - Comprehensive setup
- [Agents](../agents.md) - Agent configuration

---

*Last updated: April 2026*