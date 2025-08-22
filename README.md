# Claude Code Development Container

A secure, production-ready development container designed for running Claude Code with enhanced safety features and network isolation. This container provides a standardized development environment that can be safely used with Claude Code's dangerous mode while protecting your host system. Big parts and firewall.sh were inspired by [anthropics/claude-cod](https://github.com/anthropics/claude-code/tree/main).

## 🔒 Security Features

- **Network Isolation**: Custom firewall with precise access control
- **Sandboxed Environment**: Complete isolation from host system
- **Default-Deny Policy**: Blocks all unauthorized network access
- **Whitelisted Domains**: Only allows connections to essential services (npm, GitHub, Anthropic API)
- **Container Isolation**: Docker provides additional security boundaries

## 🚀 What's Included

- **Base**: Node.js 20 (slim variant)
- **Shell**: ZSH with Starship prompt and useful plugins
- **Tools**: Git, GitHub CLI, FZF, Delta, Nano, Vim, JQ, and more
- **Development**: Essential development dependencies and SSL certificates
- **Claude Code**: Pre-installed latest version
- **Network Tools**: iptables, ipset, iproute2 for firewall management

## 📋 Prerequisites

1. **Docker**: Install Docker Desktop or Docker Engine
2. **VS Code**: Install Visual Studio Code
3. **Extension**: Install the "Dev Containers" extension (`ms-vscode-remote.remote-containers`)

## 🛠️ Usage

### Option 1: Using the Pre-built Image

Create a `.devcontainer/devcontainer.json` in your project:

```json
{
  "name": "Claude Code Dev Container",
  "image": "your-registry/claude-dev-container:latest",
  "remoteUser": "node",
  "features": {},
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-json"
      ]
    }
  },
  "mounts": [
    "source=${localWorkspaceFolder},target=/workspace,type=bind,consistency=cached"
  ],
  "postCreateCommand": "sudo /usr/local/bin/init-firewall.sh"
}
```

### Option 2: Using the Dockerfile

Create a `.devcontainer/devcontainer.json` in your project:

```json
{
  "name": "Claude Code Dev Container",
  "build": {
    "dockerfile": "path/to/Dockerfile",
    "args": {
      "CLAUDE_CODE_VERSION": "latest",
      "TZ": "UTC"
    }
  },
  "remoteUser": "node",
  "features": {},
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-vscode.vscode-json"
      ]
    }
  },
  "mounts": [
    "source=${localWorkspaceFolder},target=/workspace,type=bind,consistency=cached"
  ],
  "postCreateCommand": "sudo /usr/local/bin/init-firewall.sh"
}
```

### Getting Started

1. **Open in VS Code**: Open your project folder in VS Code
2. **Command Palette**: Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on macOS)
3. **Reopen in Container**: Select "Dev Containers: Reopen in Container"
4. **Wait for Build**: The container will build and start automatically
5. **Start Claude**: Open a terminal and run `claude`

## 🔐 Claude Code Safety

### Safe Usage with Dangerous Mode

This container is specifically designed to safely use Claude Code's `--dangerously-skip-permissions` flag:

```bash
claude --dangerously-skip-permissions
```

**Why it's safer in this container:**
- Docker isolation prevents access to host system files
- Network firewall blocks unauthorized external connections
- Container can be easily destroyed and recreated
- No access to sensitive host system resources

### ⚠️ Security Considerations

**What this container protects against:**
- Accidental file system damage to host
- Unauthorized network connections
- System-level modifications
- Installation of malicious packages on host

**What this container does NOT protect against:**
- Credential theft (Claude Code credentials are accessible)
- Data exfiltration of files within the container
- Malicious code execution within the container scope

**Best Practices:**
- Only use with trusted repositories
- Regularly update the base image
- Monitor Claude Code's actions even in safe mode
- Keep sensitive credentials outside the container when possible

## 🔧 Customization

### Environment Variables

- `CLAUDE_CODE_VERSION`: Specify Claude Code version (default: `latest`)
- `TZ`: Set timezone (default: system timezone)
- `GIT_DELTA_VERSION`: Git delta version for enhanced diffs (default: `0.18.2`)

### Building with Custom Settings

```bash
docker build \
  --build-arg CLAUDE_CODE_VERSION=1.2.3 \
  --build-arg TZ=America/New_York \
  -t my-claude-container .
```

### Adding Your Own Tools

Extend the Dockerfile to add project-specific dependencies:

```dockerfile
FROM your-base-claude-container:latest

# Add your custom tools
RUN apt-get update && apt-get install -y \
  python3 \
  python3-pip \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install additional npm packages
RUN npm install -g typescript eslint
```

## 📁 File Structure

The container creates the following structure:

```
/workspace          # Your project files (mounted)
/home/node/.claude  # Claude Code configuration
/commandhistory     # Persistent bash history
/usr/local/bin      # Custom scripts and tools
```

## 🌐 Network Access

The container's firewall allows access to:

- **npm registry** (registry.npmjs.org)
- **GitHub** (github.com, api.github.com)
- **Anthropic API** (api.anthropic.com)
- **Essential services** (DNS, NTP)

All other external connections are blocked by default.

## 🐛 Troubleshooting

### Container Won't Start
- Ensure Docker is running
- Check VS Code Dev Containers extension is installed
- Verify devcontainer.json syntax

### Network Issues
- Firewall script may need adjustment for your use case
- Check if required services are in the whitelist
- Verify DNS resolution works

### Claude Code Issues
- Ensure you're using the correct version
- Check if authentication tokens are properly configured
- Verify network connectivity to Anthropic API

### Permission Issues
- Ensure the `node` user has proper permissions
- Check file ownership in mounted volumes
- Verify sudo configuration for firewall script

## 📖 Additional Resources

- [VS Code Dev Containers Documentation](https://code.visualstudio.com/docs/devcontainers/containers)
- [Development Containers Specification](https://containers.dev/)
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Claude Code Security Best Practices](https://docs.anthropic.com/en/docs/claude-code/security)

## 📄 License

This project is provided as-is for educational and development purposes. Please review and understand the security implications before using in production environments.

---

**⚠️ Important**: While this container provides substantial security improvements, no system is completely immune to all attacks. Always exercise caution when using dangerous mode and only work with trusted repositories.