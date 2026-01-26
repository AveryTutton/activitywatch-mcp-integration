# ActivityWatch MCP Server Setup Guide

## ✅ Setup Complete!

The ActivityWatch MCP server has been configured in your Claude Desktop application.

## Configuration Details

The MCP server has been added to your Claude Desktop configuration at:
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Configuration added:**
```json
{
  "mcpServers": {
    "activitywatch": {
      "command": "node",
      "args": ["/Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server/dist/index.js"]
    }
  }
}
```

## Next Steps

### 1. Restart Claude Desktop

**Important:** You must restart Claude Desktop for the MCP server configuration to take effect.

1. Quit Claude Desktop completely (⌘Q)
2. Reopen Claude Desktop
3. The ActivityWatch MCP server should now be available

### 2. Verify ActivityWatch is Running

The MCP server connects to ActivityWatch at `http://localhost:5600`. Make sure ActivityWatch is running:

```bash
# Check if ActivityWatch is running
curl http://localhost:5600/api/0/buckets
```

If you get a connection error, start ActivityWatch first.

### 3. Verify MCP Connection in Claude

After restarting Claude Desktop:

1. Look for the MCP icon/indicator in Claude's interface
2. Try asking Claude: "What ActivityWatch buckets do I have?"
3. Claude should be able to use the ActivityWatch tools

## Available Tools

Once connected, Claude has access to these ActivityWatch tools:

1. **`activitywatch_list_buckets`** - List all available ActivityWatch buckets
2. **`activitywatch_query_examples`** - Get examples of properly formatted queries
3. **`activitywatch_run_query`** - Execute AQL (ActivityWatch Query Language) queries
4. **`activitywatch_get_events`** - Get raw events from a specific bucket
5. **`activitywatch_get_settings`** - Get ActivityWatch configuration settings

## Example Queries You Can Ask Claude

### Basic Queries

- "What ActivityWatch buckets do I have?"
- "Show me my ActivityWatch settings"
- "What applications have I used the most today?"
- "Can you show me which websites I've spent the most time on today?"
- "How much time have I spent in productivity apps today?"

### Advanced Queries

- "Run an ActivityWatch query to show me my window events from the last 24 hours"
- "Get events from my window watcher bucket"
- "Show me examples of ActivityWatch queries I can run"

## Troubleshooting

### MCP Server Not Appearing

1. **Check the config file:**
   ```bash
   cat ~/Library/Application Support/Claude/claude_desktop_config.json
   ```
   Verify the `activitywatch` entry is present and the path is correct.

2. **Check Node.js is available:**
   ```bash
   which node
   node --version
   ```
   Should show Node.js is installed.

3. **Verify the MCP server file exists:**
   ```bash
   ls -la /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server/dist/index.js
   ```

4. **Check Claude Desktop logs:**
   - Look for error messages in Claude Desktop
   - Check console output if available

### ActivityWatch Connection Errors

1. **Verify ActivityWatch is running:**
   ```bash
   curl http://localhost:5600/api/0/buckets
   ```

2. **Check ActivityWatch is accessible:**
   - Default URL: `http://localhost:5600`
   - If using a different port, you'd need to modify the MCP server code

3. **Restart ActivityWatch if needed:**
   - Make sure ActivityWatch is collecting data
   - Check ActivityWatch logs for errors

### Query Format Errors

If Claude reports query format errors:

1. Ask Claude to use the `activitywatch_query_examples` tool first
2. Be explicit about the query format when asking Claude to run queries
3. The query format requires all statements in a single string, separated by semicolons

Example of correct format:
```json
{
  "timeperiods": ["2024-01-26/2024-01-27"],
  "query": ["events = query_bucket('bucket-id'); RETURN = events;"]
}
```

## Manual Testing

You can test the MCP server directly:

```bash
cd /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server
node dist/index.js
```

The server will output diagnostic information to stderr. You can test it by sending MCP protocol messages (though this is advanced).

## Configuration File Location

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`

**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

**Linux:** `~/.config/Claude/claude_desktop_config.json`

## Additional Resources

- [ActivityWatch MCP Server Repository](https://github.com/8bitgentleman/activitywatch-mcp-server)
- [ActivityWatch Documentation](https://docs.activitywatch.net/)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)

## Support

If you encounter issues:

1. Check the troubleshooting section above
2. Review Claude Desktop logs
3. Verify ActivityWatch is running and accessible
4. Ensure Node.js is installed and the MCP server file exists
