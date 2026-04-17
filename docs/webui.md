# OpenCode Web UI Guide

## Overview

OpenCode offers multiple ways to access its interface via web browser, beyond the terminal. This guide covers all available options - from simple built-in server to advanced deployments.

> **Why use Web UI?**
> - Share access with team members
> - Work from multiple devices
> - Use in environments without terminal access
> - Different workflow preferences

---

## Table of Contents

1. [Built-in Web Server](#1-built-in-web-server) - Simplest option
2. [Desktop App](#2-desktop-app) - Native application
3. [Docker Deployment](#3-docker-deployment) - Containerized
4. [Advanced Web UI](#4-advanced-web-ui) - Enhanced features
5. [Remote Access](#5-remote-access) - Network/remote usage
6. [Security](#6-security) - Protecting your instance

---

## 1. Built-in Web Server

The simplest way to access OpenCode via browser - no additional installation required.

### Quick Start

```bash
# Simple - opens browser automatically
opencode web

# With custom port
opencode web --port 4096

# Allow network access (for other devices on your network)
opencode web --hostname 0.0.0.0 --port 4096

# Custom port with network access
opencode web --port 3000 --hostname 0.0.0.0
```

### What It Does

| Feature | Description |
| :--- | :--- |
| **Server** | Starts local HTTP server |
| **Address** | `127.0.0.1` by default (localhost only) |
| **Browser** | Opens your default browser automatically |
| **Functionality** | Same as terminal - full OpenCode capabilities |

### When to Use

- **Local-only**: Keep default `127.0.0.1` - no network exposure
- **Team/Network**: Use `0.0.0.0` - allows other devices on your network

### Configuration Flags

| Flag | Description | Example |
| :--- | :--- | :--- |
| `--port` | Port to listen on | `4096`, `3000` |
| `--hostname` | Host to bind | `127.0.0.1` (local), `0.0.0.0` (network) |
| `--mdns` | Enable mDNS discovery | Bonjour/Avahi auto-discovery |
| `--cors` | Additional CORS origins | `https://example.com` |

### Security: Environment Variables

```bash
# Set password for security (REQUIRED for network exposure)
export OPENCODE_SERVER_PASSWORD=your_secure_password

# Optional: Set username
export OPENCODE_SERVER_USERNAME=opencode

# Then start server
opencode web
```

---

---

## 2. Desktop App

OpenCode has an official desktop application - a native app that embeds the web UI in a standalone application.

### Download

Get from: https://opencode.ai/download

| Platform | Format | Notes |
| :--- | :--- | :--- |
| **macOS** | `.dmg`, `.app.tar.gz` | Apple Silicon (M1/M2/M3) and Intel |
| **Linux** | `.deb`, `.rpm`, `.AppImage` | Most distributions |
| **Windows** | `.exe` NSIS installer | Windows 10+ |

### Features

| Feature | Description |
| :--- | :--- |
| **Native experience** | Looks and feels like a desktop app |
| **Full capabilities** | Same core OpenCode as CLI |
| **Multi-session** | Work on multiple projects simultaneously |
| **Offline mode** | Works without internet (web UI embedded) |
| **System tray** | Runs in background |

### Why Desktop App?

| Benefit | Use Case |
| :--- | :--- |
| **No terminal needed** | Prefer GUI over CLI |
| **Easier sharing** | Simple to explain to non-technical teammates |
| **Offline work** | Work without internet |
| **Multi-window** | Multiple sessions side by side |

### Installation

```bash
# Linux (Ubuntu/Debian)
sudo dpkg -i opencode_*.deb

# Linux (Fedora/RHEL)
sudo rpm -i opencode-*.rpm

# macOS
# Double-click .dmg, drag to Applications

# Windows
# Run the .exe installer
```

---

## 2.1 Open WebUI (Provider Integration)

Open WebUI is a community project that lets you use OpenCode as an **LLM provider** for other applications.

### What It Does

- Use OpenCode as the engine behind Open WebUI (ChatGPT-style interface)
- Connect OpenCode to other apps that support OpenAI-compatible APIs
- More flexible integration options

### When to Use

- You prefer the ChatGPT-style conversation interface
- Want to use multiple LLM providers alongside OpenCode
- Need features Open WebUI provides (history, export, etc.)

### Setup

See GitHub PR for details: https://github.com/anomalyco/opencode/pull/18306

```bash
# Install Open WebUI
pip install open-webui

# Configure OpenCode as provider
# In Open WebUI settings:
# 1. Go to Settings → Model Connections
# 2. Add "OpenCode" as provider
# 3. Configure the endpoint (default: http://localhost:4096)
```

---

## 3. Docker Deployment

Containerized OpenCode with web UI - perfect for server deployment or consistent environments.

### Why Docker?

| Benefit | Use Case |
| :--- | :--- |
| **Consistency** | Same environment everywhere |
| **Isolation** | No conflicts with system dependencies |
| **Server deployment** | Run on remote server |
| **Team sharing** | Same setup for all developers |

### Basic Usage

```bash
# Simple run (local only)
docker run -d -p 4096:4096 ghcr.io/brockar/opencoded:latest web

# With workspace mount (your code accessible in container)
docker run -d \
  -p 4096:4096 \
  -v $(pwd):/workspace \
  ghcr.io/brockar/opencoded:latest web --hostname 0.0.0.0

# With password (REQUIRED for network exposure)
docker run -d \
  -p 4096:4096 \
  -e OPENCODE_SERVER_PASSWORD=secret \
  ghcr.io/brockar/opencoded:latest web

# Full example with everything
docker run -d \
  -p 4096:4096 \
  -v /path/to/projects:/workspace \
  -e OPENCODE_SERVER_PASSWORD=your_password \
  -e OPENCODE_SERVER_USERNAME=admin \
  ghcr.io/brockar/opencoded:latest web \
  --hostname 0.0.0.0
```

### Docker Compose (Recommended)

For easier management and persistence:

```yaml
version: '3.8'
services:
  opencode:
    image: ghcr.io/brockar/opencoded:latest
    ports:
      - "4096:4096"
    volumes:
      - .:/workspace                    # Current directory as workspace
      - opencode-data:/home/user/.opencode  # Persistent config
    environment:
      - OPENCODE_SERVER_PASSWORD=secret    # REQUIRED for network
      - OPENCODE_SERVER_USERNAME=admin
    command: ["web", "--hostname", "0.0.0.0"]
    restart: unless-stopped

volumes:
  opencode-data:
```

**Start with**: `docker compose up -d`

### Common Docker Options

| Option | Description |
| :--- | :--- |
| `-d` | Detached (background) |
| `-p` | Port mapping (host:container) |
| `-v` | Volume mount (share files) |
| `-e` | Environment variable |
| `--hostname 0.0.0.0` | Allow network access |

---

---

## 4. Advanced Web UI

For environments with reverse proxies, URL prefixes, or advanced features.

### pk-opencode-webui

A prefix-aware Web UI for environments with reverse proxies (nginx, Caddy, Cloudflare Tunnel, etc.)

**Repository**: https://github.com/prokube/pk-opencode-webui

| Feature | Description |
| :--- | :--- |
| **Reverse proxy support** | Works behind nginx, Caddy, etc. |
| **URL prefix handling** | Serve at `/apps/opencode/` instead of root |
| **Multi-project workspace** | Switch between projects easily |
| **Command palette** | VS Code-style quick actions |
| **MCP server management** | Configure Model Context Protocol servers |

### When to Use Advanced UI

- Your server uses a reverse proxy
- You need URL path prefixes
- Want command palette functionality
- Need multi-project workspace management

### Setup

```bash
# Option 1: Standalone (requires OpenCode server running)
# Start OpenCode server
cd /your/project
opencode serve

# Start advanced Web UI
cd app-prefixable
bun install && bun run dev

# Option 2: Docker (simpler)
docker run -p 8080:8080 \
  -e API_URL=http://host.docker.internal:4096 \
  -e BASE_PATH=/apps/opencode/ \
  opencode-web
```

### Reverse Proxy Example (nginx)

```nginx
location /opencode/ {
    proxy_pass http://localhost:8080/;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
}
```

---

---

## 5. Remote Access

Access your OpenCode instance from anywhere - other devices, remote locations, or over the internet.

### Option 1: Local Network Access

For access within your home/office network:

```bash
# Start server on all network interfaces
opencode web --hostname 0.0.0.0 --port 4096
```

**Access from other devices**:
```
http://192.168.x.x:4096
```
Replace `192.168.x.x` with your computer's IP address (find with `ip addr` or `ifconfig`).

### Option 2: Cloudflare Tunnel (Recommended for Remote)

For secure remote access without opening firewall ports:

```bash
# Install cloudflared
# macOS
brew install cloudflared
# Linux
sudo apt install cloudflared

# Create a tunnel
cloudflared tunnel --name opencode
cloudflared tunnel route ip --tunnel opencode -- lb-ip 0.0.0.0

# Point to your OpenCode instance
cloudflared tunnel --hostname opencode.yourdomain.com http://localhost:4096
```

**Benefits**:
- No firewall changes needed
- HTTPS automatically
- Access from anywhere via domain name

### Option 3: Tailscale

For secure mesh VPN access:

```bash
# Install Tailscale (https://tailscale.com)
brew install tailscale  # macOS
# or
sudo apt install tailscale  # Linux

# Start OpenCode locally
opencode web --port 4096

# Access via your tailnet URL
https://your-machine.tailnet-name.ts.net:4096
```

**Benefits**:
- Encrypted peer-to-peer connection
- No port forwarding needed
- Works across devices

---

## 6. Security

Protecting your OpenCode instance from unauthorized access.

### Critical: Always Use Password

**NEVER expose OpenCode to the internet without a password.**

```bash
# Via environment variable (recommended)
export OPENCODE_SERVER_PASSWORD=your_secure_password
opencode web

# Via config file (~/.config/opencode/opencode.json)
{
  "server": {
    "password": "your_secure_password",
    "username": "admin"
  }
}
```

### Security Best Practices

| Practice | Why | How |
| :--- | :--- | :--- |
| **Always use password** | Prevents unauthorized access | Set `OPENCODE_SERVER_PASSWORD` |
| **Use VPN/tunnel** | Encrypts all traffic | Cloudflare Tunnel or Tailscale |
| **Firewall** | Blocks unauthorized network access | `sudo ufw deny 4096/tcp` (unless needed) |
| **Keep updated** | Security fixes | `opencode --version` check regularly |
| **Limit exposure** | Minimize attack surface | Use `127.0.0.1` by default |

### Firewall Commands

```bash
# Linux (UFW) - block port by default
sudo ufw deny 4096/tcp

# Allow specific IP only
sudo ufw allow from 192.168.1.100 to any port 4096

# macOS - block incoming
sudo pfctl -f /etc/pf.conf
# Add: block in on en0 proto tcp from any to any port 4096
```

### Network Exposure Levels

| Level | Command | Safe Without Password? |
| :--- | :--- | :--- |
| **Local only** | `--hostname 127.0.0.1` (default) | ✅ Yes |
| **Local network** | `--hostname 0.0.0.0` | ❌ NO |
| **Internet** | Any exposure | ❌ NO |

---

---

## Troubleshooting

### Can't Access Web UI

1. **Check if server is running**:
   ```bash
   lsof -i :4096
   # or
   netstat -tuln | grep 4096
   ```

2. **Try different port** (something else might be using it):
   ```bash
   opencode web --port 3000
   ```

3. **Check firewall**:
   ```bash
   # Linux
   sudo ufw allow 4096/tcp

   # macOS
   sudo pfctl -f /etc/pf.conf
   ```

4. **Check logs** (look for error messages):
   ```bash
   opencode web 2>&1 | head -50
   ```

### Session Not Syncing Between CLI and Web

Use `opencode attach` to connect TUI to web:

```bash
# Terminal 1: Start web server
opencode web --port 4096

# Terminal 2: Connect TUI to web session
opencode attach http://localhost:4096
```

### Docker Issues

**Port already in use**:
```bash
# Find and stop existing container
docker ps
docker stop <container_id>
```

**Permission denied**:
```bash
# Run with proper permissions
docker run -d -p 4096:4096 \
  -v $(pwd):/workspace \
  -u $(id -u):$(id -g) \
  ghcr.io/brockar/opencoded:latest web
```

### Can't Connect to Docker Server

```bash
# Start Docker daemon
sudo systemctl start docker

# Add user to docker group
sudo usermod -aG docker $USER
# Log out and back in
```

---

## Quick Reference

| Need | Solution |
| :--- | :--- |
| Quick start | `opencode web` |
| Custom port | `opencode web --port 4096` |
| Network access | `opencode web --hostname 0.0.0.0` |
| With password | `OPENCODE_SERVER_PASSWORD=secret opencode web` |
| Desktop app | Download from opencode.ai/download |
| Docker | `docker run -p 4096:4096 ghcr.io/brockar/opencoded:latest web` |
| Docker Compose | `docker compose up -d` |
| Remote via tunnel | Cloudflare Tunnel |
| Remote via VPN | Tailscale |

---

## Related

- [README](../README.md) - Setup guide
- [Setup Guide](../setup.md) - Comprehensive setup
- [Agents](../agents.md) - Agent configuration

---

## References

### Official
- [OpenCode Web Documentation](https://dev.opencode.ai/docs/web/)
- [OpenCode Download](https://opencode.ai/download) - Desktop apps
- [OpenCode GitHub](https://github.com/anomalyco/opencode)

### Community
- [Docker Image](https://github.com/brockar/opencoded) - Community Docker builds
- [pk-opencode-webui](https://github.com/prokube/pk-opencode-webui) - Advanced Web UI
- [Open WebUI Provider PR](https://github.com/anomalyco/opencode/pull/18306) - OpenCode as LLM provider

### Remote Access
- [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Tailscale](https://tailscale.com/)

---

*Last updated: April 2026*