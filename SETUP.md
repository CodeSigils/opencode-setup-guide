# OpenCode Setup Guide - Installation

This guide walks you through setting up this OpenCode configuration.

## Prerequisites

- OpenCode installed
- Node.js 18+ 
- ripgrep (optional, for better search)

## Installation Steps

### Step 1: Clone This Repository

```bash
git clone https://github.com/YOUR_USERNAME/opencode-setup-guide.git ~/opencode-setup
cd ~/opencode-setup
```

### Step 2: Copy Configuration

```bash
# Copy main config
cp ~/opencode-setup/.opencode/opencode.json ~/.config/opencode/

# Copy agent config (or merge with existing)
cp ~/opencode-setup/.opencode/oh-my-openagent.json ~/.config/opencode/
```

### Step 3: Install Skills

```bash
# Copy community skills (direct directories, not symlinks)
git clone https://github.com/chandima/opencode-config.git /tmp/opencode-config
cp -r /tmp/opencode-config/skills/* ~/.config/opencode/skills/

# Copy your custom skills
cp -r ~/opencode-setup/skills/* ~/.config/opencode/skills/
```

### Step 4: Copy Directives

```bash
cp ~/opencode-setup/.opencode/directives.md ~/.config/opencode/
```

### Step 5: Restart OpenCode

Restart your terminal or OpenCode session for changes to take effect.

## Verification

Run this to verify your setup:

```bash
opencode run "What directives are loaded?"
```

You should see your 17 directives loaded.

## Customization

### Change Models

Edit `~/.config/opencode/oh-my-openagent.json` to change models:

```json
{
  "agents": {
    "sisyphus": { "model": "opencode/your-model" }
  }
}
```

### Add More Directives

Edit `~/.config/opencode/directives.md` to add your own rules.

### Add Skills

Add more skills to `~/.config/opencode/skills/`:

```bash
# Clone and copy skills (direct directories, not symlinks)
git clone https://github.com/other/skills.git /tmp/other-skills
cp -r /tmp/other-skills/* ~/.config/opencode/skills/
```

## What's Included

| Component | Location | Purpose |
|-----------|----------|---------|
| Plugins | opencode.json | opencode-mem, oh-my-openagent |
| Agents | oh-my-openagent.json | Model configs |
| Directives | directives.md | 17 persistent rules |
| Skills | skills/ | Custom + community |

## Troubleshooting

### Directives Not Loading

Check the path in oh-my-openagent.json:
```json
"prompt_append": "file://~/.config/opencode/directives.md"
```

Ensure the file exists:
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

## Next Steps

1. Read `docs/setup.md` for complete setup guide
2. Read `docs/skills.md` for skills reference
3. Read `docs/best-practices.md` to avoid AI coding mistakes
4. Customize the directives for your needs

---

For updates:
```bash
cd ~/opencode-setup
git pull origin main
```