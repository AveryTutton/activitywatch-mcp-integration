# ActivityWatch MCP Usage Examples

## Quick Start

Once the MCP server is configured and Claude Desktop is restarted, you can start using ActivityWatch data in Claude!

## Basic Usage Examples

### 1. List Your Buckets

**Ask Claude:**
> "What ActivityWatch buckets do I have?"

Claude will use the `activitywatch_list_buckets` tool to show you all available buckets.

### 2. Get Query Examples

**Ask Claude:**
> "Show me examples of ActivityWatch queries I can run"

Claude will use the `activitywatch_query_examples` tool to provide formatted query examples.

### 3. View Today's Activity

**Ask Claude:**
> "What applications have I used the most today?"

Claude will need to:
1. Get your buckets
2. Run a query to get today's window events
3. Analyze and summarize the results

### 4. Check Settings

**Ask Claude:**
> "What are my ActivityWatch settings?"

Claude will use the `activitywatch_get_settings` tool.

## Advanced Query Examples

### Get Window Events for Today

**Ask Claude:**
> "Run an ActivityWatch query to get my window events from today. Use timeperiods for today's date and query the window watcher bucket."

Claude should format the query like:
```json
{
  "timeperiods": ["2024-01-26/2024-01-27"],
  "query": ["window_events = query_bucket(find_bucket('aw-watcher-window_')); RETURN = window_events;"]
}
```

### Get Active Time (Excluding AFK)

**Ask Claude:**
> "Show me my active window events from today, excluding time when I was away from keyboard."

Claude should create a query like:
```json
{
  "timeperiods": ["2024-01-26/2024-01-27"],
  "query": ["window_events = query_bucket(find_bucket('aw-watcher-window_')); afk_events = query_bucket(find_bucket('aw-watcher-afk_')); not_afk = filter_keyvals(afk_events, 'status', ['not-afk']); active_events = filter_period_intersect(window_events, not_afk); RETURN = active_events;"]
}
```

### Group Events by Application

**Ask Claude:**
> "Show me my application usage grouped by app name for today, sorted by duration."

Claude should create:
```json
{
  "timeperiods": ["2024-01-26/2024-01-27"],
  "query": ["window_events = query_bucket(find_bucket('aw-watcher-window_')); events_by_app = merge_events_by_keys(window_events, ['app']); RETURN = sort_by_duration(events_by_app);"]
}
```

### Filter by Specific Application

**Ask Claude:**
> "How much time did I spend in VS Code today?"

Claude should create:
```json
{
  "timeperiods": ["2024-01-26/2024-01-27"],
  "query": ["window_events = query_bucket(find_bucket('aw-watcher-window_')); code_events = filter_keyvals(window_events, 'app', ['Code']); RETURN = code_events;"]
}
```

## Productivity Analysis Examples

### Daily Summary

**Ask Claude:**
> "Can you analyze my ActivityWatch data from today and give me a productivity summary? Include top applications, total active time, and any insights."

### Weekly Review

**Ask Claude:**
> "Show me a weekly summary of my ActivityWatch data. What were my top applications this week and how did my productivity patterns look?"

### Time Tracking for Projects

**Ask Claude:**
> "Based on my ActivityWatch data, how much time have I spent on coding-related activities this week? Look at applications like VS Code, terminal, and browser with coding-related tabs."

## Tips for Best Results

### 1. Be Specific About Dates

When asking for time-based queries, specify the date range:
- "from today"
- "from the last 7 days"
- "from January 26, 2024"

### 2. Use Natural Language

Claude will translate your requests into the appropriate ActivityWatch queries. You don't need to know AQL syntax.

### 3. Ask for Summaries

Instead of raw data, ask Claude to analyze and summarize:
- ❌ "Get all events from bucket X"
- ✅ "What were my top 5 applications today?"

### 4. Request Examples First

If you're unsure about query format:
> "Show me examples of ActivityWatch queries first, then run one for today's activity"

### 5. Iterate and Refine

Start with simple queries and build up:
1. First: "What buckets do I have?"
2. Then: "Show me today's window events"
3. Finally: "Analyze my productivity patterns"

## Common Query Patterns

### Pattern 1: Simple Event Retrieval
```
events = query_bucket('bucket-id');
RETURN = events;
```

### Pattern 2: Filter by Key-Value
```
events = query_bucket('bucket-id');
filtered = filter_keyvals(events, 'app', ['AppName']);
RETURN = filtered;
```

### Pattern 3: Exclude AFK Time
```
window_events = query_bucket(find_bucket('aw-watcher-window_'));
afk_events = query_bucket(find_bucket('aw-watcher-afk_'));
not_afk = filter_keyvals(afk_events, 'status', ['not-afk']);
active = filter_period_intersect(window_events, not_afk);
RETURN = active;
```

### Pattern 4: Group and Sort
```
events = query_bucket('bucket-id');
grouped = merge_events_by_keys(events, ['app']);
sorted = sort_by_duration(grouped);
RETURN = sorted;
```

## Troubleshooting Queries

### If Claude Gets Query Format Errors

**Be explicit about format:**
> "Run this ActivityWatch query with timeperiods as ['2024-01-26/2024-01-27'] and query as a single string with all statements separated by semicolons: 'events = query_bucket(find_bucket(\"aw-watcher-window_\")); RETURN = events;'"

### If Results Are Empty

1. Check that ActivityWatch has data for that time period
2. Verify the bucket ID is correct
3. Try a broader time range

### If You Get Connection Errors

1. Verify ActivityWatch is running: `curl http://localhost:5600/api/0/buckets`
2. Check Claude Desktop MCP connection status
3. Restart Claude Desktop if needed

## Advanced: Custom Queries

Once you're comfortable, you can ask Claude to create custom queries:

> "Create an ActivityWatch query that shows me all time I spent in communication apps (Slack, Messages, Email) this week, excluding AFK time, grouped by application and sorted by total duration."

Claude will construct the appropriate AQL query and execute it.
