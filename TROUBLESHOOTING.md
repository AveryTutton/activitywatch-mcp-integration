# Troubleshooting: ActivityWatch MCP Connection

## Issue: Connector Shows "LOCAL DEV" but "Tool permissions" Is Empty

### Symptom
In Claude Desktop, the ActivityWatch connector appears under Connectors with a "Configure" button. When you click Configure, you see "Tool permissions" and the description "Choose when Claude is allowed to use these tools," but **no toggles or tools are listed**.

### Cause
This usually means the MCP server **failed to start**. Claude Desktop loads the connector from config but cannot run the server or discover its tools. Common causes:

1. **MCP server not built** – The `dist/index.js` file does not exist (you cloned the repo but never ran `npm run build`).
2. **Wrong path in config** – The path in `claude_desktop_config.json` does not point to the built `dist/index.js`.
3. **Node.js not found** – Claude Desktop cannot run `node` (e.g. PATH differs when launched from the GUI).

### Solution

#### Step 1: Build the MCP server (required after clone)

From the project root:

```bash
cd /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server
npm install
npm run build
```

Verify the build:

```bash
ls -la /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server/dist/index.js
```

You should see the file. If not, the connector will never show tools.

#### Step 2: Check Claude Desktop config

Config file (macOS):

```bash
cat ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

It should contain something like:

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

The path in `args` must be the **full path** to `dist/index.js` and the file must exist.

#### Step 3: Restart Claude Desktop fully

1. Quit Claude Desktop completely (⌘Q).
2. Reopen Claude Desktop.
3. Open Configure for the ActivityWatch connector again – you should now see the five tools listed under Tool permissions (e.g. `activitywatch_list_buckets`, `activitywatch_run_query`, etc.).

#### Step 4: If tools still don’t appear

- Ensure **ActivityWatch is running** (see “ActivityWatch Not Running” below). Some MCP servers delay or fail tool discovery if the backend is unreachable.
- Check **Claude Desktop logs** (or system logs) for errors when starting the MCP server.
- From Terminal, confirm Node can run the server:

  ```bash
  node /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server/dist/index.js
  ```
  It should run and wait on stdin (no immediate error). Press Ctrl+C to stop.

---

## Issue: ActivityWatch Not Running

### Symptom
Claude reports: "ActivityWatch isn't currently running or there's a connection issue. The MCP server is trying to connect to http://localhost:5600 but can't reach it."

### Solution

#### Step 1: Start ActivityWatch

**Option A: From Applications**
1. Open Finder
2. Go to Applications
3. Double-click `ActivityWatch.app`
4. Wait a few seconds for it to start

**Option B: From Terminal**
```bash
open -a ActivityWatch
```

#### Step 2: Verify ActivityWatch is Running

Check if the API is accessible:
```bash
curl http://localhost:5600/api/0/buckets
```

If you get JSON data back, ActivityWatch is running! ✅

If you get a connection error, wait a few more seconds and try again.

#### Step 3: Check System Tray

Look for the ActivityWatch icon in your macOS menu bar (top right). If you see it, ActivityWatch is running.

### Verify Connection in Claude

After starting ActivityWatch, try asking Claude again:
> "What ActivityWatch buckets do I have?"

## Common Issues

### ActivityWatch Starts But API Not Accessible

**Check the port:**
```bash
lsof -i :5600
```

If nothing is listening, ActivityWatch might be using a different port. Check ActivityWatch settings.

**Check ActivityWatch logs:**
- Look in `~/Library/Application Support/activitywatch/` for log files
- Check the ActivityWatch menu bar icon → Settings → Logs

### ActivityWatch Won't Start

1. **Check if it's already running:**
   ```bash
   ps aux | grep -i activitywatch
   ```

2. **Try restarting:**
   - Quit ActivityWatch completely (right-click menu bar icon → Quit)
   - Wait a few seconds
   - Start it again

3. **Check permissions:**
   - ActivityWatch needs accessibility permissions on macOS
   - System Settings → Privacy & Security → Accessibility
   - Make sure ActivityWatch is enabled

### MCP Server Connection Issues

If ActivityWatch is running but Claude still can't connect:

1. **Verify the MCP server path is correct:**
   ```bash
   ls -la /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server/dist/index.js
   ```

2. **Test the MCP server directly:**
   ```bash
   cd /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server
   node dist/index.js
   ```
   (This will run in the foreground - you can Ctrl+C to stop it)

3. **Check Claude Desktop logs:**
   - Look for error messages in Claude Desktop
   - Check Console.app for system logs

### Port Already in Use

If port 5600 is already in use by something else:

1. **Find what's using it:**
   ```bash
   lsof -i :5600
   ```

2. **Check ActivityWatch settings:**
   - ActivityWatch might be configured to use a different port
   - Check ActivityWatch → Settings → Server

3. **Update MCP server code** (if needed):
   - The server is hardcoded to `http://localhost:5600`
   - You'd need to modify the code to use a different port

## Setting Up Autostart (Recommended)

To ensure ActivityWatch starts automatically when you log in:

1. **System Settings → General → Login Items**
2. Click the `+` button
3. Navigate to `/Applications/ActivityWatch.app`
4. Add it to Login Items

This way, ActivityWatch will always be running when you need it.

## Quick Health Check

If the connector shows but has **no tools** (empty Tool permissions), run the build step first – see the “Connector shows LOCAL DEV but Tool permissions is empty” section above.

Run this command to check everything:
```bash
# Check if ActivityWatch is running
curl -s http://localhost:5600/api/0/buckets > /dev/null && echo "✅ ActivityWatch is running" || echo "❌ ActivityWatch is not running"

# Check if Node.js is available
which node > /dev/null && echo "✅ Node.js is installed" || echo "❌ Node.js not found"

# Check if MCP server file exists
test -f /Users/avery.tutton/Code/activitywatch-mcp-integration/existing-mcp-server/dist/index.js && echo "✅ MCP server file exists" || echo "❌ MCP server file not found"
```

## Still Having Issues?

1. **Restart everything:**
   - Quit ActivityWatch
   - Quit Claude Desktop
   - Start ActivityWatch
   - Start Claude Desktop
   - Try again

2. **Check ActivityWatch version:**
   - Make sure you're running a recent version
   - Update if needed from [ActivityWatch releases](https://github.com/ActivityWatch/activitywatch/releases)

3. **Verify ActivityWatch is collecting data:**
   - Open ActivityWatch web UI: http://localhost:5600
   - Check if you see buckets and events
   - If no data, ActivityWatch might need time to collect

4. **Check system permissions:**
   - ActivityWatch needs screen recording and accessibility permissions
   - System Settings → Privacy & Security → Screen Recording
   - System Settings → Privacy & Security → Accessibility
