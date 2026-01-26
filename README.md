# ActivityWatch MCP Integration Project

## Overview

This project explores the integration of existing ActivityWatch MCP (Model Context Protocol) servers with various AI tools and platforms. The goal is to evaluate, configure, and demonstrate how ActivityWatch's time tracking capabilities can be leveraged through MCP to enhance AI assistant functionality, **specifically enabling continuous data access** for Claude and other AI tools.

## Project Goals

- ✅ Evaluate existing ActivityWatch MCP server implementations
- ✅ Analyze current capabilities and limitations
- 🎯 **Enable continuous data access** (not just on-demand queries)
- 🎯 Configure MCP servers for integration with Claude via MCP connectors
- 🎯 Test and document integration patterns
- 🎯 Identify and implement enhancements needed for continuous data flow
- 🎯 Create examples and documentation for using ActivityWatch data with AI assistants

## Key Finding

**The existing MCP server provides on-demand query capabilities but does NOT provide continuous data access.** To enable Claude to have persistent awareness of ActivityWatch data, we need to:

1. **Add MCP Resources support** (2-4 days for basic, 5-8 days for production-ready)
2. **Implement background polling** to keep data fresh
3. **Create data aggregation** for summaries and insights

See [Findings Summary](./docs/FINDINGS_SUMMARY.md) for details.

## Existing MCP Server Implementations

### Node.js Implementation
- **Repository**: [8bitgentleman/activitywatch-mcp-server](https://github.com/8bitgentleman/activitywatch-mcp-server)
- **Language**: Node.js/TypeScript
- **Status**: Active (38+ stars on GitHub)

### Swift Implementation
- **Repository**: [doozMen/activitywatch-mcp](https://github.com/doozMen/activitywatch-mcp)
- **Language**: Swift
- **Status**: Alternative implementation

## ActivityWatch MCP Capabilities

The MCP server provides access to:
- List available ActivityWatch buckets
- Retrieve raw events from buckets
- Execute AQL (ActivityWatch Query Language) queries
- Access ActivityWatch configuration settings

## Use Cases

- Summarizing daily application usage and browsing history
- Analyzing personal productivity habits through natural language queries
- Gaining insights into time spent on specific tasks or projects
- Automating time tracking analysis and reporting

## Project Structure

```
.
├── README.md
├── docs/                      # Documentation and research
│   ├── FINDINGS_SUMMARY.md     # Quick summary of findings
│   ├── ANALYSIS_CONTINUOUS_DATA_ACCESS.md  # Detailed analysis
│   ├── IMPLEMENTATION_PLAN.md  # Step-by-step implementation plan
│   ├── EXISTING_MCP_SERVERS.md # Research on existing servers
│   └── PROJECT_GOALS.md        # Project roadmap
├── existing-mcp-server/        # Cloned existing MCP server for analysis
├── examples/                   # Integration examples
├── config/                     # Configuration files
└── tests/                      # Test scripts and validation
```

## Documentation

- **[Findings Summary](./docs/FINDINGS_SUMMARY.md)** - Quick answer to "what effort is needed?"
- **[Detailed Analysis](./docs/ANALYSIS_CONTINUOUS_DATA_ACCESS.md)** - Comprehensive analysis of continuous data access
- **[Implementation Plan](./docs/IMPLEMENTATION_PLAN.md)** - Step-by-step plan for implementation
- **[Existing MCP Servers](./docs/EXISTING_MCP_SERVERS.md)** - Research on available implementations

## Getting Started

### Current Status

✅ **Analysis Complete** - We've analyzed the existing MCP server and identified what's needed for continuous data access.

🎯 **Next Steps** - Implementation of MCP Resources support for continuous data access.

### For Development

1. Ensure ActivityWatch is installed and running locally
2. Review the [Implementation Plan](./docs/IMPLEMENTATION_PLAN.md)
3. Set up development environment with the existing MCP server
4. Begin implementing MCP Resources support

## Resources

- [ActivityWatch Documentation](https://docs.activitywatch.net/)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)
- [ActivityWatch MCP Server (Node.js)](https://github.com/8bitgentleman/activitywatch-mcp-server)
- [ActivityWatch MCP Server (Swift)](https://github.com/doozMen/activitywatch-mcp)

## License

TBD - To be determined based on project direction
