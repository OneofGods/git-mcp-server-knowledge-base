# GitLab MCP Server Troubleshooting Guide

This comprehensive guide addresses common issues encountered with GitLab MCP Server implementation and provides detailed solutions.

## Authentication Issues

### Wrong Environment Variable Name

**Issue**: Server fails to authenticate with GitLab
**Symptoms**:
- 401 Unauthorized errors
- "Token not found" messages
- "Authentication failed" in logs

**Root Cause**:
GitLab MCP Server specifically requires `GITLAB_PERSONAL_ACCESS_TOKEN`, but many implementations incorrectly use `GITLAB_ACCESS_TOKEN`.

**Solution**:
1. Check your environment variables:
   ```bash
   echo $GITLAB_PERSONAL_ACCESS_TOKEN
   echo $GITLAB_ACCESS_TOKEN
   ```

2. If using the wrong variable, update your environment:
   ```bash
   # Unset incorrect variable
   unset GITLAB_ACCESS_TOKEN
   
   # Set correct variable
   export GITLAB_PERSONAL_ACCESS_TOKEN="your_token_here"
   ```

3. Update any .env files or startup scripts to use the correct variable name

### Token Permission Issues

**Issue**: Server can authenticate but specific operations fail
**Symptoms**:
- 403 Forbidden errors
- "Insufficient permissions" messages
- Some operations work while others fail

**Root Cause**:
GitLab token doesn't have the required scopes/permissions.

**Solution**:
1. Verify your token has these permissions:
   - `api` (for general API access)
   - `read_repository` (for read operations)
   - `write_repository` (for write operations)

2. Create a new token with proper permissions:
   - Go to GitLab > User Settings > Access Tokens
   - Select the appropriate scopes
   - Generate a new token

## Installation Issues

### Package Installation Method

**Issue**: Server fails to install or run correctly
**Symptoms**:
- Module not found errors
- Command not found errors
- Unexpected behavior after installation

**Root Cause**:
Using incorrect package manager (`uvx` instead of `npx`) or installation method.

**Solution**:
1. Uninstall any incorrect installations:
   ```bash
   npm uninstall -g @modelcontextprotocol/git-server
   ```

2. Install using the correct method:
   ```bash
   # Preferred method (global installation)
   npm install -g @modelcontextprotocol/git-server
   
   # Alternative method (temporary)
   npx @modelcontextprotocol/git-server
   ```

3. Verify the installation:
   ```bash
   npm list -g @modelcontextprotocol/git-server
   ```

## Configuration Issues

### API Parameter Format Mismatches

**Issue**: API calls fail with format errors
**Symptoms**:
- "Invalid parameter format" errors
- "Missing required parameter" messages
- "Unexpected parameter" errors

**Root Cause**:
GitLab API requires different parameter formats than GitHub API, but the MCP server uses GitHub-style parameters.

**Solution**:
1. Implement a custom adapter to translate parameters:
   ```javascript
   // Example parameter translation
   transformParams(params, operation) {
     const transformed = {...params};
     
     if (operation === 'create_repository') {
       // GitLab uses 'visibility' instead of 'private'
       transformed.visibility = params.private ? 'private' : 'public';
       delete transformed.private;
     }
     
     return transformed;
   }
   ```

2. Configure the server to use your adapter:
   ```json
   {
     "gitlab": {
       "useAdapter": true,
       "adapterPath": "/path/to/your/gitlab-adapter.js"
     }
   }
   ```

## Process Management Issues

### Log File Access Conflicts

**Issue**: Server fails to start due to log file issues
**Symptoms**:
- "Cannot access log file" errors
- "Permission denied" for log files
- Server crashes when trying to write logs

**Root Cause**:
Multiple processes trying to access the same log file, or incorrect permissions.

**Solution**:
1. Use unique log file paths for each instance:
   ```json
   {
     "server": {
       "logFile": "~/.mcp/logs/server-${process.pid}.log"
     }
   }
   ```

2. Implement proper file locking:
   ```javascript
   const fs = require('fs');
   
   function acquireLogLock(logPath) {
     try {
       const lockFile = `${logPath}.lock`;
       // Create lock file with exclusive flag
       fs.writeFileSync(lockFile, process.pid.toString(), { flag: 'wx' });
       return true;
     } catch (err) {
       return false;
     }
   }
   ```

### Port Conflicts

**Issue**: Server fails to start due to port conflicts
**Symptoms**:
- "EADDRINUSE" errors
- "Port already in use" messages
- Server immediately exits after starting

**Root Cause**:
Claude desktop or other applications are using the same port.

**Solution**:
1. Check what's using the port:
   ```bash
   lsof -i:3000
   ```

2. Use a different port in your config:
   ```json
   {
     "server": {
       "port": 3001
     }
   }
   ```

3. Implement dynamic port allocation:
   ```javascript
   function findAvailablePort(startPort) {
     const net = require('net');
     return new Promise((resolve, reject) => {
       const server = net.createServer();
       server.listen(startPort, () => {
         const port = server.address().port;
         server.close(() => resolve(port));
       });
       server.on('error', () => {
         resolve(findAvailablePort(startPort + 1));
       });
     });
   }
   ```

## Diagnostic Tools

Use our [GitLab MCP Troubleshooting Script](../scripts/gitlab-mcp-troubleshoot.sh) to automatically check for common issues:

```bash
# Run the troubleshooting script
chmod +x ./scripts/gitlab-mcp-troubleshoot.sh
./scripts/gitlab-mcp-troubleshoot.sh
```

The script checks for:
- Environment variables
- Port conflicts
- Stale lock files
- Installation issues
- Configuration problems
