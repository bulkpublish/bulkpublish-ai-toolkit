# Changelog

## 1.14.0 — 2026-10-24

- The Claude Code plugin now connects to the hosted server at
  `https://mcp.bulkpublish.com/mcp/full` instead of starting the npm package
  with `npx`. You sign in once in the browser; there is no API key to enter at
  install time and nothing runs on your machine. It keeps every tool the local
  server had. `.mcp.json` points at the same address.

## 1.13.0 — 2026-10-09

- `platform-reference` lists the new post options for X, Bluesky, Mastodon,
  Threads, Instagram, Facebook, TikTok, YouTube, Pinterest, LinkedIn, Telegram,
  Discord and Tumblr, the LinkedIn post types `document`, `poll` and `reshare`,
  and that documents (PDF, Word, PowerPoint) publish to LinkedIn only.
- Telegram now takes options (it used to be listed as having none).
- `manage-inbox` notes that Threads supports hide and unhide, Facebook supports
  hide, unhide and delete, and hidden comments come back with `hidden: true`.

## 1.12.0 — 2026-10-09

### Removed

- Reddit platform support. Breaking: `reddit` is no longer a valid platform
  value; the Reddit section of `platform-reference` and its rows in
  `manage-channels` and `get-analytics` are gone.

## 1.11.0 — 2026-10-05

- New `manage-inbox` skill: read and triage Inbox conversations (DMs,
  reviews, comment threads), reply to DMs and comments, and moderate
  comments. The assistant shows the exact text and recipient and asks before
  every send, reply or deletion, and never sends in bulk.
- Tool counts: seven new Inbox and comment tools. The local server serves 79
  (was 72); the hosted server serves them in its 27-tool `inbox` profile, and
  stays at 20 under `core`.
- `using-bulkpublish` and README list the new tools and explain which are
  plan-gated (the Inbox is Pro and Business) and when a channel needs
  reconnecting.
- Cursor plugin registers `manage-inbox`.

## 1.10.9 — 2026-10-02

- Skills: `search_mentions` now covers Mastodon. Handles come back as
  `@username` (same server) or `@username@domain` (another server).

## 1.10.8 — 2026-09-27

- Skills: Bluesky video no longer claims a 100MB limit (not published for
  the upload path used; ~19MB proven). Pinterest video pins are 4 seconds to
  5 minutes, with no published file size for ordinary pins (2GB is the
  video-ads figure).

## 1.10.7 — 2026-09-27

- Skills: X video length is back to 140 seconds (up to 4 hours with X
  Premium), per X's own help center. 1.10.6 had moved it to 20 minutes from
  X's API upload docs, which is not what a standard account may post.

## 1.10.6 — 2026-09-27

- Skills: media specs corrected against each platform's own docs. X videos
  run to 20 minutes (longer on X Premium), not 140 seconds; Bluesky and
  Mastodon set no video length; Instagram Reels are 3 seconds to 15 minutes,
  not 3–90 seconds; TikTok allows 3 minutes for every account and 5 or 10 for
  some. Added the Reel and Story ranges for Facebook and Instagram, the Threads
  5-minute cap and the Pinterest 4-second floor, all of which BulkPublish now
  checks when a post is scheduled or published.

## 1.10.5 — 2026-09-27

- README: a Grok Bot section. Like Muse, it is set up with one chat message
  and an Authorize card.

## 1.10.4 — 2026-09-26

- README: a Muse section. Muse has no connector settings screen, so setup is one
  chat message with the hosted server address.

## 1.10.3 — 2026-09-26

- Plugin: the local MCP server is pinned to `@bulkpublish/mcp-server@1.33.2`
  instead of the unpinned latest, as the Claude plugin directory requires.

## 1.10.2 — 2026-09-26

- README: the local server has 72 tools, not 51.
- `platform-reference`: TikTok `privacyLevel` values are `PUBLIC_TO_EVERYONE`,
  `MUTUAL_FOLLOW_FRIENDS`, `FOLLOWER_OF_CREATOR`, `SELF_ONLY` (others are rejected),
  and an omitted value publishes as `PUBLIC_TO_EVERYONE`, not `SELF_ONLY`. Reddit
  video `thumbnailUrl` is optional: the video's poster frame is used when omitted.
- `schedule-post`: TikTok post type is `photo_slideshow`, not `slideshow`. RSS feed
  limits and check interval depend on the plan (not a flat 20 feeds / 15 minutes),
  and `mode: "publish"` returns 403 `FEATURE_DISABLED` on plans without auto-publish.
- `get-analytics`: `get_analytics` takes `from` / `to`, not `startDate` / `endDate`.
- `using-bulkpublish`: `compare` works only for windows of 15 days or fewer.
- `manage-channels`: `tokenStatus` is `valid` / `expiring_soon` / `expired`;
  `search_mentions` also covers LinkedIn; channel sets have MCP tools.
- `bulk-publish`: multipart uploads have MCP tools. Storage quota on a multipart
  upload is 403 `QUOTA_EXCEEDED`, not 429. Bulk `reschedule` can return 403
  `QUOTA_EXCEEDED` (pending-scheduled or per-day cap) and 400 on a bad date.
- `schedule-post`: `content` is optional (media-only posts). `get_queue_slot`
  returns `{scheduledAt, dayLabel}`, not `{suggestedTime, timezone}`. Over the
  RSS feed limit is 403 `QUOTA_EXCEEDED`. A scheduled post naming a disconnected
  channel is 400 `CHANNEL_INACTIVE`.

## 1.10.1 — 2026-09-24

- Documents the optional `ifUnmodifiedSince` on `approve_post` / `reject_post`: pass
  the `updatedAt` of the version shown to the user, and a post that changed since is
  left untouched with a 409.
- Corrects the 409 wording: the post changed since you loaded it, or is no longer
  awaiting approval. A moved scheduled time is not a cause. Approve and reject both
  set `publishWhenApproved` to `false`.

## 1.10.0 — 2026-09-24

- Documents `publishWhenApproved` on `create_post` / `update_post`: a post approved
  after its scheduled time publishes straight away instead of returning to draft.
  `schedule-post` adds a "Publish now, with review" flow (schedule for the current
  time + `requestApproval` + `publishWhenApproved`) and says not to set it when the
  user chose a specific time.
- Documents the optional `whenLate` (`"publish"` | `"hold"`) on `approve_post`, which
  decides what a late approval does; left out, the post's `publishWhenApproved` decides.

## 1.9.7 — 2026-09-24

- `schedule-post` and `bulk-publish` say pending and rejected posts "do not publish"
  until approved, instead of describing how that is done.
- The approve/reject **409** now names every cause: someone else approved, rejected or
  withdrew the post, or (on approve) its scheduled time moved.
- `schedule-post` and the README say `requestApproval` applies only to scheduled posts;
  a draft ignores it and stays `approvalStatus: "none"`.

## 1.9.6 — 2026-09-23

- `schedule-post` and the README describe the new approval rule: approving a pending
  post publishes it at its scheduled time, or immediately if that time passed less than
  15 minutes ago. If it passed more than 15 minutes ago, the post is approved but not
  published — it comes back with `status: "draft"` (`approvalStatus: "approved"`,
  `scheduledAt` unchanged) and the author is notified to choose a new time. Agents
  should read the returned `status` instead of saying the post went out.
- `approve_post` / `reject_post` document the **409** returned when the post stopped
  awaiting approval while the request was in flight (approved, rejected or withdrawn by
  someone else): reload it with `get_post` and review again.

## 1.9.5 — 2026-09-17

- `check-quota` documents the fourth `plan` value, `ltd`: the Lifetime plan granted by
  an AppSumo license. Treat it as Free-like for X (no included budget, credits only)
  and otherwise read `limits` rather than assuming figures from the plan name.

## 1.9.4 — 2026-09-13

- The RSS field-mapping reference now covers `articleImage` (`when_needed` default /
  `always` / `never`): a feed item with no picture of its own borrows one from the
  article it links to, so a text-only feed works on Instagram, Pinterest and Snapchat
  instead of skipping those channels. Also corrects the old claim that image-required
  platforms are skipped whenever an item lacks an enclosure — that now happens only
  when the article yields nothing too.

## 1.9.3 — 2026-09-11

- Cursor plugin no longer lists the `check-quota` skill: it connects to the
  hosted server, whose 20-tool profile has no quota tools, so the skill promised
  something that server cannot do. The Claude Code plugin keeps it (its local
  server has them).

- `plugin.json` now passes `claude plugin validate --strict`: the MCP server
  reference is plugin-root relative (`./.mcp.json`) and the `api_key` user
  setting declares its `type` and `title`.
- README gains a Privacy Policy section, as the Connectors Directory requires
  for locally run connectors.
- The platform reference says 15 platforms, matching every other surface
  (Reddit is documented but marked switched off).

## 1.9.2 (2026-09-11)

### Fixed
- `schedule-post`: a `scheduledAt` that is now or in the past is accepted and publishes immediately (it was described as required to be in the future), so agents no longer refuse it, and are told to double-check the date.

- **1.9.1 broke the GitHub import it claimed to fix.** Cursor's Import from
  GitHub Repo imports a repository *as a marketplace* and reads
  `.cursor-plugin/marketplace.json`; the import had been working, and removing
  that file is what stopped it. The single-entry `marketplace.json` with
  `source: "./"` beside `plugin.json` is the intended shape for a
  single-plugin repo, and is what other listed single-plugin repos ship.
  Restored. The validator accepts the pairing again and says why, so it is not
  removed a third time.
- Note on re-importing: pasting an already-imported repo does nothing visible
  because it is already registered. Use the marketplace's refresh button, or
  wait for auto-refresh, which re-indexes at most once every ten minutes.

## 1.9.1 (2026-09-11)

### Fixed

- **The repo declared itself both a plugin and a marketplace, and Cursor
  imported neither.** `.cursor-plugin/` held a `plugin.json` *and* a
  `marketplace.json` whose only entry pointed back at the repo root with
  `source: "./"`. Cursor recognises two shapes and never mixes them: a
  single-plugin repo has `plugin.json` at the root, a marketplace has
  `marketplace.json` at the root and each plugin in its own subdirectory —
  their own marketplace root holds `marketplace.json` alone, and not one of
  the 70 plugins in it uses `source: "./"`. Pasting the repo URL into Import
  from GitHub Repo did nothing at all, with no error. This is a single-plugin
  repo, so `marketplace.json` is gone.
- **The validator now understands both shapes** and fails on the hybrid, with
  the reason spelled out, so the same silent failure cannot return. CI checks
  `marketplace.json` against its schema only when one exists.

## 1.9.0 (2026-09-11)

### Added

- **Cursor plugin.** `.cursor-plugin/plugin.json` sits alongside the Claude
  manifests over the same `skills/` tree, so one repo serves both marketplaces
  and neither copy of the skills can drift from the other. The plugin points at the hosted server
  at `https://mcp.bulkpublish.com/mcp`, which authorizes over OAuth 2.1 with
  Dynamic Client Registration and PKCE, so there is no client ID or API key to
  configure. All seven skills ship with it.
- **`assets/logo.svg`.** The plated mark, referenced by relative path from both
  Cursor manifests so it resolves as the marketplace logotype.
- **`scripts/validate-cursor-plugin.mjs`** and a CI job that runs it on every
  push and pull request, checks both manifests against Cursor's published
  schemas, and fails on a version mismatch between the Claude and Cursor
  manifests. An earlier Cursor plugin lived in another repo and was deleted by
  an unrelated commit that moved the skills directory out from under it; this
  job exists so that cannot happen again unnoticed.

### Fixed

- **The plugin manifests claimed 1.6.4 while this changelog was at 1.8.0.**
  Both are now 1.9.0, and CI fails if they diverge.

## 1.8.0 (2026-09-10)

### Added

- **`using-bulkpublish` skill.** One standalone reference covering the tool
  surface, the shape of a post, every network's character limit and post
  types, the status vocabulary including `partial` and `processing`, plan
  limits, the field-name mistakes that produce a 400, and four worked
  recipes. Written to be readable on its own, with no links, so it can be
  used as a directory listing as well as a skill.

### Fixed

- **`check-quota` published stale free-plan figures.** It claimed 5 posts a
  day, 50 a month, 500MB of storage, 100 API requests a day, 5 labels and 1
  repeating schedule. The real numbers are 3, 30, 100MB, 30, 10 and 0. The
  Pro and Business channel counts were wrong too (15/55, actually 30/75).
  Regenerated from the plan table, and extended with the per-network account
  cap, team seats and RSS rows it never had.

## 1.7.0 (2026-09-10)

### Added

- **Starter prompts.** Eight prompts to paste once the server is connected,
  covering the first things people actually do: audit what is connected,
  draft and queue one post, turn a link into per-network posts, plan a week,
  post an image with caption options, review the next fortnight, retry what
  failed, and read the analytics back. Each uses only the hosted `core`
  profile, so nothing here asks the assistant for a tool it cannot reach.
- **Hosted-server setup for every client.** Each section was stdio-only, so
  the recommended route (OAuth, no API key in a config file) was documented
  only in the general Hosted server section. Every client now shows both.
- **Antigravity, Cline, Warp and Grok sections.** Their config shapes are the
  ones most likely to be guessed wrong: Antigravity accepts only `serverUrl`
  and rejects `url`/`httpUrl`, Cline wants `type: "streamableHttp"`, and
  Warp's dialog takes the server object with no `mcpServers` wrapper.

### Fixed

- **The tool list said 51 under a heading saying 51, beside a table saying
  72.** It was missing hashtag groups, templates, calendar notes, review
  links, client connect links, `share_post`/`unshare_post` and
  `update_media`. Regenerated from the server's own registrations, with the
  20 core-profile tools marked.
- **VS Code described the old `settings.json` shape.** It is `.vscode/mcp.json`
  with a `servers` key, not `mcpServers`.
- **Gemini CLI's remote key is `httpUrl`.** Plain `url` means SSE there and
  does not connect to a streamable HTTP server.

## 1.6.4 (2026-09-09)

### Changed

- **`threadParts` documented as length-checked per part.** The reference said
  only "min 2 parts", so an agent had no reason to expect a long part 3 to be
  refused. It now says every part is measured against every target platform's
  limit, which is what the API enforces.

## 1.6.3 (2026-09-09)

### Changed

- **Instagram `trialReel` documented as Reel-only.** The platform reference
  listed it as a plain boolean, so an agent would happily set it alongside a
  `feed_video` post type, where it has never had any effect. It now says the
  field needs `postTypeOverrides.instagram: "reel"`, and `graduationStrategy`
  is listed with its default and its dependency on `trialReel`.
- **Facebook `shareToStory` and `thumbnailUrl` documented.** Neither was in the
  reference. `thumbnailUrl` is the cover for a video or Reel; Facebook only
  accepts a cover once the video exists, so it is applied after the video
  publishes and lands a moment after the post, and a cover Facebook rejects
  leaves the video up with Facebook's own frame rather than failing the post.

## 1.6.2 (2026-08-22)

### Changed

- Platform count normalized to 15 across the README, the plugin manifest and the
  marketplace entry (they claimed 14 and 16 in different places). Snapchat is
  live; Reddit is excluded from the count while its API approval is pending.

## 1.6.1 (2026-08-20)

- **The tool list said 39; the server serves 51.** The count and the list in
  the README missed `list_platforms`, the three multipart-upload tools, and the
  channel-set and RSS-autopost tools, and still described channel sets, RSS and
  multipart upload as REST-only with "no MCP tool yet" long after each one got
  one. Counted from the server's own registrations this time, both the
  `server.tool` calls and the `registerWidget` ones.

## 1.6.0 (2026-08-19)

- **Snapchat support (16th platform).** Platform lists updated in the README,
  plugin/marketplace descriptions, the channels platform-key list and the
  platform reference, which gains a full Snapchat entry: post types `story`
  (default), `saved_story`, `spotlight`; every post requires exactly ONE media
  file (jpg/png image or mp4 video; Spotlight is video-only, 6-60s); plain
  stories carry no text — the caption is only used as the Spotlight description
  (160 chars) and as a saved-story title fallback. `platformSpecific.snapchat`
  options: `title` (saved-story title, 45 chars), `locale` (Spotlight, default
  `en_US`), `saveToProfile` (Spotlight, default true) — flat or keyed by
  channel ID. Analytics matrix: Snapchat reports impressions, reach, likes,
  comments, shares, clicks and video views; never saves. No first comments.

## 1.5.1 (2026-08-17)

- **The hosted MCP server is documented.** The ChatGPT section claimed
  "BulkPublish does not currently host a remote MCP server" and redirected to
  Zapier — `https://mcp.bulkpublish.com/mcp` has been live for a while, with
  OAuth 2.1 (verified: `/.well-known/oauth-authorization-server` and
  `/.well-known/oauth-protected-resource/mcp` both 200) plus `?key=` for hosts
  without OAuth. Replaced with a "Hosted server" section covering both.
- **Platform list corrected to the 14 that are actually connectable.** Reddit
  was advertised while switched off pending Reddit API approval, and Tumblr —
  which is live — was missing. Verified against `GET /api/platforms/public`.
  The Reddit page in the platform-reference skill is kept but now carries an
  "unavailable" note rather than reading as available.

## 1.5.0 (2026-08-15)

- **schedule-post skill: documented the new `unconfirmed` platform status and
  `retry_post`'s optional `republish` flag.** `unconfirmed` is terminal — the
  publish request may have reached the platform but its response was lost, so
  the post may already be live; it is never auto-retried. `retry_post` on a
  post with unconfirmed platforms and no failed ones returns 400 with code
  `UNCONFIRMED_REQUIRES_REPUBLISH`; pass `republish: true` (default false)
  only after the user has checked the account and confirmed the post is not
  live — it can duplicate the post. Mirrors webapp `bedafa4`.

## 1.4.3 (2026-08-15)

- **get-analytics skill: metric support matrix re-synced with the server's docs
  audit.** Reddit reports likes/comments/shares and Discord reports
  likes/comments — both were wrongly listed as reporting nothing; X now
  reports `saves` (bookmarks). Only Google Business, Telegram and Tumblr report
  no per-post metrics.

## 1.4.2 (2026-08-08)

- **schedule-post skill: `firstComment` is not a real parameter.** The field list documented `firstComment (string)` as a top-level post param. No such field exists in the API or the MCP tools — an agent following this would pass an unknown key and get no first comment, silently. The auto-reply is `_firstComment` at the **top level of `platformSpecific`**. Also noted that it is unsupported on Discord, Pinterest, TikTok, Google Business and Tumblr, where the main post still publishes and the comment is recorded as failed.

- **Corrects 1.4.1: `reddit.thumbnailUrl` is NOT optional.** The previous entry (and the skill) said Reddit video posts fall back to the video's auto-extracted poster frame. They do not — only the Pinterest half of that change shipped. `webapp/src/lib/platforms/reddit.ts` reads `thumbnailUrl` from `platformSpecific` and fails the publish when it is absent, so **Reddit video posts require it explicitly**. The skill now contrasts this with Pinterest's `coverImageUrl` so the two are not assumed to behave alike.
- **platform-reference skill: Reddit options corrected against the handler.** `title` is **not** required (it defaults to the first line of `content`, truncated to 300 chars); `flairText`, `nsfw` and `spoiler` **do not exist** and were removed; `subreddit` accepts `r/webdev` and `/r/webdev`, not just the bare name; `type` was missing. `platformSpecific.reddit` is keyed by **channel ID** (like Tumblr), which the skill never said. Added the derived post-kind rules, the exactly-one-media-file limit, and the two publish gotchas: Reddit returns HTTP 200 on a rule rejection, and media submissions confirm asynchronously with a 20-second timeout after which a post is failed but may still have appeared — verify before retrying.
- **platform-reference skill: Discord's required `channelId` documented.** The section listed no `platformSpecific` at all, so a post built from it would fail with "No Discord channel selected". Added that a connected Discord channel is an entire *server*, that the inner `channelId` is the Discord text-channel snowflake rather than the BulkPublish channel id, the 10 × 25MB attachment limit, and that publishing uses a global bot token — so failures are permission problems, never a reconnect issue. Dropped the mention of webhooks, which the server does not use.
- **platform-reference skill: Telegram documented as taking no `platformSpecific` options.** Added the URL-fetch media caps (5MB images / 20MB video, well below Telegram's upload limits), that content is sent with no parse mode so Markdown is not rendered, and that text over the 1,024-character caption limit arrives as a second message rather than being truncated.

## 1.4.1 (2026-08-08)

- **platform-reference skill: `pinterest.coverImageUrl` is now optional.** The server falls back to an attached image, then the video's auto-extracted poster frame; publishing fails only when all three are missing.
- **platform-reference skill: `reddit.thumbnailUrl` documented, optional.** Reddit video posts fall back to the video's auto-extracted poster frame when no thumbnail is set.

## 1.4.0 (2026-08-01)

- **Link tracking documented across the skills.** `schedule-post` gains `linkTrackingOverride` (tri-state, default `null`) with an explicit instruction to omit it unless the user asked for a behaviour — `false` is a real "post the links as written", not the same as unset. `get-analytics` gains `linkClicks` / `totalLinkClicks`, including the two rules that matter: never add them to the platform's own `clicks`, and read a 0 as "no tracked link clicks" rather than "nobody clicked", since it also covers tracking being off, the post having no links, or shortening being skipped for a channel's character limit.
- README summarises both, alongside the note that shortening can silently not apply on X and Bluesky.

## 1.3.1 (2026-07-29)

- **LinkedIn company pages are gated separately from personal profiles.** `list_platforms` entries can now carry a `variants` object keyed by channel `accountType`; LinkedIn reports `variants.organization` for company pages, which run on a separate LinkedIn app (Community Management API) with its own review. Pages can be paused for new connections while personal-profile posting is fully live — an agent reading only the platform-level `state` would wrongly tell a user LinkedIn is unavailable, or wrongly offer a company-page connect that can only 403. When a variant blocks a write, the `PLATFORM_DISABLED` error carries an `accountType` naming it.

## 1.3.0 (2026-07-28)

- **Per-metric platform support in `get-analytics`.** Every platform entry of `get_post_metrics` now carries `metricsSupported` and `supportedMetrics`, and the engagement response adds `metricSupport` / `supportedTotals` / `partialTotals` / `conditionalMetrics`. All metric columns are stored as integers defaulting to `0`, so a metric a platform never reports was indistinguishable from a real zero — an agent reading the raw response would confidently tell the user "0 impressions" for a Bluesky post, which has no impressions metric at all. The skill now carries the full per-platform table and an explicit rule: **never report a 0 without checking `supportedMetrics` first.**
- **`metricsDisabledChannels`** documented — X reads are billed, so its per-post sync is opt-in per channel and runs at most weekly. Until it is enabled on the Channels page every X figure stays 0 and refreshing cannot change it.
- Documented the two other legitimate reasons a figure is 0: the 6-hourly snapshot has not run since publish, and Facebook's impressions/reach/clicks need the `read_insights` permission.
- Server-side fixes reflected here: Pinterest now reports likes and comments (previously always 0), Threads post metrics now return at all, and Bluesky now reports saves via bookmarks.
- Plugin description corrected: 14 -> 15 platforms (Tumblr, shipped in 1.2.0).

## 1.2.0 (2026-07-25)

- **Tumblr support (15th platform).** `tumblr` added to the platform character-limit map (32,768) and display names. Tumblr posts accept up to 30 images **or** exactly one video — never both in the same post.
- **Platform availability.** Platforms can now be disabled server-side. Post creation targeting a disabled platform returns 403 `PLATFORM_DISABLED` (distinct from `FEATURE_DISABLED`, which means the plan doesn't include it). Posts already scheduled when a platform is disabled are **held, not failed**, and publish automatically once it is re-enabled — no need to delete and recreate them. `GET /api/platforms` reports the current state of every platform.
- Skills and README updated: platform count 14 -> 15, `tumblr` added to the channel platform-key list, and a full Tumblr entry in the platform reference (post type, media rules, and the channel-ID-keyed `platformSpecific.tumblr` options).

## 1.1.0 (2026-07-24)

- **Team approval** documented across the skills and README:
  - `requestApproval` (boolean, **default `false`**) on post create/update holds a scheduled post for team approval; **forced server-side** for API keys of members whose role lacks `post:publish` (contributors), regardless of the flag.
  - Post objects carry `approvalStatus` (**default `"none"`** | `pending` | `approved` | `rejected`), `approvedBy`, `approvedAt`, `rejectionReason`. `approvalStatus` is orthogonal to `status`: the scheduler skips `pending` and `rejected` posts even when scheduled and overdue.
  - New MCP tools `approve_post` and `reject_post` (mcp-server ≥1.7.0, `POST /api/posts/{id}/approve|reject`) — require a role with `post:approve` (owner, admin, approver); 400 if the post is not awaiting approval, 403 without the role, 404 if not found. Rejection takes an optional `reason` (max 2000 chars).
  - `list_posts` gained the `approvalStatus` filter (the approval queue is `approvalStatus: "pending"`).
  - `publish_post` / `retry_post` return **403 `APPROVAL_REQUIRED`** for roles without `post:publish` — agents must submit for approval instead of retrying.
- Tool count updated to 39 (Posts: 12).

## 1.0.5 (2026-07-18)

- **RSS custom-field tokens** in `schedule-post`: documented that a feed item's own extra leaf fields (namespaced or not) can be used as `{fieldName}` caption tokens beyond the standard set; the webapp editor surfaces a feed's real fields as pills after a preview.

## 1.0.4 (2026-07-18)

- **RSS field mapping** documented in `schedule-post`: the new `fieldMapping` field on `POST/PUT /api/rss-feeds` (caption template with item tokens, `mediaField` enclosure selection with media-required-platform skipping, `stripHtml`, `truncate` smart/hard/skip, `hashtags`, per-channel `channelOverrides`; `null` clears to the default).
- Corrected stale note: RSS autopost now HAS MCP tools (`list/create/update/delete_rss_feed`, mcp-server ≥1.5.0).

## 1.0.3 (2026-07-17)

- Documented three new REST endpoint groups (no MCP tools yet) across skills + README:
  - **Channel Sets** (`/api/channel-sets`) — saved channel groups, 50/org cap, names unique per org (409 `DUPLICATE_NAME`), in `manage-channels`.
  - **RSS Autopost** (`/api/rss-feeds`) — 20 feeds/org, `mode` defaults to `draft` (vs `publish` = auto-publish), changing `feedUrl` re-baselines the feed (no backlog flood), in `schedule-post`.
  - **Multipart media upload** (`/api/media/multipart/create|complete|abort`) — 10MB parts with per-part ETags and retry, videos up to 1GB, in `bulk-publish`.

## 1.0.2 (2026-07-16)

- Skills/docs: 14 platforms (added Reddit, Discord, Telegram); Google Business is `gmb`, not `google_business`.
- Corrected schedule/queue-slot guidance: `get_queue_slot` takes only an optional `timezone`.
- Completed the postFormat set (post/video/reel/story/carousel/thread) and the 7-value post status set.

