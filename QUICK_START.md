# Quick Start: ActivityWatch MCP Connection

## ✅ Setup Complete!

The ActivityWatch MCP server has been configured and is ready to use.

## Next Step: Restart Claude Desktop

**IMPORTANT:** You must restart Claude Desktop for the connection to work.

1. **Quit Claude Desktop completely** (⌘Q or File → Quit)
2. **Reopen Claude Desktop**
3. The ActivityWatch MCP server will be available

## Verify It's Working

After restarting, try asking Claude:

> "What ActivityWatch buckets do I have?"

If Claude can list your buckets, the connection is working! 🎉

## Quick Test Queries

Once connected, try these:

- "What applications have I used the most today?"
- "Show me my ActivityWatch settings"
- "What websites have I spent the most time on today?"

## Configuration Location

The MCP server is configured at:
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

## Need Help?

- **Setup details:** See [SETUP_GUIDE.md](./SETUP_GUIDE.md)
- **Usage examples:** See [USAGE_EXAMPLES.md](./USAGE_EXAMPLES.md)
- **Troubleshooting:** Check the setup guide's troubleshooting section

## Requirements

- ✅ ActivityWatch must be running (default: `http://localhost:5600`)
- ✅ Node.js must be installed
- ✅ Claude Desktop must be restarted after configuration
