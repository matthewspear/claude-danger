# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Container Management

This repository provides a containerized development environment for running Claude Code with network restrictions for safety.

### Core Commands
- `./start` - Start the development container
- `./stop` - Stop the container (preserving state)  
- `./shell` - Open a shell in the running container (auto-starts if needed)
- `./destroy` - Completely destroy the container and volumes (with confirmation)

### Container Architecture

The development environment runs in a Docker container with:
- Node.js 20 runtime with pnpm package manager
- Claude Code CLI pre-installed with alias `cc` (uses `--dangerously-skip-permissions`)
- Network firewall configured to restrict outbound connections to approved domains only
- Persistent volumes for workspace, Claude settings, and shell history
- Security restrictions: read-only filesystem, dropped capabilities, PID limits

### Network Security

The container implements a restrictive firewall via `init-firewall.sh` that:
- Blocks all outbound traffic by default
- Allows only specific domains needed for development (npm registry, Anthropic API, etc.)
- Uses ipset and iptables to enforce domain-based access control
- Must be initialized with `sudo /usr/local/bin/init-firewall.sh` inside the container

### Persistent Data

- `/workspace` - Main development workspace (mounted from ./workspace)
- `~/.claude` - Claude Code settings and configuration
- Command history preserved across container restarts

### Port Forwarding

Default ports exposed from container:
- 5173 - Vite dev server
- 3000 - Next.js dev server

### Container Lifecycle

The container automatically destroys itself if `.claude.json` is empty, ensuring clean state management between sessions.