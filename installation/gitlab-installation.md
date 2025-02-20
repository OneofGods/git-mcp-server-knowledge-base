# GitLab MCP Server Installation Guide

This guide provides step-by-step instructions for installing and configuring the Git MCP server to work with GitLab.

## Prerequisites

- Node.js (LTS version recommended)
- npm or yarn package manager
- GitLab account with personal access token
- Basic understanding of MCP servers

## Installation Steps

### Step 1: Install the Server Package

```bash
# Option 1: Install globally (recommended for stability)
npm install -g @modelcontextprotocol/git-server

# Option 2: Run without installing
npx @modelcontextprotocol/git-server
```

> ⚠️ **Important**: Do NOT use `uvx` as it caused installation issues in previous attempts.

### Step 2: Create Directory Structure

Create the necessary directories for your MCP server configuration:

```bash
mkdir -p ~/.mcp/config
mkdir -p ~/.mcp/logs
mkdir -p ~/.mcp/adapters
```

### Step 3: Set Environment Variables

Create a `.env` file in your project directory:

```
# For GitLab - use the correct variable name!
GITLAB_PERSONAL_ACCESS_TOKEN=your_gitlab_token_here

# Optional configuration
MCP_SERVER_PORT=3000
MCP_LOG_LEVEL=info
MCP_CONFIG_PATH=~/.mcp/config/config.json
```

> ⚠️ **Critical**: The environment variable must be `GITLAB_PERSONAL_ACCESS_TOKEN`, not `GITLAB_ACCESS_TOKEN`. Using the wrong name is a common source of authentication errors.

### Step 4: Configure the GitLab Adapter

Create or download the GitLab adapter and save it to `~/.mcp/adapters/gitlab-adapter.js`. 

The adapter should handle:
- Parameter translation between GitHub and GitLab formats
- Proper authentication with the correct token variable
- Endpoint mapping
- Response transformation

You can find a sample adapter in the [adapters directory](../adapters/gitlab-adapter.js).

### Step 5: Create Configuration File

Create a configuration file at `~/.mcp/config/config.json`:

```json
{
  "server": {
    "port": 3000,
    "logLevel": "info",
    "logFile": "~/.mcp/logs/server.log"
  },
  "gitlab": {
    "useAdapter": true,
    "adapterPath": "~/.mcp/adapters/gitlab-adapter.js",
    "baseUrl": "https://gitlab.com/api/v4"
  },
  "process": {
    "cleanupOnExit": true,
    "timeoutMs": 30000,
    "lockFile": "~/.mcp/server.lock"
  }
}
```

### Step 6: Create Start-up Script

Create a startup script called `start-gitlab-mcp.sh`:

```bash
#!/bin/bash

# Check if Claude desktop is running
if pgrep -f "Claude" > /dev/null; then
  echo "Warning: Claude desktop is running, which may cause port conflicts"
  read -p "Continue anyway? (y/n) " choice
  if [[ $choice != "y" ]]; then
    exit 1
  fi
fi

# Set environment variables
export GITLAB_PERSONAL_ACCESS_TOKEN="your_token_here"
export MCP_SERVER_PORT=3000
export MCP_CONFIG_PATH="$HOME/.mcp/config/config.json"

# Run the server
npx @modelcontextprotocol/git-server
```

Make it executable:
```bash
chmod +x start-gitlab-mcp.sh
```

## Verifying Installation

To verify your installation is working correctly:

```bash
# Start the server
./start-gitlab-mcp.sh

# In another terminal, test the connection
curl http://localhost:3000/status
```

You should see a response indicating the server is running and connected to GitLab.

## Troubleshooting

If you encounter issues during installation, refer to the [GitLab MCP Server Troubleshooting Guide](../troubleshooting/gitlab-mcp-server.md).

Common installation issues include:
- Incorrect environment variable names
- Missing dependencies
- Port conflicts with Claude desktop or other applications
- Incorrect adapter configuration
- GitLab token permission issues
