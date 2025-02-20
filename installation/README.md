# Git MCP Server Installation Guides

This directory contains comprehensive installation guides for Git MCP servers with different providers.

## Available Installation Guides

- [GitLab MCP Server Installation](gitlab-installation.md)
- [GitHub MCP Server Installation](github-installation.md)
- [Generic Git MCP Server Installation](generic-git-installation.md)

## Installation Methods

### Global Installation (Recommended)

For most stable operation, install the server globally:

```bash
npm install -g @modelcontextprotocol/git-server
```

### NPX Usage

For temporary or testing purposes:

```bash
npx @modelcontextprotocol/git-server
```

### Common Installation Issues

1. **Wrong Package Manager**
   - ❌ `uvx @modelcontextprotocol/git-server` (don't use)
   - ✅ `npx @modelcontextprotocol/git-server` (correct)

2. **Missing Dependencies**
   - Ensure you have all required dependencies: `npm install axios fs-extra winston`

3. **Installation Location**
   - Global installations need admin privileges on some systems
   - Local installations need to specify the path in your config
