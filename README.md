# BulkPublish AI Toolkit

Connect your AI assistant to [BulkPublish](https://bulkpublish.com) — schedule posts, manage channels, upload media, and track analytics across 15 social media platforms, all from your AI tool of choice.

## What You Get

**79 MCP tools** in the local server (`npx @bulkpublish/mcp-server@1.33.2`), including interactive MCP Apps widgets. The hosted server at `https://mcp.bulkpublish.com/mcp` (OAuth 2.1, used by the Claude and ChatGPT directories) serves the 20-tool `core` profile — channels, posts, media, analytics and the interactive panels — or the 27-tool `inbox` profile, which adds the Inbox and comment tools; `BULKPUBLISH_TOOL_PROFILE=core|inbox|full` switches either.

| Category | Tools (full profile) | Examples |
|---|---|---|
| **Posts** | 13 | Create, update, get, list, publish, retry, delete, approve, reject, story, bulk actions, metrics, queue slot |
| **Channels** | 5 | List accounts, platforms, health check, platform options, @mention search |
| **Media** | 10 | Upload (URL/file), list, get, alt text, delete, presigned and chunked uploads |
| **Analytics** | 1 | Summary by date range with per-platform breakdown |
| **Labels, hashtag groups, templates, calendar notes** | 16 | Create, list, update, delete each |
| **Client review & connect links** | 8 | Share/unshare a post, batch review links, client connect links |
| **Schedules, channel sets, RSS autopost** | 12 | Create, list, update, delete each |
| **Inbox and comments** | 7 | List and read conversations, reply to DMs and reviews, triage (read/archive/snooze/assign), list comments, reply to and moderate comments |
| **Quota** | 1 | Plan usage and limits |
| **Interactive panels** | 6 | Composer, posts, channels, media, analytics, quota |

Every tool carries a title and explicit `readOnlyHint` / `destructiveHint` / `idempotentHint` / `openWorldHint` values.

**Team approval:** posts carry an `approvalStatus` (`none` default | `pending` | `approved` | `rejected`) that is separate from `status` — a pending or rejected post never publishes. Pass `requestApproval: true` (default `false`) when creating or updating a scheduled post to hold it for review (a draft ignores it); it is forced on for API keys whose role lacks `post:publish` (contributors), who also get `403 APPROVAL_REQUIRED` from publish/retry. Owners, admins and approvers release posts with `approve_post` / `reject_post`. An approved post publishes at its scheduled time, or immediately if that time passed less than 15 minutes ago; if it passed more than 15 minutes ago, the post comes back as `status: "draft"` (`approvalStatus: "approved"`, `scheduledAt` unchanged) and the author is notified to choose a new time. Set `publishWhenApproved: true` on create/update to have a late approval publish straight away instead (use it for "publish now, once approved", scheduling for the current time — not when a specific time was chosen), and `approve_post` takes an optional `whenLate` (`"publish"` | `"hold"`) to decide for a single late post; left out, the post's `publishWhenApproved` decides. Both tools take an optional `ifUnmodifiedSince` — pass the `updatedAt` of the version shown to the user — and return 409 if the post changed since you loaded it (checked when `ifUnmodifiedSince` is sent) or is no longer awaiting approval; nothing is written, so reload it and review again. Approving or rejecting sets `publishWhenApproved` to `false`.

**Link tracking:** BulkPublish can rewrite a post's links to `bulkpubli.sh` short URLs and count the clicks — the only way to measure outbound clicks, since platform APIs do not report them (X dropped the field entirely). It is off by default, opt-in per organization, and part of the Pro and Business plans only (on Free, links always publish as written); `linkTrackingOverride` (`true` | `false` | `null`, default `null`) overrides it per post. It is **tri-state — omit it unless the user asked**, because `false` is an explicit "post the links as written", not the same as inheriting. Clicks come back as `linkClicks` / `totalLinkClicks`, which are measured by us and therefore available on every platform, and are deliberately **not** part of `clicks`/`totalClicks` — one visit can register in both, so never add them together. Shortening is skipped on any channel where the rewrite would exceed that platform's character limit (a short URL is 28 characters and can be longer than the link it replaces), so tracking can silently not apply on X or Bluesky; the post still publishes with its original links.

**Inbox and comments:** the Inbox (DMs, reviews, comment threads) is part of the Pro and Business plans; replying to or moderating comments on a post is on every plan except Free; reading a post's comments works on every plan. A channel connected before message or comment access was available may need reconnecting, and some platforms do not allow DMs or replies yet. Replies and deletions cannot be unsent, so the `manage-inbox` skill makes the assistant show the exact text and recipient and wait for your yes on each one.

**Supported platforms:** Facebook, Instagram, X/Twitter, TikTok, YouTube, Threads, Bluesky, Pinterest, Google Business Profile, LinkedIn, Mastodon, Discord, Telegram, Tumblr, Snapchat

## Prerequisites

- **Node.js 18+** (for running the MCP server via `npx`)
- **BulkPublish API key** — get one at [app.bulkpublish.com/developer](https://app.bulkpublish.com/developer)

---

## Quick Install

Three ways to get started — pick what fits your workflow:

```bash
# 1. Claude Code Plugin (recommended — auto-updates, includes skills)
/plugin marketplace add azeemkafridi/bulkpublish-ai-toolkit
/plugin install bulkpublish@bulkpublish-ai-toolkit

# 2. Agent Skills (works with 41+ agents — Claude Code, Cursor, Codex, Copilot, etc.)
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit

# 3. MCP Server (any MCP-compatible tool)
claude mcp add bulkpublish -- npx -y @bulkpublish/mcp-server@1.33.2
```

---

## Setup by Tool

### Claude Code

**Option A — Plugin (recommended, auto-updates):**

```bash
/plugin marketplace add azeemkafridi/bulkpublish-ai-toolkit
/plugin install bulkpublish@bulkpublish-ai-toolkit
```

You'll be prompted for your API key during installation.

**Option B — Agent Skills:**

```bash
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit
```

Or install a single skill:

```bash
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit --skill schedule-post
```

**Option C — MCP only:**

```bash
claude mcp add bulkpublish -- npx -y @bulkpublish/mcp-server@1.33.2
```

Then set your API key:

```bash
export BULKPUBLISH_API_KEY=bp_your_key_here
```

**Option D — hosted server, no key on disk (recommended for MCP-only use):**

```bash
claude mcp add --transport http bulkpublish https://mcp.bulkpublish.com/mcp
```

Claude Code opens the sign-in when you first use a tool. The token belongs to
that client alone and you can revoke it from your account at any time.

---

### Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "bulkpublish": {
      "command": "npx",
      "args": ["-y", "@bulkpublish/mcp-server@1.33.2"],
      "env": {
        "BULKPUBLISH_API_KEY": "bp_your_key_here"
      }
    }
  }
}
```

Restart Claude Desktop to activate.

---

### Cursor

**Option A — Plugin (recommended, includes the skills and needs no key):**

Search for **BulkPublish** in **Cursor Settings → Plugins** and click Install, then
complete the BulkPublish sign-in prompt. Or add this repo as a marketplace and
install from it:

```bash
/add-plugin bulkpublish
```

The plugin connects to the hosted server at `https://mcp.bulkpublish.com/mcp`.
Sign-in is OAuth 2.1 with Dynamic Client Registration and PKCE, so Cursor
registers itself and there is no client ID or API key to paste. It ships all
seven skills alongside the tools.

**Option B — Agent Skills:**

```bash
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit -a cursor
```

**Option C — MCP config.** Create or edit `.cursor/mcp.json` in your project root (or globally at `~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "bulkpublish": {
      "command": "npx",
      "args": ["-y", "@bulkpublish/mcp-server@1.33.2"],
      "env": {
        "BULKPUBLISH_API_KEY": "bp_your_key_here"
      }
    }
  }
}
```

**Option D — hosted server, no key on disk.** `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "bulkpublish": {
      "url": "https://mcp.bulkpublish.com/mcp"
    }
  }
}
```

---

### Windsurf

**Option A — Agent Skills:**

```bash
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit -a windsurf
```

**Option B — MCP config.** Edit `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "bulkpublish": {
      "command": "npx",
      "args": ["-y", "@bulkpublish/mcp-server@1.33.2"],
      "env": {
        "BULKPUBLISH_API_KEY": "${env:BULKPUBLISH_API_KEY}"
      }
    }
  }
}
```

Set your key in your shell environment:

```bash
export BULKPUBLISH_API_KEY=bp_your_key_here
```

Or hardcode it directly in the `env` block above.

**Option C — hosted server, no key on disk.** Windsurf uses `serverUrl` for
remote servers, not `url`. In `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "bulkpublish": {
      "serverUrl": "https://mcp.bulkpublish.com/mcp"
    }
  }
}
```

---

### VS Code (GitHub Copilot)

**Option A — Agent Skills:**

```bash
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit -a github-copilot
```

**Option B — MCP config.** Add to your VS Code `settings.json` (Cmd+Shift+P > "Preferences: Open User Settings (JSON)"):

```json
{
  "mcp": {
    "servers": {
      "bulkpublish": {
        "command": "npx",
        "args": ["-y", "@bulkpublish/mcp-server@1.33.2"],
        "env": {
          "BULKPUBLISH_API_KEY": "bp_your_key_here"
        }
      }
    }
  }
}
```

**Option C — hosted server, no key on disk.** VS Code's key is `servers`, not
`mcpServers`. In `.vscode/mcp.json`:

```json
{
  "servers": {
    "bulkpublish": {
      "type": "http",
      "url": "https://mcp.bulkpublish.com/mcp"
    }
  }
}
```

---

### Gemini CLI

**Option A — CLI command:**

```bash
gemini mcp add bulkpublish -- npx -y @bulkpublish/mcp-server@1.33.2
```

**Option B — Edit `~/.gemini/settings.json`:**

```json
{
  "mcpServers": {
    "bulkpublish": {
      "command": "npx",
      "args": ["-y", "@bulkpublish/mcp-server@1.33.2"],
      "env": {
        "BULKPUBLISH_API_KEY": "$BULKPUBLISH_API_KEY"
      }
    }
  }
}
```

Set your key:

```bash
export BULKPUBLISH_API_KEY=bp_your_key_here
```

**Option C — hosted server, no key on disk:**

```bash
gemini mcp add --transport http bulkpublish https://mcp.bulkpublish.com/mcp
```

Editing `~/.gemini/settings.json` by hand works too, but the key for a
streamable HTTP server is `httpUrl`. Plain `url` means SSE and will not
connect:

```json
{
  "mcpServers": {
    "bulkpublish": {
      "httpUrl": "https://mcp.bulkpublish.com/mcp"
    }
  }
}
```

---

### OpenAI Codex CLI

**Option A — Agent Skills:**

```bash
npx skills@1.7.0 add azeemkafridi/bulkpublish-ai-toolkit -a codex
```

**Option B — CLI command:**

```bash
codex mcp add bulkpublish -- npx -y @bulkpublish/mcp-server@1.33.2
```

**Option C — Edit `~/.codex/config.toml`:**

```toml
[mcp_servers.bulkpublish]
command = "npx"
args = ["-y", "@bulkpublish/mcp-server@1.33.2"]

[mcp_servers.bulkpublish.env]
BULKPUBLISH_API_KEY = "bp_your_key_here"
```

**Option D — hosted server, no key on disk.** `codex mcp add` covers stdio
servers only, so this one is edited into `~/.codex/config.toml`:

```toml
[mcp_servers.bulkpublish]
url = "https://mcp.bulkpublish.com/mcp"
```

---

### Hosted server (ChatGPT, claude.ai, and other web hosts)

Web-based hosts can't spawn a local process, so they need a remote HTTP endpoint
instead of the `npx` stdio setups above. BulkPublish hosts one:

```
https://mcp.bulkpublish.com/mcp
```

It serves the same tool suite as the stdio server over Streamable HTTP, and
authenticates two ways:

- **OAuth 2.1 (recommended).** Add the URL as a custom connector and the host
  walks you through the consent screen, where you paste your `bp_` API key once.
  No key ever lives in the connector's URL.
- **Key in the URL,** for hosts that don't do OAuth:
  `https://mcp.bulkpublish.com/mcp?key=bp_your_key_here`. Treat that URL as a
  secret — anyone holding it holds your API key.

Get a key at [app.bulkpublish.com/developer](https://app.bulkpublish.com/developer).

---

### Antigravity

Antigravity accepts only `serverUrl` for a remote server. Its own docs are
explicit that `url` and `httpUrl` are not supported, so the shape that works
in Cursor fails here silently. Edit `~/.gemini/config/mcp_config.json` (or
`.agents/mcp_config.json` in a workspace):

```json
{
  "mcpServers": {
    "bulkpublish": {
      "serverUrl": "https://mcp.bulkpublish.com/mcp"
    }
  }
}
```

---

### Cline

Set `type` explicitly rather than leaving Cline to infer the transport. In
`cline_mcp_settings.json`:

```json
{
  "mcpServers": {
    "bulkpublish": {
      "type": "streamableHttp",
      "url": "https://mcp.bulkpublish.com/mcp"
    }
  }
}
```

---

### Warp

Warp's dialog takes the server object on its own, with no `mcpServers`
wrapper around it. Open **MCP servers > + Add** and paste:

```json
{
  "bulkpublish": {
    "url": "https://mcp.bulkpublish.com/mcp"
  }
}
```

---

### Grok

No config file. Open **grok.com/connectors > New connector > Custom**, enter
`https://mcp.bulkpublish.com/mcp`, and sign in when prompted. Grok
requires a publicly reachable URL, which the hosted server is.

---

### Grok Bot

Grok Bot, xAI's always-on cloud agent, is set up in chat rather than in a
settings screen. Send it:

```
Add a custom MCP server called BulkPublish at https://mcp.bulkpublish.com/mcp
```

Confirm the name and address it shows back, then click **Authorize** on the
connect card and sign in. Type `@` in a chat to attach BulkPublish. Grok Bot
only reaches public HTTPS servers, which the hosted one is. It cannot run the
local npx server.

---

### Muse

Muse, Meta's personal AI agent, has no connector settings screen. You set it
up by asking in chat. Send Muse a message like this:

```
Add a custom MCP connector called BulkPublish. The server is at
https://mcp.bulkpublish.com/mcp over Streamable HTTP and signs in with OAuth.
```

Muse may ask whether the server needs a key. It does not: the server signs in
with OAuth, so you approve access in your browser and paste nothing. After
that, ask it to post, schedule, or read your analytics in plain language.

---

### Other MCP-Compatible Tools

Any tool that supports the [Model Context Protocol](https://modelcontextprotocol.io) can connect to BulkPublish. The general pattern:

**Stdio transport:**
```
command: npx
args: -y @bulkpublish/mcp-server@1.33.2
env: BULKPUBLISH_API_KEY=bp_your_key_here
```

---

## Skills (Claude Code Plugin)

If you installed via the Claude Code plugin, you get these skills:

| Skill | Command | Description |
|---|---|---|
| Platform Reference | `/bulkpublish:platform-reference` | All 15 platforms — post types, media rules, required fields, limits |
| Schedule Post | `/bulkpublish:schedule-post` | Create and schedule posts with optimal timing, incl. the team approval flow |
| Get Analytics | `/bulkpublish:get-analytics` | Pull performance reports and engagement data |
| Manage Channels | `/bulkpublish:manage-channels` | View connected accounts, channel sets, and troubleshoot issues |
| Bulk Publish | `/bulkpublish:bulk-publish` | Upload media (multipart for videos up to 1GB) and publish posts in batch |
| Check Quota | `/bulkpublish:check-quota` | View plan limits and current usage |

---

## Prompts to Start With

Paste one of these once the server is connected. Each uses only what the
hosted `core` profile can do, so the assistant is never left guessing.
Replace anything in square brackets.

**See what is connected**
> Show me my BulkPublish channels, then list everything scheduled on them for the next seven days. Tell me which channels have nothing queued.

**Draft one post and queue it**
> Write a LinkedIn post about [your topic]. Keep to my usual voice: [describe it in a line]. Show me the draft first, and once I say yes, schedule it in BulkPublish to the next free slot in my queue.

**Turn a link into posts**
> Read [paste a URL] and turn it into one post for each of my connected BulkPublish channels, adapted to what works on each network rather than the same text everywhere. Schedule them for next Tuesday morning. Show me all of them before you schedule anything.

**Plan a week in one pass**
> Here are five things I want to post about this week: [list them]. Create a BulkPublish post for each, spread across the free slots in my queue so nothing goes out back to back, and show me the finished week when you are done.

**Post an image with caption options**
> Upload [file path, or attach the image] to my BulkPublish media library, then write three caption options for Instagram, each with a different angle. Once I pick one, schedule it with that image.

**Check the next two weeks**
> List everything scheduled in BulkPublish for the next fourteen days, grouped by day. Flag anything duplicated, anything going out at an odd hour for its network, and any day with nothing at all.

**Fix what failed**
> Find any BulkPublish posts that failed in the last seven days. For each one, tell me in plain language what went wrong, then retry only the ones that look like a temporary problem and tell me which ones need me to fix something first.

**Read the numbers back**
> Pull my BulkPublish analytics for the last thirty days. Tell me the three posts that did best, what they have in common, and one thing I should do more of next month. Use my numbers, not general advice.

The same list, in nine languages, is on every client guide at
[bulkpublish.com/integrations](https://www.bulkpublish.com/integrations/).

---

## Examples

Once connected, just ask your AI assistant in natural language:

```
"Schedule a post about our new feature to Twitter and LinkedIn for tomorrow at 9am EST"

"How did my posts perform this week?"

"Upload these 5 images and schedule them as daily Instagram posts"

"Which of my channels have connection issues?"

"How many posts do I have left on my plan today?"

"Set up my blog's RSS feed so new articles land as drafts for my LinkedIn and X channels"

"Schedule this for Friday 9am but hold it for my editor to approve"

"Show me everything waiting on approval, then approve the LinkedIn one"

"What came into my inbox today? Draft replies for the questions"
```

---

## MCP Tool Reference

The hosted server serves the 20-tool `core` profile marked `*` below (plus the tools marked `+` where it serves the `inbox` profile); the local
server serves all 79 (the list below shows the main groups; labels, hashtag
groups, templates, calendar notes and review/connect links are summarised in
the table above). Set `BULKPUBLISH_TOOL_PROFILE=core|inbox|full` to switch.

<details>
<summary>Click to expand full tool list</summary>

**Posts (13):**
`create_post`*, `update_post`*, `delete_post`*, `list_posts`*, `get_post`*, `publish_post`*, `retry_post`*, `get_post_metrics`*, `get_queue_slot`*, `approve_post`, `reject_post`, `publish_story`, `bulk_posts`

**Channels (4):**
`list_channels`*, `get_channel_health`, `get_channel_options`, `search_mentions`

**Platforms (1):**
`list_platforms`

**Media (9):**
`upload_media`*, `list_media`*, `create_media_upload`*, `finalize_media_upload`*, `get_media`, `delete_media`, `create_multipart_upload`, `complete_multipart_upload`, `abort_multipart_upload`

**Analytics (1):**
`get_analytics`*

**Labels (4):**
`list_labels`, `create_label`, `update_label`, `delete_label`

**Channel sets (4):**
`list_channel_sets`, `create_channel_set`, `update_channel_set`, `delete_channel_set`

**Schedules (4):**
`list_schedules`, `create_schedule`, `update_schedule`, `delete_schedule`

**RSS autopost (4):**
`list_rss_feeds`, `create_rss_feed`, `update_rss_feed`, `delete_rss_feed`

**Inbox and comments (7):**
`list_conversations`+, `get_conversation`+, `reply_to_conversation`+, `update_conversation`+, `list_post_comments`+, `reply_to_comment`+, `moderate_comment`+

**Quota (1):**
`get_quota_usage`

**Interactive UI (MCP Apps) (6):**
`compose_post`*, `view_analytics`*, `view_posts`*, `view_channels`*, `view_media`*, `view_quota`

`*` = in the hosted `core` profile. `+` = added by the `inbox` profile.

</details>

---

## Other Integrations

BulkPublish is available across many platforms:

| Integration | Install |
|---|---|
| **Node.js SDK** | `npm install bulkpublish` |
| **Python SDK** | `pip install bulkpublish` |
| **Homebrew CLI** | `brew tap azeemkafridi/bulkpublish && brew install bulkpublish` |
| **Zapier** | [Search "BulkPublish" in Zapier](https://zapier.com/apps/bulkpublish) |
| **n8n** | `n8n-nodes-bulkpublish` (Community Nodes) |
| **Make.com** | Search "BulkPublish" in Make |
| **IFTTT** | [ifttt.com/services/bulkpublish](https://ifttt.com/services/bulkpublish) |
| **REST API** | [app.bulkpublish.com/docs](https://app.bulkpublish.com/docs) |
| **Postman** | [Import collection](https://github.com/azeemkafridi/bulkpublish-api/blob/main/postman-collection.json) |

---

## Links

- [BulkPublish](https://bulkpublish.com) — Main site
- [API Docs](https://app.bulkpublish.com/docs) — Interactive API reference
- [Developer Dashboard](https://app.bulkpublish.com/developer) — API keys & usage
- [MCP Server on npm](https://www.npmjs.com/package/@bulkpublish/mcp-server) — `@bulkpublish/mcp-server@1.33.2`
- [GitHub](https://github.com/azeemkafridi/bulkpublish-api) — SDKs, examples, guides

## Network endpoints and credentials

- `mcp.json` (Agent Plugins format, used by Cursor): connects to the hosted server `https://mcp.bulkpublish.com/mcp` over HTTPS and signs in with OAuth. No key is stored in the repo.
- `.mcp.json` (Grok Build and other clients): runs the pinned `@bulkpublish/mcp-server@1.33.2` locally via `npx`. It reads your key from the `BULKPUBLISH_API_KEY` environment variable and calls only `https://app.bulkpublish.com` with it.
- Credentials: a BulkPublish API key (starts with `bp_`, created at https://app.bulkpublish.com/developer) or an OAuth sign-in. No other secrets, files, or env vars are read.

## Privacy Policy

The plugin runs the BulkPublish MCP server on your machine and talks only to
your BulkPublish account over HTTPS, using the API key you configure. It does
not read your conversation history, files, or memory, and it sends nothing to
anyone other than BulkPublish. How BulkPublish collects, uses, retains and
shares data, and how to contact us, is described in the
[BulkPublish Privacy Policy](https://www.bulkpublish.com/privacy/).

## License

MIT
