# Plan: Activity-Based Progress Updates → Notion & Jira

**Goal:** A tool that **watches what you're working on** (with ActivityWatch and other signals), **synthesizes summary comments and status updates**, and **writes them into Notion and Jira** so progress is updated automatically instead of you typing static updates by hand.

---

## Direction (clarified)

- **Not:** Manually updating Notion/Jira, or building one-off reports you read.
- **Yes:** A background "activity observer" that:
  1. **Monitors** your activity (focus time, apps, windows, tabs, optional: git, open files).
  2. **Combines** that with ActivityWatch (and any other) data into a coherent view of "what was worked on."
  3. **Produces** short summary comments and suggested status updates (e.g. "Spent 2h on notion-integration-app; 3 commits on Slack assignment flow; suggest: In Progress, comment: '…'").
  4. **Pushes** those updates into progress tools: **Notion** (page comments, property updates, or block content) and **Jira** (comments, status, description).

So Notion and Jira become **destinations** for auto-generated, activity-derived progress text—not the source of truth you update statically.

---

## High-level architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│  OBSERVE                                                                 │
│  • ActivityWatch API (events: app, window, tab, duration, AFK)            │
│  • Optional: git log (commits, files) in time window                     │
│  • Optional: focused Notion page / Jira issue (browser tab or IDE)       │
└───────────────────────────────┬─────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  SYNTHESIZE                                                              │
│  • Group events by time window (e.g. last 2h, or "session")               │
│  • Map activity → project/task (e.g. repo name, Notion DB, Jira key)     │
│  • Generate summary text (template-based or LLM)                        │
│  • Suggest status (e.g. In Progress, Done) and comment body              │
└───────────────────────────────┬─────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  WRITE TO PROGRESS TOOLS                                                 │
│  • Notion: append comment to page, or update property (e.g. "Last update")│
│  • Jira: add comment, transition issue status, update description       │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Data sources (observe)

| Source | What it gives | How you get it |
|--------|----------------|----------------|
| **ActivityWatch** | Time per app/window/tab, AFK, categories | REST API (`/api/0/buckets/.../events`, query API); aw-client (Python) or fetch from your app |
| **Git** | Commits, messages, files changed in a time range | `git log --since/--until`, or libgit2 / simple-exec in your tool |
| **Window/tab title** | "Cursor – notion-integration-app", "Notion – Security DB" | Already in AW (aw-watcher-window, aw-watcher-web); no extra watcher needed if AW is running |
| **Notion (optional)** | "Currently open page" or "last edited page" | Harder; could infer from browser tab URL (AW) or a small Notion-side "focus" convention |
| **Jira (optional)** | "Currently open issue" | Same idea: infer from tab URL or branch name (e.g. branch `feature/PROJ-123`) |

Start with **ActivityWatch + git**; add Notion/Jira "current context" later if you want.

---

## Synthesis (summaries and status)

- **Time windows:** e.g. "last 2 hours," "today," "since last push."
- **Project/task mapping:**  
  - From AW: map app + window/title or URL to "project" (e.g. Cursor + path → `notion-integration-app`; Chrome + notion.so → "Notion Security").  
  - From git: repo + branch (and optionally `JIRA-123` in branch name).
- **Summary text:**  
  - **Templates:** "Spent {duration} on {project}. Git: {n} commits – {messages}."  
  - **LLM (optional):** Feed window titles, commit messages, and durations; ask for 1–2 sentence status comment.
- **Status suggestion:** Rules (e.g. "if commits in last 4h and no 'Done' in message → In Progress") or LLM.

Output per "session" or per "task" could be:

- `summary_comment`: string to post as Notion/Jira comment.
- `suggested_status`: e.g. "In Progress" (for Jira) or a Notion select value.
- `target`: which Notion page or Jira issue to update (if known).

---

## Writing to Notion and Jira

- **Notion**
  - **Comments:** Create comment on a page (e.g. "Progress: …") via Notion API.
  - **Properties:** Update "Last update" or "Status" or a rich-text "Progress log" on a database page.
  - **Blocks:** Append a "Progress" block to a page (e.g. "2025-02-09: …").
- **Jira**
  - **Comments:** Add comment with the summary text.
  - **Status:** Transition issue (e.g. To Do → In Progress) via Jira API.
  - **Description / custom field:** Append or set "Last activity summary."

A separate **notion-integration-app** (or similar) may already talk to Notion; this "activity → progress" tool would implement the flow that **writes** these updates from ActivityWatch-derived data.

---

## Where this could live

1. **Inside this repo (activitywatch-mcp-integration)**  
   New module or script: e.g. `src/activity-summary/` or `scripts/activity-to-progress.js` that:
   - Pulls from ActivityWatch (and optionally git),
   - Runs synthesis (template or LLM),
   - Calls Notion and Jira APIs to create comments or update properties.

2. **Separate service**  
   A small daemon or cron job that runs the same pipeline and posts to Notion/Jira via API (same as above, different process).

3. **Hybrid**  
   Script run on a schedule (e.g. every 2 hours or at end of day); no need for a full daemon at first.

---

## Practical next steps

1. **Proof of concept**
   - Script that: reads ActivityWatch events for "last N hours" (e.g. port 5600), plus `git log` for same window.
   - Maps activity to one "project" (e.g. repo name or "Unknown").
   - Produces one summary string (template: "Spent X on Y; Z commits: …").
   - Writes that string as a **Notion comment** on a **configurable page ID** (or a test page).

2. **Config**
   - `.env` or config: ActivityWatch base URL, Notion page/DB IDs to update, optional Jira project/key.
   - Optional: which time window, how often to run, whether to use LLM.

3. **Linking updates to the "right" task**
   - **Simple:** One Notion page or Jira filter = "my current work"; all summaries go there as comments.
   - **Richer:** Infer task from AW (e.g. URL contains notion page id, or branch name = Jira key) and post comment on that page/issue.

4. **Status updates**
   - Once comments work, add: "suggested status" from rules or LLM, and call Notion/Jira to set status (or append to a "Progress" property).

---

## Summary

| What you want | How this plan addresses it |
|---------------|-----------------------------|
| Something that **watches** what you're working on | ActivityWatch + optional git (and later tab/focus) as inputs. |
| **Combine** that with ActivityWatch data | Pipeline aggregates AW events + git (and optional context) per time window and project. |
| **Detailed summaries and status updates** | Synthesis step produces comment text and optional status suggestion (template or LLM). |
| **Import / push into Notion and Jira** | Write step uses Notion API (comments, properties, blocks) and Jira API (comments, transitions) so those tools are **populated** by the tool instead of static manual updates. |

This gives you a clear direction: **observe → summarize → write to Notion/Jira**, with the option to extend data sources and synthesis (e.g. LLM) over time.
