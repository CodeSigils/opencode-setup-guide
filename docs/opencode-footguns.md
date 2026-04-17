# OpenCode Footguns - AI Anti-Patterns Guide

## Overview

Research from 2025-2026 reveals AI-generated code contains **significantly more defects** than human-written code. This guide documents common "foot guns" and how to avoid them.

---

## The 10 Anti-Patterns

### 1. Code Duplication

**Problem**: AI generates code without searching your codebase first.

| Data Point | Value |
| :--- | :--- |
| AI-generated duplication | 12.3% of lines |
| Human-only | 8.3% |

**Root cause**: AI produces what matches training patterns, not your existing utilities.

**Prevention**:
```bash
# Always search first
ast_grep for similar functions before generating
```

---

### 2. Phantom Validation (TypeScript Types ≠ Runtime)

**Problem**: AI generates beautiful interfaces but ZERO runtime validation.

```typescript
// ❌ USELESS at runtime
interface User { name: string; email: string }

// ✅ Use Zod for actual validation
import { z } from 'zod'
const UserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email()
})
```

**Root cause**: Types vanish at compile time.

**Prevention**: Prompt: "Add runtime validation with Zod"

---

### 3. The Happy Path Bias

**Problem**: AI generates code that "works when everything goes right".

| Distribution | Training Data | Production Code |
| :--- | :--- | :--- |
| Happy path | 85% | 20% |
| Error handling | 10% | 35% |
| Edge cases | 3% | 25% |

**Detection checklist**:
- [ ] `pool.connect()` has `release()` in `finally`?
- [ ] `fetch()` has retry with delay?
- [ ] `addEventListener()` has cleanup?
- [ ] Database queries parameterized?

---

### 4. The Cache Stampede

**Problem**: Cache code without race condition protection.

**Root cause**: Training data dominated by single-user examples.

**Prevention**: Prompt: "Add cache with locking/deduplication"

---

### 5. Memory Leaks (Missing Cleanup)

**Problem**: AI implements `subscribe` but forgets `unsubscribe`.

**Root cause**: Training data rarely shows cleanup code.

**Prevention**:
```typescript
// Always include cleanup
useEffect(() => {
  const sub = service.subscribe()
  return () => sub.unsubscribe() // CLEANUP
}, [])
```

---

### 6. Auth Context Leaks (IDOR Vulnerabilities)

**Problem**: Every authenticated user can access every resource.

**Root cause**: AI generates authentication without authorization.

**Prevention**: Prompt: "Add role-based access control, row-level security"

**Check**:
```bash
grep -r "WHERE.*id" --include="*.sql"
```

---

### 7. Tunnel Vision

**Problem**: AI only sees fraction of your codebase. Recreates existing logic.

**Root cause**: Default tools load limited context.

| Tool | Default Lines |
| :--- | :--- |
| Cursor | 250 lines |

**Prevention**:
- Provide full context files
- Explicitly search first
- Use `.opencode-rules` file

---

### 8. God Components

**Problem**: One massive 500-line component instead of smaller ones.

**Root cause**: Single prompt = single output.

**Prevention**: Break into iterative prompts.

---

### 9. Dead Code & Over-Engineering

**Problem**: "Might be useful" code that never gets removed.

| Anti-Pattern | Prevalence |
| :--- | :--- |
| Comments everywhere | 90-100% |
| Over-specification | 80-90% |

**Prevention**: Prompt: "Minimal implementation first"

---

### 10. Console.log Debugging

**Problem**: Uses `console.log` instead of proper logging.

**Prevention**:
```bash
# Find all console.log
grep -r "console.log" --include="*.ts"
```

---

## Better Prompts (Templates)

### Template 1: Safe API Handler
```
Create a handler that:
- Uses parameterized queries ONLY
- Includes error handling
- Has input validation (Zod)
- Returns proper HTTP codes
- NO console.log
```

### Template 2: Safe Database Query
```
Write a query that:
- Uses parameterized placeholders (? or $1)
- Validates input with Zod
- Has try/catch with proper error handling
- Returns meaningful error messages
```

### Template 3: Safe Frontend Component
```
Create a React component that:
- Uses existing UI patterns from codebase
- Includes loading/error states
- Has proper cleanup in useEffect
- Is properly typed with TypeScript
- NO console.log
```

---

## Token Efficiency Tips

| Technique | Tokens Saved |
| :--- | :--- |
| Short specific prompts | 30-50% |
| Reuse session_id | 70%+ |
| Reference existing code | Context savings |
| Incremental changes | Fewer tokens |

---

## Quick Reference Card

| Check | Command |
| :--- | :--- |
| Duplication | `ast_grep` |
| Auth issues | `grep "WHERE.*id"` |
| Console.log | `grep "console.log"` |
| Missing cleanup | `grep "subscribe"` |
| Runtime validation | `grep "z\."` |

---

## Sources

- [Toolpod Blog](https://toolpod.dev/blog/preventing-cursor-tunnel-vision-ai-coding)
- [Variant Systems](https://variantsystems.io/blog/vibe-code-anti-patterns)
- [Pockit](https://pockit.tools/blog/debugging-ai-generated-code-hidden-production-bugs-patterns-guide/)
- [Medium](https://medium.com/@montes.makes/lint-against-the-machine-a-field-guide-to-catching-ai-coding-agent-anti-patterns-3c4ef7baeb9e)
- [CodeRabbit Study](https://coderabbit.ai)