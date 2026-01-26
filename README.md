# ActivityWatch MCP Integration Project

## Overview

This project explores the integration of existing ActivityWatch MCP (Model Context Protocol) servers with various AI tools and platforms. The goal is to evaluate, configure, and demonstrate how ActivityWatch's time tracking capabilities can be leveraged through MCP to enhance AI assistant functionality.

## Project Goals

- Evaluate existing ActivityWatch MCP server implementations
- Configure MCP servers for integration with AI tools (Claude, ChatGPT, etc.)
- Test and document integration patterns
- Identify potential enhancements or customizations needed
- Create examples and documentation for using ActivityWatch data with AI assistants

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
├── docs/              # Documentation and research
├── examples/          # Integration examples
├── config/            # Configuration files
└── tests/             # Test scripts and validation
```

## Getting Started

1. Ensure ActivityWatch is installed and running
2. Install the MCP server of choice
3. Configure the MCP server for your AI tool
4. Test the integration

## Resources

- [ActivityWatch Documentation](https://docs.activitywatch.net/)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)
- [ActivityWatch MCP Server (Node.js)](https://github.com/8bitgentleman/activitywatch-mcp-server)
- [ActivityWatch MCP Server (Swift)](https://github.com/doozMen/activitywatch-mcp)

## License

TBD - To be determined based on project direction
