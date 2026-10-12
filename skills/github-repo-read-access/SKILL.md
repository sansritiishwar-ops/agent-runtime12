---
name: github-repo-read-access
description: Use when you need to interact with GitHub repositories, including reading or writing files, managing commits, issues, pull requests, branches, and repository settings. Covers both read and write GitHub operations.
---

# GitHub Repository Access

## When to Use This Skill

Use this skill when you need to:

### Read Operations
- Read file contents from a GitHub repository
- Browse repository structure and list directory contents
- Retrieve commit history or specific commit details
- Read issues, pull requests, or their comments
- Get repository metadata (description, stars, forks, topics, license)
- Search for code within repositories
- List branches, tags, or releases
- Read repository README or documentation files
- Analyze repository statistics or contributor information

### Write Operations
- Create or update files in a repository
- Make commits and push code to branches
- Create, update, or close issues
- Create, update, merge, or close pull requests
- Add comments, labels, or assignees to issues/PRs
- Create or delete branches
- Create or delete tags
- Create, update, or delete releases
- Update repository settings (description, topics, visibility)
- Manage webhooks
- Manage repository collaborators and permissions

## Authentication Requirements

### Public Repositories (Read)
- Many read operations on public repositories work without authentication
- However, authenticated requests have higher rate limits (5,000 vs 60 requests per hour)
- Always prefer authenticated access when available

### Private Repositories (Read)
- Require a GitHub Personal Access Token (PAT) or OAuth token
- Token must have appropriate read scopes:
  - `repo` scope for full repository access
  - `read:org` scope for organization repository access
  - `read:user` scope for user-related information

### Write Operations (All Repositories)
- **Always require authentication** with a token that has write permissions
- Required scopes depend on the operation:
  - `repo` scope: Full control of private and public repositories (read and write)
  - `public_repo` scope: Access to public repositories only (read and write)
  - `workflow` scope: Update GitHub Actions workflow files
  - `admin:repo_hook` scope: Manage repository webhooks
  - `admin:org` scope: Manage organization settings and teams (for org repos)
  - `delete_repo` scope: Delete repositories (rarely needed)

### OAuth Considerations
- For user-initiated actions, use OAuth flows to act on behalf of the user
- Request minimum required scopes for the specific operation
- Clearly communicate what permissions are needed and why
- Respect user consent and token expiration

### Token Handling
- Never hardcode tokens in code or expose them in logs
- Use secure environment variables or credential management systems
- Tokens should be provided through proper authentication connectors
- Rotate tokens regularly, especially those with write access
- Use fine-grained personal access tokens when possible for better security

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

## Available Write Operations

### 1. Creating and Updating Files

**Single File Operations:**
- Create new files using the GitHub Contents API
- Update existing files (requires the current file's SHA)
- Delete files (requires the current file's SHA)
- All operations create a commit automatically

**Required Parameters:**
- Repository (owner/repo)
- File path
- File content (base64-encoded for binary files)
- Commit message
- Branch name
- File SHA (for updates and deletes)

**Best Practices:**
- Always provide meaningful commit messages
- Verify the file path is correct before writing
- For updates, fetch the current SHA first to avoid conflicts
- Consider file size limits (100 MB for regular files)

### 2. Making Commits and Pushing Code

**Git Data API:**
- Create blobs (file contents)
- Create trees (directory structures)
- Create commits with multiple files
- Update references (branches) to point to new commits

**Workflow:**
1. Get the current commit SHA of the target branch
2. Create blobs for each file being changed
3. Create a tree with the new structure
4. Create a commit pointing to the new tree
5. Update the branch reference to the new commit

**When to Use:**
- Multiple file changes in a single commit
- Complex repository structure modifications
- Maintaining commit history integrity

### 3. Branch Management

**Operations:**
- **Create branch:** From any existing commit or branch
- **Delete branch:** Remove merged or obsolete branches
- **List branches:** Get all branches with metadata
- **Get branch:** Retrieve specific branch details
- **Update branch protection:** Configure protection rules

**Branch Protection Rules:**
- Require pull request reviews
- Require status checks to pass
- Require signed commits
- Restrict who can push
- Require linear history

**Best Practices:**
- Use descriptive branch names (feature/, bugfix/, hotfix/ prefixes)
- Never force-push to protected branches
- Delete merged branches to keep repository clean
- Respect branch protection rules

### 4. Tag Management

**Operations:**
- **Create lightweight tag:** Simple pointer to a commit
- **Create annotated tag:** Tag with metadata (tagger, date, message)
- **Delete tag:** Remove obsolete tags
- **List tags:** Get all repository tags

**Use Cases:**
- Version releases (v1.0.0, v2.0.1)
- Mark important milestones
- Reference specific stable points

### 5. Issue Management

**Operations:**
- **Create issue:** With title, body, labels, assignees
- **Update issue:** Modify title, body, state, labels, assignees, milestone
- **Close issue:** Mark as completed
- **Reopen issue:** Reactivate a closed issue
- **Add comments:** Contribute to issue discussion
- **Add labels:** Categorize issues
- **Assign users:** Designate responsibility
- **Set milestone:** Track progress toward goals

**Best Practices:**
- Use clear, descriptive titles
- Provide detailed descriptions with reproduction steps (for bugs)
- Add relevant labels for categorization
- Link related issues and pull requests
- Keep issues focused on single topics

### 6. Pull Request Management

**Operations:**
- **Create PR:** From one branch to another (across forks supported)
- **Update PR:** Modify title, body, base branch
- **Merge PR:** Merge, squash, or rebase strategies
- **Close PR:** Without merging
- **Request reviewers:** Assign code reviewers
- **Add comments:** General or line-specific comments
- **Submit reviews:** Approve, request changes, or comment
- **Update PR branch:** Merge or rebase with base branch

**Merge Strategies:**
- **Merge commit:** Preserves all commits and creates a merge commit
- **Squash and merge:** Combines all commits into one
- **Rebase and merge:** Replays commits on top of base branch

**Best Practices:**
- Write clear PR titles and descriptions
- Reference related issues ("Fixes #123")
- Keep PRs focused and reasonably sized
- Respond to review feedback promptly
- Ensure CI/CD checks pass before merging
- Use draft PRs for work in progress

### 7. Release Management

**Operations:**
- **Create release:** With tag, title, description, and assets
- **Update release:** Modify release details
- **Delete release:** Remove a release (tag remains unless deleted separately)
- **Upload release assets:** Attach binaries, documentation, etc.
- **Delete release assets:** Remove specific assets

**Release Types:**
- **Full release:** Stable, production-ready
- **Pre-release:** Beta, alpha, release candidate
- **Draft:** Unpublished, for preparation

**Best Practices:**
- Use semantic versioning (MAJOR.MINOR.PATCH)
- Write comprehensive release notes
- Include changelogs and breaking changes
- Attach relevant binaries and documentation
- Mark unstable releases as pre-releases

### 8. Repository Settings Management

**Operations:**
- **Update repository:** Modify description, homepage, topics, visibility
- **Update default branch:** Change which branch is default
- **Enable/disable features:** Issues, wiki, projects, discussions
- **Archive repository:** Make read-only
- **Transfer repository:** Move to different owner/organization

**Visibility Options:**
- **Public:** Anyone can see
- **Private:** Only collaborators can see
- **Internal:** Organization members can see (for enterprise)

**Critical Operations (Use with Caution):**
- **Delete repository:** Permanent and irreversible
- **Transfer ownership:** Changes control
- **Archive:** Prevents further changes

### 9. Webhook Management

**Operations:**
- **Create webhook:** Subscribe to repository events
- **Update webhook:** Modify URL, events, or settings
- **Delete webhook:** Remove webhook
- **Test webhook:** Trigger a ping event
- **List webhooks:** Get all configured webhooks

**Common Events:**
- push, pull_request, issues, release, fork, star, etc.

### 10. Collaborator Management

**Operations:**
- **Add collaborator:** Grant repository access
- **Remove collaborator:** Revoke access
- **Update permissions:** Change permission level (read, write, admin)
- **List collaborators:** Get all with access

## Tool Requirements

This skill assumes you have access to tools or MCP servers that can interact with the GitHub API, such as:
- GitHub-specific tools (e.g., `get_github_file`, `create_github_issue`, `merge_github_pr`)
- A GitHub MCP server that exposes GitHub API operations
- Generic HTTP tools with proper GitHub API endpoint knowledge

When using tools:
1. Check what GitHub tools or MCP servers are available in your tool set
2. Use the most specific tool for the operation
3. Pass required parameters clearly (owner, repo name, file path, branch, etc.)
4. Handle pagination for large result sets
5. For write operations, verify required parameters before making the call
6. Always check for errors in responses

## Error Handling

### Common Errors and Responses

**404 Not Found:**
- Repository, file, issue, or PR doesn't exist
- Resource is private without proper access
- Response: Verify names, paths, and access permissions

**401 Unauthorized / 403 Forbidden:**
- Missing or invalid authentication token
- Token lacks required scopes for the operation
- Protected branch prevents the action
- Response: Check token validity, required scopes, and branch protection rules

**409 Conflict:**
- File was modified since you last read it (SHA mismatch)
- Branch already exists (when creating)
- Cannot be merged due to conflicts
- Response: Fetch latest version, resolve conflicts, retry

**422 Unprocessable Entity:**
- Invalid parameters or malformed request
- Validation failed (e.g., invalid branch name, missing required field)
- Response: Validate input parameters and fix issues

**Rate Limit Exceeded (403):**
- Exceeded GitHub API rate limits
- Response: Wait for rate limit reset or use authenticated requests with higher limits

**Repository Empty:**
- Repository has no commits yet
- Response: Initialize repository with first commit

**Merge Conflicts:**
- Changes cannot be automatically merged
- Response: Resolve conflicts manually or via API

**Branch Protection Violations:**
- Attempting to push directly to protected branch
- Required status checks not passing
- Required reviews not obtained
- Response: Follow branch protection workflow (create PR, get reviews, pass checks)

### Retry Strategy
- For rate limits: wait and retry after reset time
- For 5xx server errors: retry with exponential backoff (up to 3 attempts)
- For 409 conflicts: fetch latest, merge/resolve, retry once
- For 4xx client errors (except 409): do not retry, fix the request instead
- For 401/403 on write operations: verify token scopes before retrying

## Security Considerations

### 1. Token Security
- Never log or display authentication tokens
- Use tokens with minimum required scopes
- Rotate tokens regularly, especially write-access tokens
- Use fine-grained tokens when possible
- Revoke tokens immediately if compromised
- Store tokens in secure vaults, not in code or configuration files

### 2. Data Privacy
- Respect repository privacy settings
- Don't cache or store sensitive repository data unnecessarily
- Be mindful of private repositories and confidential code
- Sanitize logs and error messages to avoid leaking sensitive data

### 3. Access Control
- Only access repositories you have permission to access
- Verify organization policies before accessing org repositories
- Respect repository visibility settings
- Use principle of least privilege for token scopes

### 4. Validation Before Destructive Operations
- **Always confirm before:**
  - Deleting branches, tags, or files
  - Force-pushing to branches
  - Closing issues or pull requests
  - Deleting or archiving repositories
  - Transferring ownership
- Implement confirmation prompts or require explicit flags
- Log destructive operations for audit trails

### 5. Input Validation
- Validate all user inputs before passing to GitHub API
- Sanitize file paths to prevent directory traversal
- Validate branch names, tag names, and commit messages
- Check for malicious content in files being uploaded
- Verify URLs and webhook endpoints

### 6. Rate Limiting
- Monitor rate limit usage
- Implement rate limiting on your side to avoid hitting GitHub's limits
- Use conditional requests (ETags) when appropriate to save quota
- For write operations, be especially mindful of limits

### 7. Audit Logging
- Log all write operations with:
  - Timestamp
  - User/token identifier
  - Repository and resource affected
  - Action taken
  - Result (success/failure)
- Maintain logs for security audits and debugging

### 8. Sensitive Operations
- **Force push:** Can rewrite history and cause data loss
- **Repository deletion:** Permanent and irreversible
- **Webhook creation:** Can send data to external endpoints
- **Permission changes:** Can grant unauthorized access
- **Secret management:** Never commit secrets to repositories

## Best Practices

### Efficient API Usage
1. **Batch Operations:** When possible, retrieve multiple items in one request
2. **Pagination:** Handle paginated results properly, don't fetch all pages if not needed
3. **Caching:** Cache repository metadata that doesn't change frequently
4. **Conditional Requests:** Use ETags and `If-None-Match` headers
5. **Specific Queries:** Be as specific as possible to reduce response size
6. **GraphQL API:** Consider using GitHub's GraphQL API for complex queries

### Repository Reference Format
- Use `owner/repo` format consistently (e.g., `octocat/Hello-World`)
- Validate repository names before making requests
- Handle both user repositories and organization repositories
- Support repository IDs as alternative to names

### Branch and Commit References
- Default branch: Use repository's default branch (usually `main` or `master`)
- Specific branches: Reference by exact branch name
- Commits: Use full SHA when possible (short SHA works for most operations)
- Tags: Reference by tag name for release versions

### Write Operation Best Practices

**Before Writing:**
1. Verify you have necessary permissions
2. Check branch protection rules
3. Fetch latest state to avoid conflicts
4. Validate input data

**When Writing:**
1. Use meaningful commit messages (present tense, imperative mood)
2. Include context and reasoning in PR/issue descriptions
3. Add relevant labels and assignees
4. Link related issues and PRs
5. Follow repository contribution guidelines

**After Writing:**
1. Verify the operation succeeded
2. Check for any warnings or notifications
3. Monitor for CI/CD pipeline results
4. Respond to reviews and feedback

### Pull Request Workflow
1. Create feature branch from latest main/default
2. Make changes and commit with clear messages
3. Push branch to remote
4. Create pull request with detailed description
5. Request reviewers
6. Address review feedback
7. Ensure all checks pass
8. Merge using appropriate strategy
9. Delete feature branch after merge

### Commit Message Format
```
<type>: <subject>

<body>

<footer>
```

Types: feat, fix, docs, style, refactor, test, chore

Example:
```
feat: add user authentication

Implement JWT-based authentication system with refresh tokens.
Includes login, logout, and token refresh endpoints.

Closes #123
```

## Workflow Examples

### Example 1: Reading from a Repository

1. **Verify repository access:** Check if repository exists and is accessible
2. **Get repository info:** Retrieve basic metadata
3. **Identify target:** Determine which branch/commit to read from
4. **Perform read operation:** Get file, list commits, etc.
5. **Process results:** Handle data and pagination
6. **Error handling:** Handle errors with clear messages

### Example 2: Creating and Updating a File

1. **Authenticate:** Ensure token has `repo` scope
2. **Check repository access:** Verify write permissions
3. **Determine target branch:** Usually feature branch, not main
4. **For updates:** Fetch current file SHA
5. **Prepare content:** Format file content properly
6. **Create/update file:** Make API call with commit message
7. **Verify result:** Check response for success
8. **Handle errors:** Retry on conflict after fetching latest

### Example 3: Creating a Pull Request

1. **Create feature branch:** From latest main/default branch
2. **Make changes:** Create/update files with commits
3. **Push branch:** Upload changes to remote
4. **Create PR:** Specify base and head branches, add description
5. **Request reviewers:** Add team members or specific users
6. **Monitor checks:** Wait for CI/CD to complete
7. **Address feedback:** Update PR based on reviews
8. **Merge PR:** When approved and checks pass
9. **Clean up:** Delete feature branch

### Example 4: Managing an Issue

1. **Create issue:** With descriptive title and detailed body
2. **Add labels:** Categorize (bug, enhancement, question, etc.)
3. **Assign users:** Designate who should work on it
4. **Set milestone:** Track toward release goal
5. **Add comments:** Discuss and provide updates
6. **Link PR:** Reference related pull request
7. **Close issue:** When resolved, with closing comment

## Completion Reporting

When completing a task using this skill:

**For Read Operations:**
- Clearly state what was retrieved from which repository
- Mention the branch or commit if relevant
- If errors occurred, explain what went wrong and corrective actions taken
- For large results, summarize rather than dumping all data
- Provide context about the repository when useful (stars, last update, etc.)

**For Write Operations:**
- Clearly state what action was performed on which repository
- Include relevant identifiers (commit SHA, PR number, issue number, branch name)
- Mention the branch or target of the operation
- Confirm success or explain any failures
- For PRs and issues, include the URL for easy access
- For destructive operations, confirm what was deleted/modified
- If approval or reviews are pending, communicate the next steps
