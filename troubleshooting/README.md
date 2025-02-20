# Git MCP Server Troubleshooting

This directory contains comprehensive troubleshooting guides for common issues encountered when working with Git MCP servers.

## Troubleshooting Guides

- [GitLab MCP Server Troubleshooting](gitlab-mcp-server.md)
- [GitHub MCP Server Troubleshooting](github-mcp-server.md)
- [Generic Git Server Issues](generic-git-issues.md)

## Common Issues by Category

### Authentication Issues
- [Environment Variable Problems](environment-variables.md)
- [Token Permission Issues](token-permissions.md)
- [API Authentication Errors](api-authentication.md)

### Configuration Issues
- [Path Configuration Problems](path-configuration.md)
- [Port Conflicts](port-conflicts.md)
- [Config File Format Issues](config-file-format.md)

### Process Management Issues
- [Lock File Problems](lock-file-management.md)
- [Process Conflicts](process-conflicts.md)
- [Claude Desktop Conflicts](claude-desktop-conflicts.md)

### API Integration Issues
- [API Parameter Mapping](api-parameter-mapping.md)
- [Response Transformation Issues](response-transformation.md)
- [Endpoint Mapping Problems](endpoint-mapping.md)

## Diagnostic Tools

This repository includes several diagnostic tools to help identify and resolve issues:

- [GitLab MCP Troubleshooting Script](../scripts/gitlab-mcp-troubleshoot.sh)
- [Environment Validator](../scripts/validate-environment.js)
- [Configuration Validator](../scripts/validate-config.js)
- [Connection Tester](../scripts/test-connection.js)

## Common Error Patterns

Understanding error patterns can help quickly identify issues:

| Error Message | Likely Cause | Resolution Guide |
|---------------|--------------|------------------|
| `Error: GITLAB_PERSONAL_ACCESS_TOKEN not set` | Missing environment variable | [Environment Variables](environment-variables.md) |
| `Error: EADDRINUSE` | Port conflict | [Port Conflicts](port-conflicts.md) |
| `Error: 401 Unauthorized` | Token issues | [API Authentication](api-authentication.md) |
| `Error: Cannot find module` | Installation issues | [Installation Guide](../installation/README.md) |
| `Error: Invalid parameter format` | API mismatch | [API Parameter Mapping](api-parameter-mapping.md) |
| `Error: Could not acquire lock` | Process conflict | [Lock File Management](lock-file-management.md) |

## Contributing

If you've encountered and solved an issue not documented here, please contribute by adding a new troubleshooting guide following the [template](template.md).
