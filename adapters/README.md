# Git MCP Server Adapters

This directory contains adapters for connecting Git MCP servers to different Git providers (GitHub, GitLab, etc.)

## What are MCP Adapters?

MCP adapters are modules that translate between the standard MCP server interface and specific Git provider APIs. They handle:

1. Parameter format translation
2. Authentication mechanisms
3. Endpoint mapping
4. Response transformation

## Available Adapters

- [GitLab Adapter](gitlab-adapter.js) - Adapter for GitLab API
- [GitHub Adapter](github-adapter.js) - Adapter for GitHub API (baseline)
- [Generic Git Adapter](generic-git-adapter.js) - Generic adapter for Git providers

## Adapter Implementation Pattern

All adapters follow this general structure:

```javascript
class GitProviderAdapter {
  constructor(config) {
    this.config = config;
    this.token = process.env.PROVIDER_TOKEN;
    this.baseUrl = config.baseUrl || 'https://provider-api-url';
    
    // Setup client/authentication
  }
  
  // Transform parameters from GitHub format to provider format
  transformParams(params, operation) {
    // Parameter mapping logic
  }
  
  // Get the appropriate endpoint for the operation
  getEndpoint(operation, params) {
    // Endpoint mapping logic
  }
  
  // Transform provider response to GitHub format
  transformResponse(response, operation) {
    // Response transformation logic
  }
  
  // Operation-specific methods
  async getFileContents(params) { /* ... */ }
  async createRepository(params) { /* ... */ }
  async createOrUpdateFile(params) { /* ... */ }
  // etc.
}
```

## Creating Custom Adapters

To create a custom adapter for a new Git provider:

1. Start with the [adapter template](adapter-template.js)
2. Identify parameter differences between GitHub and your provider
3. Map endpoints appropriately
4. Implement response transformations
5. Handle authentication specific to your provider

## Common Adapter Issues

### Parameter Mapping Issues

GitHub and other Git providers often use different parameter names for the same concept:

| Operation | GitHub Parameter | GitLab Parameter | Generic Git Parameter |
|-----------|------------------|------------------|----------------------|
| Repository privacy | `private: true` | `visibility: 'private'` | Varies |
| Branch reference | `ref: 'branch'` | `branch: 'branch'` | Varies |
| Commit SHA | `sha: 'abc123'` | `last_commit_id: 'abc123'` | Varies |

### Authentication Differences

Different providers use different authentication methods:

- GitHub: `Authorization: Bearer TOKEN`
- GitLab: `Private-Token: TOKEN`
- Others: May use different header or query parameter approaches

### Response Format Variations

Response formats vary significantly between providers:

```javascript
// Example response transformation
transformResponse(response, operation) {
  if (operation === 'getFileContents') {
    // GitHub returns content directly
    // GitLab wraps it in a 'content' field
    return {
      content: response.content || response,
      encoding: response.encoding || 'utf-8',
      // Map other fields accordingly
    };
  }
  // Other operations...
}
```

## Testing Your Adapter

Use the [adapter test suite](../scripts/test-adapter.js) to verify your adapter:

```bash
# Test your adapter against a specific provider
node scripts/test-adapter.js --adapter=./adapters/your-adapter.js --provider=your-provider
```
