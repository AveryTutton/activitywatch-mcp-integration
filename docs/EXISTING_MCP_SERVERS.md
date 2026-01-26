# Existing ActivityWatch MCP Servers

## Node.js Implementation

**Repository**: [8bitgentleman/activitywatch-mcp-server](https://github.com/8bitgentleman/activitywatch-mcp-server)

### Features
- List available ActivityWatch buckets
- Retrieve raw events from buckets
- Execute AQL (ActivityWatch Query Language) queries
- Access ActivityWatch configuration settings

### Installation
```bash
npm install -g activitywatch-mcp-server
# or
git clone https://github.com/8bitgentleman/activitywatch-mcp-server
cd activitywatch-mcp-server
npm install
```

### Configuration
- Requires ActivityWatch to be running
- Default connection: `http://localhost:5600`
- MCP server runs on stdio transport

### MCP Tools Provided
1. `list_buckets` - List all available buckets
2. `get_events` - Retrieve events from a bucket
3. `query_aql` - Execute AQL queries
4. `get_config` - Get ActivityWatch configuration

## Swift Implementation

**Repository**: [doozMen/activitywatch-mcp](https://github.com/doozMen/activitywatch-mcp)

### Features
- Similar functionality to Node.js version
- Swift-based implementation
- Alternative for Swift-native environments

### Installation
```bash
git clone https://github.com/doozMen/activitywatch-mcp
cd activitywatch-mcp
# Follow Swift build instructions
```

## Comparison

| Feature | Node.js | Swift |
|---------|---------|-------|
| Language | TypeScript/Node.js | Swift |
| Maintenance | Active | Active |
| Community | 38+ stars | Smaller |
| Platform | Cross-platform | macOS/iOS focused |
| Setup | npm install | Swift build |

## Integration Points

Both servers implement the MCP protocol and can be integrated with:
- Claude (via Cursor, Anthropic API)
- ChatGPT (via plugins/extensions)
- Other MCP-compatible AI tools

## Notes

- Both are open-source (MIT license)
- Both require ActivityWatch to be running locally
- Data remains private and local (no cloud sync)
- Standard MCP protocol ensures compatibility
