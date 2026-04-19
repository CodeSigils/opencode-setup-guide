---
name: code-checklist
description: Load my 16 directives and validation rules for code delivery
license: MIT
compatibility: opencode
---

# Code Checklist

This skill loads my personal directives and validation rules
for code delivery.

## What I Do

I enforce 16 directives that must be followed for every task.

## Loaded Directives

### Core Rules (Run First)

1. **Check Official Documentation**
   - Unknown library → context7_query-docs
   - API examples → codesearch
   - Code patterns → grep_app_searchGitHub

2. **Check Community Best Practices**
   - General → websearch
   - Code examples → grep_app_searchGitHub
   - How developers do X → codesearch

3. **Documentation vs Persistence**
   - Persist rules (~300 lines max)
   - Reference docs: load on-demand via grep/memory

4. **Trust But Verify**
   - Read EVERY changed file
   - Run lsp_diagnostics
   - Run tests, verify exit 0
   - Cross-check claims vs actual code

### Search Rules

1. **Use Right Tool for Search**
   - `rg` (ripgrep) for large codebases
   - Build-in grep for small files

2. **Anti-Duplication**
   - Don't re-do delegated work manually
   - Wait for background agents

### Code Quality

1. **Read Before Edit**
   - Always read file first

2. **Quick Validation**
   - Check: ast_grep for similar functions
   - Check: grep "z\." for Zod validation
   - Check: grep "subscribe" → has unsubscribe?
   - Check: grep "console.log" in changed files

### Session Management

1. **session_id for Retries**
   - Preserve context, save tokens

2. **Stop at Verification Failure**
   - Build fails → Fix
   - Tests fail → Fix

### Writing Standards

1. **GitHub Markdown Style**
   - Break lines at ~80 chars
   - Code blocks with syntax highlighting
   - Clear header hierarchy

2. **GitHub Flavored Markdown Tables**
   - Use | :--- | for column alignment

3. **Prompt Efficiency**
   - Short, specific prompts
   - One task at a time

### Anti-Patterns (AI Footguns)

1. **Code Duplication**
   - Prevention: ast_grep before generating

2. **Phantom Validation (TS != Runtime)**
   - Use Zod for runtime validation

3. **Memory Leaks**
   - Subscribe must have unsubscribe

4. **Auth Leaks (IDOR)**
   - Add role-based access control

5. **Console.log**
   - Use proper logger

## When to Use Me

Use this skill before delivering any code or when you need
reminder of the rules:

- "Run the checklist"
- "Check against your directives"
- "Verify this code follows your rules"

## Quick Validation

Run these before delivery:

```bash
# Lint
lsp_diagnostics

# Tests
bun test

# Build
bun run build

# Search for common issues
rg "console.log" --glob "*.ts"
rg "subscribe" | rg -v "unsubscribe"
```