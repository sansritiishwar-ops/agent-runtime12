---
name: github-repo-read-access
description: Use when you need to read data from GitHub repositories, including files, commits, issues, pull requests, repository structure, or metadata. This skill covers read-only GitHub operations.
---

# GitHub Repository Read Access

## When to Use This Skill

Use this skill when you need to:
- Read file contents from a GitHub repository
- Browse repository structure and list directory contents
- Retrieve commit history or specific commit details
- Read issues, pull requests, or their comments
- Get repository metadata (description, stars, forks, topics, license)
- Search for code within repositories
- List branches, tags, or releases
- Read repository README or documentation files
- Analyze repository statistics or contributor information

## When NOT to Use This Skill

**Do not use this skill for:**
- Creating repositories
- Pushing code or making commits
- Creating, updating, or closing issues or pull requests
- Modifying repository settings
- Managing webhooks or GitHub Actions
- Any write operations to GitHub

For write operations, you would need a different skill with appropriate write permissions.

## Authentication Requirements

### Public Repositories
- Many read operations on public repositories work without authentication
- However, authenticated requests have higher rate limits (5,000 vs 60 requests per hour)
- Always prefer authenticated access when available

### Private Repositories
- Require a GitHub Personal Access Token (PAT) or OAuth token
- Token must have appropriate scopes:
  - `repo` scope for full repository access
  - `read:org` scope for organization repository access
  - `read:user` scope for user-related information

### Token Handling
- Never hardcode tokens in code or expose them in logs
- Use secure environment variables or credential management systems
- Tokens should be provided through proper authentication connectors

## Available Read Operations

### 1. Reading File Contents
- Retrieve the content of specific files from any branch or commit
- Supports text and binary files (binary files are base64-encoded)
- Can specify a particular commit SHA, branch name, or tag

### 2. Repository Structure
- List files and directories at any path
- Navigate through directory trees
- Get file metadata (size, type, SHA, download URL)

### 3. Commit Information
- Retrieve commit history with pagination
- Get details of specific commits (author, message, changes, timestamp)
- Compare commits to see differences
- List commits on specific branches or by specific authors

### 4. Issues and Pull Requests
- List open, closed, or all issues
- Read issue details, labels, assignees, and comments
- List pull requests with filters (state, base branch, etc.)
- Read pull request details including review status and changed files
- Access PR comments and reviews

### 5. Repository Metadata
- Repository description, homepage, and topics
- Star count, fork count, watcher count
- Default branch, creation date, last update
- Language statistics
- License information
- Contributor list

### 6. Branches, Tags, and Releases
- List all branches and their latest commits
- List all tags
- Read release information including assets and release notes

### 7. Code Search
- Search for code within a repository
- Search across multiple repositories (if needed)
- Filter by file extension, path, or language

## Tool Requirements

This skill assumes you have access to tools or MCP servers that can interact with the GitHub API, such as:
- GitHub-specific tools (e.g., `get_github_file`, `list_github_repo`, `get_github_commits`)
- A GitHub MCP server that exposes GitHub API operations
- Generic HTTP tools with proper GitHub API endpoint knowledge

When using tools:
1. Check what GitHub tools or MCP servers are available in your tool set
2. Use the most specific tool for the operation (e.g., prefer `get_github_file` over generic HTTP requests)
3. Pass required parameters clearly (owner, repo name, file path, branch, etc.)
4. Handle pagination for large result sets (commits, issues, file listings)

## Error Handling

### Common Errors and Responses

**404 Not Found:**
- Repository doesn't exist or is private without proper access
- File or path doesn't exist in the specified branch/commit
- Response: Verify repository name, path, and access permissions

**401 Unauthorized / 403 Forbidden:**
- Missing or invalid authentication token
- Token lacks required scopes for the operation
- Response: Check token validity and required permissions

**Rate Limit Exceeded (403):**
- Exceeded GitHub API rate limits
- Response: Wait for rate limit reset (check `X-RateLimit-Reset` header) or switch to authenticated requests

**422 Unprocessable Entity:**
- Invalid parameters or malformed request
- Response: Validate input parameters (branch names, commit SHAs, etc.)

**Repository Empty:**
- Repository has no commits yet
- Response: Inform that the repository is newly created with no content

### Retry Strategy
- For rate limits: wait and retry after reset time
- For 5xx server errors: retry with exponential backoff
- For 4xx client errors: do not retry, fix the request instead

## Security Considerations

1. **Token Security:**
   - Never log or display authentication tokens
   - Use tokens with minimum required scopes
   - Rotate tokens regularly

2. **Data Privacy:**
   - Respect repository privacy settings
   - Don't cache or store sensitive repository data unnecessarily
   - Be mindful of private repositories and confidential code

3. **Access Control:**
   - Only access repositories you have permission to read
   - Verify organization policies before accessing org repositories
   - Respect repository visibility settings

4. **Rate Limiting:**
   - Monitor rate limit usage
   - Implement rate limiting on your side to avoid hitting GitHub's limits
   - Use conditional requests (ETags) when appropriate to save quota

## Best Practices

### Efficient API Usage
1. **Batch Operations:** When possible, retrieve multiple items in one request rather than making individual requests
2. **Pagination:** Handle paginated results properly, don't fetch all pages if you only need a few items
3. **Caching:** Cache repository metadata that doesn't change frequently
4. **Conditional Requests:** Use ETags and `If-None-Match` headers to avoid unnecessary data transfer
5. **Specific Queries:** Be as specific as possible (target branch, file path, date ranges) to reduce response size

### Repository Reference Format
- Use `owner/repo` format consistently (e.g., `octocat/Hello-World`)
- Validate repository names before making requests
- Handle both user repositories and organization repositories

### Branch and Commit References
- Default branch: Use repository's default branch (usually `main` or `master`)
- Specific branches: Reference by exact branch name
- Commits: Use full SHA when possible, though short SHA works for most operations
- Tags: Reference by tag name for release versions

## Workflow Example

Typical workflow for reading from a repository:

1. **Verify repository access:** Check if the repository exists and is accessible
2. **Get repository info:** Retrieve basic metadata to understand the repository
3. **Identify target:** Determine which branch, tag, or commit to read from
4. **Perform read operation:** Execute the specific read operation (get file, list commits, etc.)
5. **Process results:** Handle the returned data, including pagination if needed
6. **Error handling:** Gracefully handle any errors with clear messages

## Completion Reporting

When completing a task using this skill:
- Clearly state what was retrieved from which repository
- Mention the branch or commit if relevant
- If errors occurred, explain what went wrong and any corrective actions taken
- For large results, summarize rather than dumping all data
- Provide context about the repository when useful (stars, last update, etc.)
