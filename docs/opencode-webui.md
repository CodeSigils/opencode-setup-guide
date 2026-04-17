# OpenCode Web UI Guide

## Overview

OpenCode offers multiple ways to access its interface via web browser, beyond the terminal. This guide covers all available options.

---

## Table of Contents

1. [Built-in Web Server](#1-built-in-web-server)
2. [Desktop App](#2-desktop-app)
3. [Docker Deployment](#3-docker-deployment)
4. [Advanced Web UI](#4-advanced-web-ui)
5. [Remote Access](#5-remote-access)
6. [Security](#6-security)

---

## 1. Built-in Web Server

The simplest way to access OpenCode via browser.

### Quick Start

```bash
# Simple - opens browser automatically
opencode web

# With custom port
opencode web --port 4096

# Allow network access (for other devices)
opencode web --hostname 0.0.0.0 --port 4096
```

### What It Does

- Starts local HTTP server on `127.0.0.1`
- Opens your default browser automatically
- Provides same functionality as terminal

### Configuration

| Flag | Description |
| :--- | :--- |
| `--port` | Port to listen on |
| `--hostname` | Host to bind (use `0.0.0.0` for network) |
| `--mdns` | Enable mDNS discovery |
| `--cors` | Additional CORS origins |

### Environment Variables

```bash
# Set password for security
export OPENCODE_SERVER_PASSWORD=yourpassword
export OPENCODE_SERVER_USERNAME=opencode
```

---

## 2. Desktop App

OpenCode has an official desktop application built with SolidJS.

### Download

Get from: https://opencode.ai/download

| Platform | Format |
| :--- | :--- |
| macOS | `.dmg`, `.app.tar.gz` |
| Linux | `.deb`, `.rpm`, `.AppImage` |
| Windows | `.exe` NSIS installer |

### Features

- Native desktop experience
- Same core OpenCode capabilities
- Multi-session support
- Works offline (web UI embedded in binary)

---

## 3. Docker Deployment

Containerized OpenCode with web UI.

### Basic Usage

```bash
# Simple run
docker run -d -p 4096:4096 ghcr.io/brockar/opencoded:latest web

# With workspace mount
docker run -d \
  -p 4096:4096 \
  -v $(pwd):/workspace \
  ghcr.io/brockar/opencoded:latest web --hostname 0.0.0.0

# With password
docker run -d \
  -p 4096:4096 \
  -e OPENCODE_SERVER_PASSWORD=secret \
  ghcr.io/brockar/opencoded:latest web
```

### Docker Compose

```yaml
version: '3.8'
services:
  opencode:
    image: ghcr.io/brockar/opencoded:latest
    ports:
      - "4096:4096"
    volumes:
      - .:/workspace
    environment:
      - OPENCODE_SERVER_PASSWORD=secret
    command: ["web", "--hostname", "0.0.0.0"]
```

---

## 4. Advanced Web UI

### pk-opencode-webui

A prefix-aware Web UI for environments with reverse proxies.

**Repository**: https://github.com/prokube/pk-opencode-webui

**Features**:
- Full reverse proxy support
- Multi-project workspace
- URL prefix handling
- Command palette (VS Code-style)
- MCP server management

### Setup

```bash
# Start standard OpenCode server
cd /your/project
opencode serve

# Start advanced Web UI
cd app-prefixable
bun install && bun run dev
```

Or via Docker:

```bash
docker run -p 8080:8080 \
  -e API_URL=http://host.docker.internal:4096 \
  -e BASE_PATH=/apps/opencode/ \
  opencode-web
```

---

## 5. Remote Access

### Local Network Access

Run on your network:

```bash
opencode web --hostname 0.0.0.0 --port 4096
```

Access from other devices:
```
http://192.168.x.x:4096
```

### Cloudflare Tunnel (Recommended for Remote)

```bash
# Install cloudflared
brew install cloudflared  # macOS
# or
sudo apt install cloudflared  # Linux

# Create tunnel
cloudflared tunnel --name opencode
cloudflared tunnel route ip --tunnel opencode -- lb-ip 0.0.0.0

# Point to OpenCode
cloudflared tunnel --hostname opencode.yourdomain.com http://localhost:4096
```

### Tailscale

```bash
# Install tailscale
brew install tailscale

# Start OpenCode
opencode web --port 4096

# Access via tailnet URL
https://your-machine.tailnet-name.ts.net:4096
```

---

## 6. Security

### Setting Password

```bash
# Via environment
export OPENCODE_SERVER_PASSWORD=your_secure_password
opencode web

# Or in config
{
  "server": {
    "password": "your_secure_password"
  }
}
```

### Best Practices

1. **Always use password** when exposing to network
2. **Use VPN or tunnel** for remote access
3. **Firewall** block external access to port unless needed
4. **Keep CLI updated** for latest security fixes

---

## Troubleshooting

### Can't Access Web UI

1. Check if server is running:
   ```bash
   lsof -i :4096
   ```

2. Try different port:
   ```bash
   opencode web --port 3000
   ```

3. Check firewall:
   ```bash
   sudo ufw allow 4096/tcp
   ```

### Session Not Syncing

Use `opencode attach` to connect TUI to web:

```bash
# Start web server
opencode web --port 4096

# In another terminal
opencode attach http://localhost:4096
```

---

## Quick Reference

| Need | Solution |
| :--- | :--- |
| Just try it | `opencode web` |
| Custom port | `opencode web --port 4096` |
| Network access | `opencode web --hostname 0.0.0.0` |
| With password | `OPENCODE_SERVER_PASSWORD=secret opencode web` |
| Desktop app | Download from opencode.ai/download |
| Docker | `docker run -p 4096:4096 ghcr.io/brockar/opencoded:latest web` |

---

## References

- Official Docs: https://dev.opencode.ai/docs/web/
- Desktop App: https://opencode.ai/download
- Docker Image: https://github.com/brockar/opencoded
- Advanced Web UI: https://github.com/prokube/pk-opencode-webui

---

Last updated: April 2026