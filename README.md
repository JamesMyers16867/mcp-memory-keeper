# MCP Memory Keeper - Claude Code Context Management

[![npm version](https://img.shields.io/npm/v/mcp-memory-keeper.svg)](https://www.npmjs.com/package/mcp-memory-keeper)
[![npm downloads](https://img.shields.io/npm/dm/mcp-memory-keeper.svg)](https://www.npmjs.com/package/mcp-memory-keeper)
[![CI](https://github.com/mkreyman/mcp-memory-keeper/actions/workflows/ci.yml/badge.svg)](https://github.com/mkreyman/mcp-memory-keeper/actions/workflows/ci.yml)
[![codecov](https://codecov.io/gh/mkreyman/mcp-memory-keeper/branch/main/graph/badge.svg)](https://codecov.io/gh/mkreyman/mcp-memory-keeper)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A Model Context Protocol (MCP) server that provides persistent context management for Claude AI coding assistants. Never lose context during compaction again! This MCP server helps Claude Code maintain context across sessions, preserving your work history, decisions, and progress.

## 🚀 Quick Start

Get started in under 30 seconds:

```bash
# Add memory-keeper to Claude
claude mcp add memory-keeper npx mcp-memory-keeper

# Start a new Claude session and use it!
# Try: Analyze the current repo and save your analysis in memory-keeper
```

That's it! Memory Keeper is now available in all your Claude sessions. Your context is stored in `~/mcp-data/memory-keeper/` and persists across sessions.

## 🚀 Practical Memory Keeper Workflow Example

### **Custom Command + CLAUDE.md = Automatic Context Management**

#### **CLAUDE.md** (condensed example)

```markdown
# Project Configuration

## Development Rules

- Always use memory-keeper to track progress
- Save architectural decisions and test results
- Create checkpoints before context limits

## Quality Standards

- All tests must pass before marking complete
- Document actual vs claimed results
```

#### **Custom Command Example: `/my-dev-workflow`**

```markdown
# My Development Workflow

When working on the provided project:

- Use memory-keeper with channel: <project_name>
- Save progress at every major milestone
- Document all decisions with category: "decision"
- Track implementation status with category: "progress"
- Before claiming anything is complete, save test results

## Workflow Steps

1. Initialize session with project name as channel
2. Save findings during investigation
3. Create checkpoint before major changes
4. Document what actually works vs what should work
```

#### **Usage Example**

```
User: /my-dev-workflow authentication-service

AI: Setting up workflow for authentication-service.
[Uses memory-keeper with channel "authentication-service"]

[... AI works, automatically saving context ...]

User: "Getting close to context limit. Create checkpoint and give me a key"

AI: "Checkpoint created: authentication-service-checkpoint-20250126-143026"

[Continue working until context reset or compact manually]

User: "Restore from key: authentication-service-checkpoint-20250126-143026"

AI: "Restored! Continuing OAuth implementation. We completed the token validation, working on refresh logic..."
```

**The Pattern:**

1. Custom command includes instructions to use memory-keeper
2. AI follows those instructions automatically
3. **When you notice the conversation getting long, YOU ask Claude to save a checkpoint** (like saving your game before a boss fight!)
4. **When Claude runs out of space and starts fresh, YOU tell it to restore using the checkpoint key**

**🎯 Key Feature:** Memory Keeper is a shared board! You can:

- Continue in the same session after reset
- Start a completely new session and restore
- Have multiple Claude sessions running in parallel, all sharing the same memory
- One session can save context that another session retrieves

This enables powerful workflows like having one Claude session doing research while another implements code, both sharing discoveries through Memory Keeper!

## Why MCP Memory Keeper?

Claude Code users often face context loss when the conversation window fills up. This MCP server solves that problem by providing a persistent memory layer for Claude AI. Whether you're working on complex refactoring, multi-file changes, or long debugging sessions, Memory Keeper ensures your Claude assistant remembers important context, decisions, and progress.

### Perfect for:

- Long coding sessions with Claude Code
- Complex projects requiring context preservation
- Teams using Claude AI for collaborative development
- Developers who want persistent context across Claude sessions

## Features

- 🔄 Save and restore context between Claude Code sessions
- 📁 File content caching with change detection
- 🏷️ Organize context with categories and priorities
- 📺 **Channels** - Persistent topic-based organization (auto-derived from git branch)
- 📸 Checkpoint system for complete context snapshots
- 🤖 Smart compaction helper that never loses critical info
- 🔍 Full-text search across all saved context
- 🕐 **Enhanced filtering** - Time-based queries, regex patterns, pagination
- 📊 **Change tracking** - See what's been added, modified, or deleted since any point
- 💾 Export/import for backup and sharing
- 🌿 Git integration with automatic context correlation
- 📊 AI-friendly summarization with priority awareness
- 🚀 Fast SQLite-based storage optimized for Claude
- 🔁 **Batch operations** - Save, update, or delete multiple items atomically
- 🔄 **Channel reassignment** - Move items between channels based on patterns
- 🔗 **Context relationships** - Link related items with typed relationships
- 👁️ **Real-time monitoring** - Watch for context changes with filters

## Installation

### Recommended: NPX Installation

```bash
claude mcp add memory-keeper npx mcp-memory-keeper
```

This single command:

- ✅ Always uses the latest version
- ✅ Handles all dependencies automatically
- ✅ Works across macOS, Linux, and Windows
- ✅ No manual building or native module issues

### Alternative Installation Methods

<details>
<summary>Global Installation</summary>

```bash
npm install -g mcp-memory-keeper
claude mcp add memory-keeper mcp-memory-keeper
```

</details>

<details>
<summary>From Source (for development)</summary>

```bash
# 1. Clone the repository
git clone https://github.com/mkreyman/mcp-memory-keeper.git
cd mcp-memory-keeper

# 2. Install dependencies
npm install

# 3. Build the project
npm run build

# 4. Add to Claude
claude mcp add memory-keeper /absolute/path/to/mcp-memory-keeper/bin/mcp-memory-keeper
```

</details>

## Configuration

### Environment Variables

#### Storage and Installation

- `DATA_DIR` - Directory for database storage (default: `~/mcp-data/memory-keeper/`)
- `MEMORY_KEEPER_INSTALL_DIR` - Installation directory (default: `~/.local/mcp-servers/memory-keeper/`)
- `MEMORY_KEEPER_AUTO_UPDATE` - Set to `1` to enable auto-updates

#### Token Limit Configuration

- `MCP_MAX_TOKENS` - Maximum tokens allowed in responses (default: `25000`, range: `1000-100000`)
  - Adjust this if your MCP client has different limits
- `MCP_TOKEN_SAFETY_BUFFER` - Safety buffer percentage (default: `0.8`, range: `0.1-1.0`)
  - Uses only this fraction of the max tokens to prevent overflows
- `MCP_MIN_ITEMS` - Minimum items to return even if exceeding limits (default: `1`, range: `1-100`)
  - Ensures at least some results are returned
- `MCP_MAX_ITEMS` - Maximum items allowed per response (default: `100`, range: `10-1000`)
  - Upper bound for result sets regardless of token limits
- `MCP_CHARS_PER_TOKEN` - Characters per token ratio (default: `3.5`, range: `2.5-5.0`) **[Advanced]**
  - Adjusts token estimation accuracy for different content types
  - Lower values = more conservative (safer but returns fewer items)
  - Higher values = more aggressive (returns more items but risks overflow)

Example configuration for stricter token limits:

```bash
export MCP_MAX_TOKENS=20000        # Lower max tokens
export MCP_TOKEN_SAFETY_BUFFER=0.7  # More conservative buffer
export MCP_MAX_ITEMS=50             # Fewer items per response
export MCP_CHARS_PER_TOKEN=3.0      # More conservative estimation (optional)
```

#### Tool Profiles

By default, all 38 tools are exposed. To reduce context overhead in your AI assistant, you can activate a tool profile that limits which tools are available.

**Quick usage:**

```bash
# Essential tools only (8 tools)
TOOL_PROFILE=minimal npx mcp-memory-keeper

# Standard workflow set (22 tools)
TOOL_PROFILE=standard npx mcp-memory-keeper

# All tools (default)
TOOL_PROFILE=full npx mcp-memory-keeper
```

**Built-in profiles:**

| Profile    | Tools | Description                                                    |
| ---------- | ----- | -------------------------------------------------------------- |
| `minimal`  | 8     | Core persistence: save, get, search, status, checkpoint        |
| `standard` | 22    | Daily workflow: core + git, batch ops, channels, export/import |
| `full`     | 38    | All tools (default, backwards compatible)                      |

**Custom profiles via config file:**

Create `~/.mcp-memory-keeper/config.json` to define or override profiles:

```json
{
  "profiles": {
    "my_workflow": [
      "context_session_start",
      "context_save",
      "context_get",
      "context_search",
      "context_checkpoint",
      "context_restore_checkpoint",
      "context_diff",
      "context_timeline"
    ]
  }
}
```

Then activate it: `TOOL_PROFILE=my_workflow npx mcp-memory-keeper`

Config file profiles take precedence over built-in defaults with the same name.

**Profile resolution precedence:**

| `TOOL_PROFILE` | Config file has profile? | Built-in exists? | Result                           |
| -------------- | ------------------------ | ---------------- | -------------------------------- |
| Set            | Yes                      | —                | Uses config file definition      |
| Set            | No                       | Yes              | Uses built-in definition         |
| Set            | No                       | No               | Warning + falls back to `full`   |
| Not set        | —                        | —                | Uses built-in `full` (all tools) |

**Environment variables:**

| Variable              | Description                                                               |
| --------------------- | ------------------------------------------------------------------------- |
| `TOOL_PROFILE`        | Profile name to activate (e.g., `minimal`, `standard`, `full`, or custom) |
| `TOOL_PROFILE_CONFIG` | Override config file path (default: `~/.mcp-memory-keeper/config.json`)   |

> Note: Profile resolution happens once at server startup. Changes to the env var or config file take effect on the next server restart.

**Claude Code / Claude Desktop configuration:**

```json
{
  "mcpServers": {
    "memory-keeper": {
      "command": "npx",
      "args": ["mcp-memory-keeper"],
      "env": {
        "TOOL_PROFILE": "minimal"
      }
    }
  }
}
```

See `examples/config.json` for a complete example config file.

### Claude Code (CLI)

#### Configuration Scopes

Choose where to save the configuration:

```bash
# Project-specific (default) - only for you in this project
claude mcp add memory-keeper npx mcp-memory-keeper

# Shared with team via .mcp.json
claude mcp add --scope project memory-keeper npx mcp-memory-keeper

# Available across all your projects
claude mcp add --scope user memory-keeper npx mcp-memory-keeper
```

#### Verify Configuration

```bash
# List all configured servers
claude mcp list

# Get details for Memory Keeper
claude mcp get memory-keeper
```

### Claude Desktop App

1. Open Claude Desktop settings
2. Navigate to "Developer" → "Model Context Protocol"
3. Click "Add MCP Server"
4. Add the following configuration:

```json
{
  "mcpServers": {
    "memory-keeper": {
      "command": "npx",
      "args": ["mcp-memory-keeper"]
    }
  }
}
```

That's it! No paths needed - npx automatically handles everything.

### Verify Installation

#### For Claude Code:

1. Restart Claude Code or start a new session
2. The Memory Keeper tools should be available automatically
3. Test with: `mcp_memory_save({ key: "test", value: "Hello Memory Keeper!" })`
4. If not working, check server status:
   ```bash
   claude mcp list  # Should show memory-keeper as "running"
   ```

#### For Claude Desktop:

1. Restart Claude Desktop after adding the configuration
2. In a new conversation, the Memory Keeper tools should be available
3. Test with the same command above

### Troubleshooting

If Memory Keeper isn't working:

```bash
# Remove and re-add the server
claude mcp remove memory-keeper
claude mcp add memory-keeper npx mcp-memory-keeper

# Check logs for errors
# The server output will appear in Claude Code's output panel
```

### Updating to Latest Version

With the npx installation method, you automatically get the latest version every time! No manual updates needed.

If you're using the global installation method:

```bash
# Update to latest version
npm update -g mcp-memory-keeper

# Start a new Claude session
# The updated features will be available immediately
```

**Note**: You don't need to reconfigure the MCP server in Claude after updating. Just start a new session!

## Usage

### Session Management

```javascript
// Start a new session
mcp_context_session_start({
  name: 'Feature Development',
  description: 'Working on user authentication',
});

// Start a session with project directory for git tracking
mcp_context_session_start({
  name: 'Feature Development',
  description: 'Working on user authentication',
  projectDir: '/path/to/your/project',
});

// Start a session with a default channel
mcp_context_session_start({
  name: 'Feature Development',
  description: 'Working on user authentication',
  projectDir: '/path/to/your/project',
  defaultChannel: 'auth-feature', // Will auto-derive from git branch if not specified
});

// Set project directory for current session
mcp_context_set_project_dir({
  projectDir: '/path/to/your/project',
});

// List recent sessions
mcp_context_session_list({ limit: 5 });

// Continue from a previous session
mcp_context_session_start({
  name: 'Feature Dev Continued',
  continueFrom: 'previous-session-id',
});
```

### Working with Channels (NEW in v0.10.0)

Channels provide persistent topic-based organization that survives session crashes and restarts:

```javascript
// Channels are auto-derived from git branch (if projectDir is set)
// Branch "feature/auth-system" becomes channel "feature-auth-system" (20 chars max)

// Save to a specific channel
mcp_context_save({
  key: 'auth_design',
  value: 'Using JWT with refresh tokens',
  category: 'decision',
  priority: 'high',
  channel: 'auth-feature', // Explicitly set channel
});

// Get items from a specific channel
mcp_context_get({ channel: 'auth-feature' });

// Get items across all channels (default behavior)
mcp_context_get({ category: 'task' });

// Channels persist across sessions - perfect for:
// - Multi-branch development
// - Feature-specific context
// - Team collaboration on different topics
```

### Enhanced Context Storage

```javascript
// Save with categories and priorities
mcp_context_save({
  key: 'current_task',
  value: 'Implement OAuth integration',
  category: 'task',
  priority: 'high',
});

// Save decisions
mcp_context_save({
  key: 'auth_strategy',
  value: 'Using JWT tokens with 24h expiry',
  category: 'decision',
  priority: 'high',
});

// Save progress notes
mcp_context_save({
  key: 'progress_auth',
  value: 'Completed user model, working on token generation',
  category: 'progress',
  priority: 'normal',
});

// Retrieve by category
mcp_context_get({ category: 'task' });

// Retrieve specific item
mcp_context_get({ key: 'current_task' });

// Get context from specific session
mcp_context_get({
  sessionId: 'session-id-here',
  category: 'decision',
});

// Enhanced filtering (NEW in v0.10.0)
mcp_context_get({
  category: 'task',
  priorities: ['high', 'normal'],
  includeMetadata: true, // Get timestamps, size info
  sort: 'created_desc', // created_asc/desc, updated_asc/desc, priority
  limit: 10, // Pagination
  offset: 0,
});

// Time-based queries (NEW in v0.10.0)
mcp_context_get({
  createdAfter: '2025-01-20T00:00:00Z',
  createdBefore: '2025-01-26T23:59:59Z',
  includeMetadata: true,
});

// Pattern matching (NEW in v0.10.0)
mcp_context_get({
  keyPattern: 'auth_.*', // Regex to match keys
  category: 'decision',
});
```

### File Caching

```javascript
// Cache file content for change detection
mcp_context_cache_file({
  filePath: '/src/auth/user.model.ts',
  content: fileContent,
});

// Check if file has changed
mcp_context_file_changed({
  filePath: '/src/auth/user.model.ts',
  currentContent: newFileContent,
});

// Get current session status
mcp_context_status();
```

### Complete Workflow Example

```javascript
// 1. Start a new session
mcp_context_session_start({
  name: 'Settings Refactor',
  description: 'Refactoring settings module for better performance',
});

// 2. Save high-priority task
mcp_context_save({
  key: 'main_task',
  value: 'Refactor Settings.Context to use behaviors',
  category: 'task',
  priority: 'high',
});

// 3. Cache important files
mcp_context_cache_file({
  filePath: 'lib/settings/context.ex',
  content: originalFileContent,
});

// 4. Save decisions as you work
mcp_context_save({
  key: 'architecture_decision',
  value: 'Split settings into read/write modules',
  category: 'decision',
  priority: 'high',
});

// 5. Track progress
mcp_context_save({
  key: 'progress_1',
  value: 'Completed behavior definition, 5 modules remaining',
  category: 'progress',
  priority: 'normal',
});

// 6. Before context window fills up
mcp_context_status(); // Check what's saved

// 7. After Claude Code restart
mcp_context_get({ category: 'task', priority: 'high' }); // Get high priority tasks
mcp_context_get({ key: 'architecture_decision' }); // Get specific decisions
mcp_context_file_changed({ filePath: 'lib/settings/context.ex' }); // Check for changes
```

### Checkpoints (Phase 2)

Create named snapshots of your entire context that can be restored later:

```javascript
// Create a checkpoint before major changes
mcp_context_checkpoint({
  name: 'before-refactor',
  description: 'State before major settings refactor',
  includeFiles: true, // Include cached files
  includeGitStatus: true, // Capture git status
});

// Continue working...
// If something goes wrong, restore from checkpoint
mcp_context_restore_checkpoint({
  name: 'before-refactor',
  restoreFiles: true, // Restore cached files too
});

// Or restore the latest checkpoint
mcp_context_restore_checkpoint({});
```

### Context Summarization (Phase 2)

Get AI-friendly summaries of your saved context:

```javascript
// Get a summary of all context
mcp_context_summarize();

// Get summary of specific categories
mcp_context_summarize({
  categories: ['task', 'decision'],
  maxLength: 2000,
});

// Summarize a specific session
mcp_context_summarize({
  sessionId: 'session-id-here',
  categories: ['progress'],
});
```

Example summary output:

```markdown
# Context Summary

## High Priority Items

- **main_task**: Refactor Settings.Context to use behaviors
- **critical_bug**: Fix memory leak in subscription handler

## Task

- implement_auth: Add OAuth2 authentication flow
- update_tests: Update test suite for new API

## Decision

- architecture_decision: Split settings into read/write modules
- db_choice: Use PostgreSQL for better JSON support
```

### Batch Operations

Perform multiple operations atomically:

```javascript
// Save multiple items at once
mcp_context_batch_save({
  items: [
    { key: 'config_api_url', value: 'https://api.example.com', category: 'note' },
    { key: 'config_timeout', value: '30000', category: 'note' },
    { key: 'config_retries', value: '3', category: 'note' },
  ],
});

// Update multiple items
mcp_context_batch_update({
  updates: [
    { key: 'ta