---
name: markdown-lint
description: Lint markdown files for style issues using markdownlint
license: MIT
compatibility: opencode
---

# Markdown Lint

This skill lints markdown files for style and syntax issues using markdownlint.

## What I Do

I run markdownlint on the specified file to check for:

- Code block syntax errors
- Heading hierarchy issues
- Link issues (broken, empty)
- Table formatting issues
- GFM (GitHub Flavored Markdown) compliance

## Usage

Run on a specific file:

```bash
npx markdownlint README.md
```

Run on all markdown files:

```bash
npx markdownlint "**/*.md"
```

Run with custom config:

```bash
npx markdownlint --config .markdownlint.json README.md
```

## Installation

The skill uses markdownlint-cli which is typically already installed in the OpenCode
project. If not:

```bash
npm install -g markdownlint-cli
```

## Common Issues Fixed

1. **Code blocks**: Use proper syntax highlighting (e.g., "```json", not "```")
2. **Headings**: Don't skip levels (## then ###, not # then ###)
3. **Tables**: Must have | :--- | header row
4. **Links**: Don't have empty link text or URLs

## Configuration

Create `.markdownlint.json` in project root:

```json
{
  "MD013": false,
  "MD033": false,
  "MD041": false
}
```

Common rules to disable:

- MD013: Line length (disable for readability)
- MD033: Inline HTML (allow in rare cases)
- MD041: First line heading (optional)