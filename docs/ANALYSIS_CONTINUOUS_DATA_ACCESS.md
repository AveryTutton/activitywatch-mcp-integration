# Analysis: Continuous ActivityWatch Data Access via MCP

## Executive Summary

The existing ActivityWatch MCP server (by 8bitgentleman) provides **on-demand query capabilities** but does **not** provide continuous data access. This document analyzes what would be required to enable continuous data access for Claude and other AI tools.

## Current Implementation Analysis

### How the Existing MCP Server Works

The existing server implements **MCP Tools** (on-demand functions):

1. **`activitywatch_list_buckets`** - Lists available buckets
2. **`activitywatch_run_query`** - Executes AQL queries on-demand
3. **`activitywatch_get_events`** - Retrieves raw events from buckets
4. **`activitywatch_get_settings`** - Gets ActivityWatch configuration
5. **`activitywatch_query_examples`** - Provides query examples

**Architecture:**
- Connects to ActivityWatch API at `http://localhost:5600/api/0`
- Uses **stdio transport** for MCP communication
- Data is fetched **only when Claude requests it** via tool calls
- No background data synchronization or caching

### Current Data Flow

```
ActivityWatch (local) → API (localhost:5600) → MCP Server → Claude (on-demand queries)
```

**Limitations:**
- ❌ Data is only available when explicitly queried
- ❌ No automatic updates or synchronization
- ❌ Claude must know what to ask for
- ❌ No persistent context of recent activity
- ❌ No proactive data availability

## The Goal: Continuous Data Access

### What "Continuous Data Access" Means

For your use case (personal productivity + leadership visibility), you need:

1. **Recent Activity Context**: Claude should have awareness of recent work without explicit queries
2. **Automatic Updates**: New ActivityWatch data should become available to Claude automatically
3. **Persistent Context**: Historical data should be accessible for analysis and documentation
4. **Proactive Insights**: Claude can suggest productivity patterns without being asked

### MCP Protocol Capabilities

The MCP protocol supports two mechanisms for data access:

#### 1. **Tools** (Current Implementation)
- On-demand function calls
- Claude requests data when needed
- ✅ Already implemented
- ❌ Not suitable for continuous access

#### 2. **Resources** (Not Currently Used)
- Persistent data sources that Claude can access
- Can be listed, read, and watched for changes
- ✅ Suitable for continuous access
- ❌ Not implemented in existing server

## Solution Approaches

### Approach 1: MCP Resources (Recommended)

**Concept:** Expose ActivityWatch data as MCP Resources that Claude can access and monitor.

**Implementation:**
- Create MCP Resources for:
  - Recent activity summaries (last 24 hours, last week)
  - Current session data
  - Daily/weekly productivity summaries
  - Application usage statistics
- Resources can be "watched" for changes
- Claude can read resources proactively

**Effort Estimate:**
- **Low-Medium** (2-4 days)
- Extend existing server with Resources support
- Add periodic data aggregation/formatting
- Implement resource watching/polling

**Pros:**
- ✅ Native MCP pattern for continuous data
- ✅ Claude can proactively access resources
- ✅ Supports change notifications
- ✅ Clean separation of concerns

**Cons:**
- ⚠️ Requires understanding MCP Resources API
- ⚠️ Need to define resource structure/schema

### Approach 2: Background Polling + Caching

**Concept:** MCP server polls ActivityWatch in background and caches recent data.

**Implementation:**
- Background worker polls ActivityWatch API every N minutes
- Caches recent events, summaries, statistics
- Tools return cached data (faster) or fresh data
- Add new tools like `get_recent_activity`, `get_today_summary`

**Effort Estimate:**
- **Medium** (3-5 days)
- Add background polling mechanism
- Implement caching layer
- Create summary/aggregation functions
- Handle cache invalidation

**Pros:**
- ✅ Faster response times
- ✅ Can provide "recent activity" context
- ✅ Works with existing Tools pattern

**Cons:**
- ⚠️ Still requires explicit tool calls
- ⚠️ Cache management complexity
- ⚠️ Not truly "continuous" from Claude's perspective

### Approach 3: File System Watching

**Concept:** Monitor ActivityWatch's local database files for changes.

**Implementation:**
- ActivityWatch stores data in SQLite databases (typically `~/.local/share/activitywatch/`)
- Watch database files for changes
- Parse and expose new events via MCP
- Could use Resources or enhanced Tools

**Effort Estimate:**
- **Medium-High** (5-7 days)
- Requires understanding ActivityWatch database schema
- File watching implementation
- SQLite parsing/querying
- More complex than API approach

**Pros:**
- ✅ Direct access to raw data
- ✅ Can detect changes immediately
- ✅ No dependency on ActivityWatch API

**Cons:**
- ⚠️ More complex implementation
- ⚠️ Database schema may change
- ⚠️ Platform-specific file watching
- ⚠️ Requires database access permissions

### Approach 4: Hybrid Approach (Recommended for Production)

**Concept:** Combine MCP Resources + Background Polling + Smart Caching

**Implementation:**
- Use MCP Resources for structured summaries (daily/weekly reports)
- Background polling for recent activity (last hour, current session)
- Caching for frequently accessed data
- Tools for on-demand complex queries

**Effort Estimate:**
- **Medium-High** (5-8 days)
- Combines benefits of multiple approaches
- More robust and feature-complete

**Pros:**
- ✅ Best of all worlds
- ✅ Flexible and extensible
- ✅ Supports both proactive and reactive access

**Cons:**
- ⚠️ More complex to implement
- ⚠️ More code to maintain

## Recommended Implementation Plan

### Phase 1: MCP Resources Support (Week 1)

**Goal:** Enable Claude to access ActivityWatch data as persistent resources

**Tasks:**
1. Study MCP Resources API specification
2. Design resource structure:
   - `activitywatch://recent/24h` - Last 24 hours summary
   - `activitywatch://recent/today` - Today's activity
   - `activitywatch://recent/week` - This week's summary
   - `activitywatch://current/session` - Current active session
3. Implement resource listing and reading
4. Add background polling (every 5-15 minutes)
5. Format data for Claude consumption

**Deliverables:**
- Extended MCP server with Resources support
- Documentation on resource structure
- Test with Claude Desktop

### Phase 2: Enhanced Context & Summaries (Week 2)

**Goal:** Provide rich, contextual summaries for leadership visibility

**Tasks:**
1. Create aggregation functions:
   - Daily productivity summary
   - Application usage breakdown
   - Task/project time allocation
   - Productivity patterns
2. Add smart formatting for human-readable reports
3. Implement time-based resource updates
4. Add configuration for update frequency

**Deliverables:**
- Rich summary resources
- Configuration options
- Example outputs

### Phase 3: Advanced Features (Week 3+)

**Goal:** Enhanced capabilities for documentation and insights

**Tasks:**
1. Natural language query interface
2. Trend analysis and insights
3. Export capabilities (for reports)
4. Custom resource types based on use cases

## Technical Considerations

### ActivityWatch API Limitations

- API is REST-based, requires polling
- No webhooks or push notifications
- Rate limiting considerations (if any)
- Local-only (localhost:5600)

### MCP Protocol Considerations

- Resources must be URI-addressable
- Resources can have metadata (title, description, mimeType)
- Resources can be watched for changes (if supported by client)
- Claude Desktop supports Resources ✅

### Performance Considerations

- Polling frequency balance (freshness vs. overhead)
- Data aggregation efficiency
- Response size limits
- Caching strategy

## Effort Summary

| Approach | Effort | Complexity | Continuous Access | Recommended |
|----------|--------|------------|-------------------|-------------|
| MCP Resources | 2-4 days | Low-Medium | ✅ Yes | ✅ **Yes** |
| Background Polling | 3-5 days | Medium | ⚠️ Partial | Maybe |
| File Watching | 5-7 days | Medium-High | ✅ Yes | No |
| Hybrid | 5-8 days | High | ✅ Yes | ✅ **Yes** |

## Next Steps

1. **Decision Point:** Choose implementation approach
   - **Quick Start:** MCP Resources (Approach 1)
   - **Production Ready:** Hybrid (Approach 4)

2. **Proof of Concept:**
   - Implement basic Resources support
   - Test with Claude Desktop
   - Validate data flow and usability

3. **Iterative Development:**
   - Start with simple resources (today's activity)
   - Add more sophisticated summaries
   - Refine based on usage

## Questions to Answer

1. **Update Frequency:** How often should data refresh? (5 min, 15 min, hourly?)
2. **Data Granularity:** What level of detail? (raw events, summaries, both?)
3. **Historical Depth:** How far back should be accessible? (24h, week, month?)
4. **Use Cases Priority:** Which use cases are most important?
   - Real-time activity awareness
   - Daily/weekly summaries
   - Productivity analysis
   - Documentation assistance
5. **Leadership Reports:** What format for leadership visibility? (structured summaries, natural language, both?)

## Conclusion

The existing MCP server provides a solid foundation but needs enhancement for continuous data access. **MCP Resources** is the recommended approach as it:
- Aligns with MCP protocol best practices
- Enables proactive data access by Claude
- Requires moderate effort (2-4 days for basic implementation)
- Provides a path for future enhancements

The hybrid approach (Resources + Polling + Caching) would provide the most robust solution for production use but requires more development time.
