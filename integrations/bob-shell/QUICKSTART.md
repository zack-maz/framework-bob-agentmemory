# Bob Shell Quick Start Guide

## 5-Minute Setup

### Step 1: Start the agentmemory server

Open a terminal and run:

```bash
npx @agentmemory/agentmemory
```

The server will start on `http://localhost:3111`. Keep this terminal open.

### Step 2: Configure Bob Shell

Choose **global** or **project** configuration:

#### Option A: Global Configuration (All Projects)

Create or edit `~/.bob/mcp_settings.json`:

```json
{
  "mcpServers": {
    "agentmemory": {
      "command": "npx",
      "args": ["-y", "@agentmemory/mcp"],
      "env": {
        "AGENTMEMORY_URL": "http://localhost:3111",
        "AGENTMEMORY_SECRET": "${AGENTMEMORY_SECRET}"
      }
    }
  }
}
```

#### Option B: Project Configuration (Single Project)

Create `.bob/mcp.json` in your project root:

```json
{
  "mcpServers": {
    "agentmemory": {
      "command": "npx",
      "args": ["-y", "@agentmemory/mcp"],
      "env": {
        "AGENTMEMORY_URL": "http://localhost:3111",
        "AGENTMEMORY_SECRET": "${AGENTMEMORY_SECRET}"
      }
    }
  }
}
```

**Windows users**: If direct `npx` doesn't work, use:
```json
{
  "mcpServers": {
    "agentmemory": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "@agentmemory/mcp"],
      "env": {
        "AGENTMEMORY_URL": "http://localhost:3111",
        "AGENTMEMORY_SECRET": "%AGENTMEMORY_SECRET%"
      }
    }
  }
}
```

### Step 3: Restart Bob Shell

Close and reopen Bob Shell to load the MCP configuration.

### Step 4: Verify Connection

In Bob Shell, ask:

```
"List available MCP tools"
```

You should see agentmemory tools like `memory_smart_search`, `memory_save`, `memory_sessions`, etc.

### Step 5: Test Memory Operations

Try these commands:

```
"Search my memory for TypeScript best practices"
"Remember that I prefer async/await over Promise chains"
"Show me my recent sessions"
```

## Daily Workflow

### Starting a Session

When you start working on a project, ask Bob to recall relevant context:

```
"Search my memory for information about [project name]"
"What do you remember about my preferences for [topic]?"
"Show me recent decisions about [area]"
```

### During Work

Save important information as you go:

```
"Remember that [important fact]"
"Save this decision: [decision details]"
"Observe that I prefer [pattern or approach]"
```

### Ending a Session

Before closing Bob Shell, save a session summary:

```
"Summarize this session and save it to memory"
"What should we remember from this conversation?"
"Save the key decisions and outcomes"
```

## Common Use Cases

### 1. Project Context Recall

```
"Search my memory for the architecture decisions we made for the authentication system"
"What patterns have we established for error handling in this codebase?"
```

### 2. Preference Management

```
"Remember that I always want unit tests for new features"
"Save my preference: use TypeScript strict mode"
"What are my coding style preferences?"
```

### 3. Decision Tracking

```
"Save this decision: we're using PostgreSQL instead of MongoDB because..."
"What database decisions have we made?"
"Show me all architecture decisions from the last month"
```

### 4. Session History

```
"Show me sessions from the last week"
"What did we work on yesterday?"
"List all sessions related to the API refactoring"
```

### 5. Profile and Timeline

```
"Show me my user profile"
"What's my recent activity timeline?"
"List my saved preferences"
```

## Tips for Best Results

### 1. Be Specific in Searches

❌ Bad: "Search memory"
✅ Good: "Search my memory for React component patterns we discussed"

### 2. Save Context, Not Just Facts

❌ Bad: "Remember: use TypeScript"
✅ Good: "Remember that I prefer TypeScript over JavaScript because of type safety and better IDE support"

### 3. Use Descriptive Session Summaries

❌ Bad: "Save this session"
✅ Good: "Summarize this session: we refactored the authentication system to use JWT tokens and implemented refresh token rotation"

### 4. Regular Memory Maintenance

- Review your profile periodically: `"Show me my user profile"`
- Check recent sessions: `"Show me sessions from the last week"`
- Update outdated preferences: `"Update my preference: now using Bun instead of Node.js"`

## Troubleshooting

### Tools Not Available

If agentmemory tools don't appear:

1. Check server is running: `curl http://localhost:3111/agentmemory/health`
2. Verify MCP config syntax in `~/.bob/mcp_settings.json` or `.bob/mcp.json`
3. Restart Bob Shell
4. On Windows, try `cmd /c npx` instead of direct `npx`

### Memory Operations Fail

If memory operations return errors:

1. Verify server connectivity: `curl http://localhost:3111/agentmemory/health`
2. Check authentication if using `AGENTMEMORY_SECRET`
3. Review error messages in Bob Shell output
4. Test REST API directly: `curl http://localhost:3111/agentmemory/health`

### Server Not Starting

If `npx @agentmemory/agentmemory` fails:

1. Check Node.js version: `node --version` (requires Node.js 18+)
2. Clear npx cache: `rm -rf ~/.npm/_npx` (macOS/Linux) or delete `%LOCALAPPDATA%\npm-cache\_npx` (Windows)
3. Try explicit version: `npx -y @agentmemory/agentmemory@latest`
4. Install globally: `npm install -g @agentmemory/agentmemory`

## Advanced Configuration

### Custom Server URL

If running agentmemory on a different port or host:

```json
{
  "mcpServers": {
    "agentmemory": {
      "command": "npx",
      "args": ["-y", "@agentmemory/mcp"],
      "env": {
        "AGENTMEMORY_URL": "http://your-server:port",
        "AGENTMEMORY_SECRET": "${AGENTMEMORY_SECRET}"
      }
    }
  }
}
```

### Authentication

To enable authentication, set `AGENTMEMORY_SECRET` environment variable:

**macOS/Linux:**
```bash
export AGENTMEMORY_SECRET="your-secret-key"
```

**Windows:**
```cmd
set AGENTMEMORY_SECRET=your-secret-key
```

Then start agentmemory with the same secret:

```bash
AGENTMEMORY_SECRET="your-secret-key" npx @agentmemory/agentmemory
```

### Multiple Projects

Use project-level `.bob/mcp.json` to have different configurations per project:

- Project A: `.bob/mcp.json` → `http://localhost:3111`
- Project B: `.bob/mcp.json` → `http://localhost:3112`

Or use the same server for all projects to share memory across them.

## Shared Memory Across Tools

Bob Shell shares the same agentmemory server with:

- **Claude Code** (native plugin + hooks)
- **Codex CLI** (native plugin + hooks)
- **Cursor** (MCP server)
- **Cline** (MCP server)
- **Goose** (MCP server)
- **Other MCP clients**

Memory saved in Bob Shell is immediately available in Claude Code, Cursor, and vice versa.

## Next Steps

- Read [README.md](./README.md) for detailed integration information
- Review [VALIDATION.md](./VALIDATION.md) for technical validation details
- Check [../../README.md](../../README.md) for full agentmemory documentation
- Explore MCP tools: `"List all available memory tools"`

## Getting Help

- **Documentation**: [agentmemory README](../../README.md)
- **Issues**: [GitHub Issues](https://github.com/rohitg00/agentmemory/issues)
- **Discussions**: [GitHub Discussions](https://github.com/rohitg00/agentmemory/discussions)
