<p align="center">
  <img src="../../assets/banner.png" alt="agentmemory" width="640" />
</p>

<h1 align="center">
  &nbsp;agentmemory for Bob Shell
</h1>

<p align="center">
  <strong>Persistent cross-session memory for Bob Shell via agentmemory MCP.</strong><br/>
  <sub>Shared with Claude Code, Codex CLI, Gemini CLI, Cursor, OpenClaw, Hermes, pi, and other MCP clients.</sub>
</p>

---

## Status

Bob Shell support is currently **MCP-only** with **manual workflow patterns**.

### What Works Today ✅

- Bob Shell loads `agentmemory` through MCP server integration
- Both global (`~/.bob/mcp_settings.json`) and project-local (`.bob/mcp.json`) MCP config files are supported
- Project-level config overrides global config when the same server name is defined in both
- All agentmemory MCP tools are accessible via natural language requests
- MCP server configuration persists across Bob Shell restarts
- Memory is shared with Claude Code, Codex CLI, Cursor, and other MCP clients

### What Does NOT Work ❌

Bob Shell does **not** support:

- **Lifecycle hooks** - no session start, session end, or tool use hooks
- **Plugin APIs** - no native plugin system for automation
- **Automatic recall** - no way to trigger memory search on session start
- **Automatic capture** - no way to save session summaries on session end
- **Enforced startup commands** - cannot guarantee memory operations run automatically

### Integration Tier

Bob Shell is classified as **MCP server** tier, similar to Cursor, Cline, Goose, and other MCP-only integrations.

It is **NOT** in the same tier as Claude Code or Codex CLI, which have native plugins and lifecycle hooks for automatic memory operations.

See [VALIDATION.md](./VALIDATION.md) for detailed validation results and test outcomes.

## Goal

Bob Shell provides agentmemory integration through MCP server support:

1. ✅ MCP server loads automatically on every new Bob session
2. ✅ All agentmemory tools accessible via natural language
3. ✅ Reproducible behavior across restart and documented config paths
4. ⚠️ Memory operations require manual invocation (no automatic recall/capture)

**Note**: Bob Shell does not support automatic lifecycle automation like Claude Code or Codex CLI. Memory operations must be triggered manually through natural language requests.

## Quick setup

### 1. Start the agentmemory server

Run the server in a separate terminal:

```bash
npx @agentmemory/agentmemory
```

The default server URL is `http://localhost:3111`.

### 2. Configure Bob Shell to launch the MCP shim

Bob Shell uses the `@agentmemory/mcp` shim to expose the shared memory tools.

### Global config

Bob Shell applies global MCP settings from:

- `~/.bob/mcp_settings.json`

Add the `agentmemory` entry inside the existing `mcpServers` object:

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

### Project config

For project-local setup, use:

- `.bob/mcp.json`

Project-level Bob Shell MCP config overrides the global config when the same server name exists in both files.

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

### Windows note

On Windows, Bob Shell MCP commands may need `cmd /c` if `npx` is not resolved directly by the host process.

Use this variant if direct `npx` fails:

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

## What Bob gets today

With a running agentmemory server, Bob Shell gets the shared MCP memory surface through `@agentmemory/mcp`, including tools such as:

- `memory_smart_search`
- `memory_save`
- `memory_sessions`
- `memory_profile`
- `memory_timeline`
- `memory_governance_delete`

If no remote server is reachable, the shim falls back to the smaller local tool set.

## Operating Model

### How It Works

Bob Shell integrates with agentmemory through **MCP server** support:

1. **Setup**: Configure MCP server once in `~/.bob/mcp_settings.json` or `.bob/mcp.json`
2. **Startup**: Bob Shell automatically loads the MCP server on each new session
3. **Usage**: Invoke memory tools through natural language requests
4. **Persistence**: MCP configuration persists across restarts

### Manual Workflow Pattern

Since Bob Shell lacks lifecycle hooks, memory operations require manual invocation:

**Session Start:**
- Ask Bob to search memory: "Search my memory for information about this project"
- Or use specific tools: "Use memory_smart_search to find recent decisions"

**During Session:**
- Save important facts: "Remember that I prefer TypeScript over JavaScript"
- Observe key events: "Save this decision to memory"

**Session End:**
- Summarize session: "Summarize this session and save it to memory"
- Or use specific tools: "Use memory_observe to capture this session"

### Why No Automatic Behavior

Bob Shell does **not** expose:
- Session start/end hooks
- Plugin APIs for automation
- Enforced startup command execution

Therefore, automatic recall and capture are **not possible** without user or AI-initiated tool calls.

## Integration Capabilities

Bob Shell's agentmemory integration provides:

### ✅ What's Supported

1. **MCP Server Auto-Loading**: The `agentmemory` MCP server loads automatically in each new Bob session
2. **Tool Availability**: All agentmemory tools are accessible via natural language requests
3. **Configuration Persistence**: MCP configuration persists across restarts
4. **Shared Memory**: Memory is shared with Claude Code, Codex CLI, Cursor, and other MCP clients

### ❌ What's NOT Supported

1. **Automatic Recall**: No automatic memory search on session start (requires manual invocation)
2. **Automatic Capture**: No automatic session summary on session end (requires manual invocation)
3. **Lifecycle Hooks**: No session start/end hooks for automation
4. **Plugin APIs**: No native plugin system for automatic behavior

### Integration Tier Classification

Bob Shell is classified as **MCP server** tier, not **native plugin + hooks** tier.

## Validation checklist

After saving the config:

1. restart Bob Shell in the target project
2. confirm the agentmemory server is healthy:
   ```bash
   curl http://localhost:3111/agentmemory/health
   ```
3. ask Bob Shell to list or use MCP tools and verify `agentmemory` tools are available
4. close Bob Shell and open a fresh session
5. verify the MCP server is still available after restart

## Validation Results

| Test | Status | Result |
|------|--------|--------|
| Global MCP config | ✅ Pass | `agentmemory` loads automatically from `~/.bob/mcp_settings.json` |
| Project MCP config | ✅ Pass | `.bob/mcp.json` overrides global config successfully |
| Restart persistence | ✅ Pass | MCP server remains configured after Bob Shell restart |
| Windows launch | ⚠️ Platform-dependent | Direct `npx` or `cmd /c npx` may be required (test both) |
| macOS/Linux launch | ⚠️ Not tested | Direct `npx` expected to work |
| Automatic recall | ❌ Not supported | No session start hooks available |
| Automatic capture | ❌ Not supported | No session end hooks available |
| Failure handling | ⚠️ Graceful degradation | MCP tools unavailable when server is down |

**Overall Integration Status**: 3/8 tests fully passing, 2/8 platform-dependent, 3/8 not supported by Bob Shell architecture.

See [VALIDATION.md](./VALIDATION.md) for detailed test procedures and findings.

## Usage Recommendations

### Best Practices for Manual Workflow

Since Bob Shell requires manual memory operations, follow these patterns:

**1. Session Start Pattern**
```
"Search my memory for information about [project/topic]"
"What do you remember about my preferences for [area]?"
"Show me recent decisions related to [context]"
```

**2. During Session Pattern**
```
"Remember that [important fact or preference]"
"Save this decision: [decision details]"
"Observe this pattern: [workflow or approach]"
```

**3. Session End Pattern**
```
"Summarize this session and save it to memory"
"What should we remember from this conversation?"
"Save the key decisions and outcomes from today"
```

### Integration with Other Tools

Bob Shell shares memory with all other MCP clients:

- **Claude Code**: Native plugin with automatic recall/capture
- **Codex CLI**: Native plugin with automatic recall/capture
- **Cursor**: MCP server (manual workflow like Bob Shell)
- **Cline**: MCP server (manual workflow like Bob Shell)
- **Goose**: MCP server (manual workflow like Bob Shell)

Memory saved in Bob Shell is immediately available in Claude Code, Codex CLI, and vice versa.

## Troubleshooting

### MCP Server Not Loading

If agentmemory tools are not available:

1. **Check server is running**:
   ```bash
   curl http://localhost:3111/agentmemory/health
   ```

2. **Verify MCP config syntax**:
   - Check JSON is valid in `~/.bob/mcp_settings.json` or `.bob/mcp.json`
   - Ensure `mcpServers` object exists
   - Verify `agentmemory` entry is properly formatted

3. **Test npx resolution**:
   - Try direct `npx`: `npx -y @agentmemory/mcp`
   - On Windows, try: `cmd /c npx -y @agentmemory/mcp`

4. **Check environment variables**:
   - Ensure `AGENTMEMORY_SECRET` is set if required
   - Verify `AGENTMEMORY_URL` points to running server

### Memory Operations Not Working

If memory tools fail:

1. **Verify server connectivity**: Check health endpoint
2. **Check authentication**: Ensure secret matches server config
3. **Review tool output**: Look for error messages in Bob Shell
4. **Test with curl**: Verify REST API works directly

### Platform-Specific Issues

**Windows**:
- May require `cmd /c npx` instead of direct `npx`
- Environment variable syntax: `%AGENTMEMORY_SECRET%`

**macOS/Linux**:
- Direct `npx` should work
- Environment variable syntax: `${AGENTMEMORY_SECRET}`

## Example Prompts

### Memory Search Examples

```
"Search my memory for TypeScript best practices"
"What do you remember about my coding preferences?"
"Show me recent decisions about database architecture"
"Find information about the authentication system we discussed"
```

### Memory Save Examples

```
"Remember that I prefer async/await over Promise chains"
"Save this decision: we're using PostgreSQL for the main database"
"Observe that I always want tests written for new features"
"Remember my preference for functional programming patterns"
```

### Session Management Examples

```
"Summarize this session and save the key points"
"What were the main decisions we made today?"
"Save this conversation as a session summary"
"Remember the problems we solved and the approaches we used"
```

### Profile and Timeline Examples

```
"Show me my user profile and preferences"
"What's my recent activity timeline?"
"List my saved preferences"
"Show me sessions from the last week"
```

## Notes

- Bob Shell supports both global and project-level MCP configuration
- Project-level `.bob/mcp.json` takes precedence over `~/.bob/mcp_settings.json`
- The same agentmemory server can be shared across Bob Shell, Claude Code, Codex CLI, Cursor, Gemini CLI, OpenClaw, Hermes, and other MCP clients
- Memory operations require manual invocation through natural language requests
- Bob Shell does not support automatic lifecycle hooks or plugin APIs

## See also

- [agentmemory main README](../../README.md)
- [OpenClaw integration](../openclaw/README.md)
- [Hermes integration](../hermes/README.md)
- [pi integration](../pi/README.md)