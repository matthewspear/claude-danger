# Claude Danger

A containerized development environment for running Claude Code with network restrictions for enhanced security.

Allow you to run Claude Code dangerously without giving it access to your full machine.

## Usage

1. **Setup Docker** - Ensure you have [Docker](https://www.docker.com/get-started/) / [OrbStack](https://orbstack.dev/) installed and running
2. **Start the server** - Run `./start` to launch the development environment
3. **Shell to the server** - Run `./shell` to access the container and use `cc` to run Claude Code dangerously
4. **Initial setup** - Run `cc` for the first time to configure Claude Code and sign into your Anthropic account (your `.claude` config is linked between host and container)
5. **Work within workspace folder** - All development work should be done in the `/workspace` directory
6. **Stop to stop the server** - Run `./stop` when finished to preserve state

## Quick Start

```bash
# Start the development container
./start

# Open a shell in the container
./shell

# Stop the container (preserving state)
./stop

# Destroy container and all data (with confirmation)
./destroy
```

## Features

- **Secure Container**: Docker environment with restricted network access
- **Claude Code Ready**: Pre-installed CLI with `cc` alias
- **Network Firewall**: Blocks unauthorized outbound connections
- **Persistent Storage**: Workspace and settings preserved across restarts
- **Development Ports**: Pre-configured for common dev servers (3000, 5173)

## Container Details

### Runtime Environment
- Node.js 20 with pnpm package manager
- Claude Code CLI with `--dangerously-skip-permissions` flag
- Security hardening: read-only filesystem, dropped capabilities, PID limits

### Network Security
The container implements restrictive firewall rules via `init-firewall.sh`:
- Blocks all outbound traffic by default
- Allows only approved domains (npm registry, Anthropic API, etc.)
- Uses ipset and iptables for domain-based access control

To add additional allowed domains, modify the `ALLOWED_DOMAINS` environment variable in `docker-compose.yml`:
```yaml
environment:
  ALLOWED_DOMAINS: >-
    registry.npmjs.org,
    api.anthropic.com,
    sentry.io,
    statsig.anthropic.com,
    statsig.com,
    your-domain.com
```

### Persistent Volumes
- `/workspace` - Main development files (mounted from `./workspace`)
- `~/.claude` - Claude Code configuration and settings
- Shell history preserved across container restarts

## Port Forwarding

Default exposed ports:
- **5173** - Vite development server
- **3000** - Next.js development server

## Security Notes

This environment is designed for secure development with Claude Code while maintaining network restrictions to prevent unauthorized data access or transmission.

## Disclaimer

**Use at your own risk.** This project provides a containerized environment for running Claude Code with network restrictions, but no security system is perfect. Users are responsible for understanding the implications of running AI-powered code generation tools in their environment.
