# Implementation Plan: Continuous ActivityWatch Data Access

## Overview

This document outlines the step-by-step plan to implement continuous ActivityWatch data access via MCP Resources, enabling Claude to have persistent awareness of your activity data.

## Phase 1: Foundation & Research (Days 1-2)

### Day 1: MCP Resources Deep Dive

**Tasks:**
- [ ] Study MCP Resources specification
  - Resource listing
  - Resource reading
  - Resource watching (if supported)
  - URI schemes and structure
- [ ] Review Claude Desktop MCP Resources support
- [ ] Examine existing MCP servers that use Resources
- [ ] Document resource design patterns

**Deliverables:**
- Research notes on MCP Resources
- Resource structure design document
- Examples of resource implementations

### Day 2: ActivityWatch Data Analysis

**Tasks:**
- [ ] Analyze ActivityWatch API endpoints
  - Event structure
  - Bucket types and data
  - Query capabilities
  - Rate limits/constraints
- [ ] Test API locally
- [ ] Identify data aggregation needs
- [ ] Design data transformation pipeline

**Deliverables:**
- API analysis document
- Sample data structures
- Aggregation requirements

## Phase 2: Core Implementation (Days 3-5)

### Day 3: MCP Resources Infrastructure

**Tasks:**
- [ ] Extend existing MCP server with Resources support
  - Add Resources capability to server config
  - Implement `ListResources` handler
  - Implement `ReadResource` handler
- [ ] Define resource URI scheme
  - `activitywatch://recent/24h`
  - `activitywatch://recent/today`
  - `activitywatch://recent/week`
  - `activitywatch://current/session`
- [ ] Create resource metadata structure
- [ ] Basic resource reading (fetch from API on-demand)

**Deliverables:**
- Extended MCP server with Resources support
- Resource URI scheme documentation
- Basic resource reading working

### Day 4: Background Polling & Caching

**Tasks:**
- [ ] Implement background polling mechanism
  - Configurable interval (default: 15 minutes)
  - Error handling and retry logic
  - Graceful shutdown
- [ ] Create caching layer
  - In-memory cache for recent data
  - Cache invalidation strategy
  - Cache size management
- [ ] Integrate polling with Resources
  - Update resources when new data arrives
  - Maintain resource freshness

**Deliverables:**
- Background polling system
- Caching implementation
- Resources updated from cache

### Day 5: Data Aggregation & Formatting

**Tasks:**
- [ ] Implement aggregation functions
  - Last 24 hours summary
  - Today's activity breakdown
  - Weekly summary
  - Current session tracking
- [ ] Create human-readable formatting
  - Natural language summaries
  - Structured data (JSON)
  - Both formats available
- [ ] Add configuration options
  - Polling interval
  - Cache TTL
  - Resource update triggers

**Deliverables:**
- Aggregation functions
- Formatted resource outputs
- Configuration system

## Phase 3: Testing & Integration (Days 6-7)

### Day 6: Local Testing

**Tasks:**
- [ ] Test MCP server locally
  - Verify Resources are listed correctly
  - Test resource reading
  - Validate data format
  - Check error handling
- [ ] Test with Claude Desktop
  - Configure MCP server
  - Verify Claude can access resources
  - Test resource updates
  - Validate data accuracy
- [ ] Performance testing
  - Polling overhead
  - Cache efficiency
  - Response times

**Deliverables:**
- Working local setup
- Test results and documentation
- Performance metrics

### Day 7: Documentation & Examples

**Tasks:**
- [ ] Write setup guide
  - Installation instructions
  - Configuration options
  - Claude Desktop setup
- [ ] Create usage examples
  - Common queries
  - Resource access patterns
  - Integration examples
- [ ] Document resource structure
  - URI scheme
  - Data formats
  - Update frequency

**Deliverables:**
- Complete documentation
- Usage examples
- Setup guide

## Phase 4: Enhancement (Days 8+)

### Advanced Features

**Tasks:**
- [ ] Add more resource types
  - Project-based summaries
  - Application usage trends
  - Productivity insights
- [ ] Implement resource watching (if supported)
- [ ] Add export capabilities
- [ ] Create leadership report format
- [ ] Natural language query interface

**Deliverables:**
- Enhanced resource types
- Advanced features
- Production-ready implementation

## Technical Stack

### Existing Foundation
- TypeScript/Node.js
- MCP SDK (`@modelcontextprotocol/sdk`)
- Axios for HTTP requests
- ActivityWatch API (localhost:5600)

### New Components Needed
- Background polling mechanism (setInterval or node-cron)
- In-memory caching (Map or LRU cache)
- Data aggregation utilities
- Resource formatting functions

## Resource Design

### Resource URI Scheme

```
activitywatch://recent/24h          # Last 24 hours summary
activitywatch://recent/today         # Today's activity
activitywatch://recent/week          # This week's summary
activitywatch://recent/month         # This month's summary
activitywatch://current/session      # Current active session
activitywatch://apps/top             # Top applications
activitywatch://productivity/today   # Productivity metrics
```

### Resource Structure

```typescript
interface ActivityWatchResource {
  uri: string;
  name: string;
  description: string;
  mimeType: "application/json" | "text/plain";
  // Content includes:
  // - Summary statistics
  // - Time breakdowns
  // - Application usage
  // - Productivity metrics
  // - Human-readable description
}
```

### Example Resource Content

```json
{
  "period": "2024-01-26",
  "summary": {
    "totalActiveTime": "8h 32m",
    "topApplications": [
      { "app": "Code", "duration": "3h 15m", "percentage": 37.5 },
      { "app": "Chrome", "duration": "2h 45m", "percentage": 32.1 }
    ],
    "productivityScore": 0.78,
    "focusTime": "6h 12m"
  },
  "humanReadable": "Today you spent 8 hours and 32 minutes actively working. Your top applications were Code (3h 15m) and Chrome (2h 45m). You maintained good focus with 6 hours and 12 minutes of focused work time.",
  "rawData": { /* detailed event data */ }
}
```

## Configuration

### Server Configuration

```json
{
  "mcpServers": {
    "activitywatch": {
      "command": "node",
      "args": ["/path/to/activitywatch-mcp-server/dist/index.js"],
      "env": {
        "ACTIVITYWATCH_URL": "http://localhost:5600",
        "POLL_INTERVAL_MINUTES": "15",
        "CACHE_TTL_MINUTES": "30",
        "ENABLE_RESOURCES": "true"
      }
    }
  }
}
```

## Success Criteria

### Phase 1 Success
- ✅ MCP Resources are listed in Claude Desktop
- ✅ Resources can be read successfully
- ✅ Data is accurate and up-to-date

### Phase 2 Success
- ✅ Background polling works reliably
- ✅ Resources update automatically
- ✅ Caching improves performance

### Phase 3 Success
- ✅ Claude can access recent activity without explicit queries
- ✅ Data is formatted for easy consumption
- ✅ Setup is straightforward

### Phase 4 Success
- ✅ Leadership reports are generated
- ✅ Productivity insights are available
- ✅ Documentation assistance works well

## Risk Mitigation

### Technical Risks
- **MCP Resources API changes**: Monitor MCP spec updates
- **ActivityWatch API changes**: Version pinning, error handling
- **Performance issues**: Caching, optimization, monitoring

### Integration Risks
- **Claude Desktop compatibility**: Test early and often
- **Configuration complexity**: Provide clear docs and examples
- **Data privacy**: Ensure local-only operation

## Timeline Summary

| Phase | Duration | Key Deliverables |
|-------|----------|------------------|
| Phase 1: Foundation | 2 days | Research, design |
| Phase 2: Core Implementation | 3 days | Working Resources |
| Phase 3: Testing | 2 days | Tested, documented |
| Phase 4: Enhancement | Ongoing | Advanced features |

**Total for MVP: 7 days**
**Total for Production: 10-14 days**

## Next Immediate Steps

1. **Start with MCP Resources research** (Day 1)
2. **Set up development environment** with existing server
3. **Create proof-of-concept** resource implementation
4. **Test with Claude Desktop** early and iterate
