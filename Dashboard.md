# CARDINAL — Dashboard and HUD UX Specification
Version 1.0 | 2026-11-25
Design tokens: Design_Topography.md. Backend contract: API.md. Priorities: PRD.md.

## 1. Navigation and app shell
React Router: /overview (default), /character, /inventory, /quests, /economy, /dungeons, /professions, /knowledge, /yui, /settings, /hud-preview, /setup. Topbar includes active profile/class, local sync status, mode marker, quick search (Ctrl+K configurable in browser), HUD launch/preview and settings. Left navigation desktop; responsive drawer/tablet, mobile bottom Home/Items/Quests/YUI/More. Deep links work after direct refresh via SPA fallback only for UI paths (not API). Back/forward and URL filters work. Sidebar collapse, theme, density persisted. Shared StateBanner shows demo, offline, denied scopes or sync error. All private values from API client; source/age visible. Use demo only via explicit mode; no mixed sample data in live mode.

## 2. Routes and requirements
| Route | Core UI | Data / behavior |
|---|---|---|
| /setup | Welcome, local privacy, demo/connect, scoped key field, test, confirmation | /connection/test then /connection; password field, never retain in JS storage, clear after response |
| /overview | Hero profile, 3 metrics, trend, tracked quest, latest items/runs, YUI shortcut | /state, history; raw XP if no target; empty state rather than fake chart |
| /character | Level, XP, class, playtime, jobs, guild, rank, progression | /profile, history; class label from API, no invented XP percent |
| /inventory | Section tabs, grid/list, search, filters, selected details, compare tray | /inventory; actual slot layout from sections; details include lore, quantity, durability when known; /inventory/compare |
| /quests | Journal, filters, tracked quest, checklist, edit dialog, source links | local CRUD; one tracked active quest; explicit manual badge; sync HUD |
| /economy | Cols, income/expense/net, chart, transactions, coverage | balance/summary/transactions; decimal strings, applied-only totals, date filters and export |
| /dungeons | Record cards, run table, success rate, duration, difficulty | records and history; limited upstream history displayed; no estimates for unobserved runs |
| /professions | Job level cards, XP, curated recipe/resource guides | profile.jobs + knowledge; raw XP, no fabricated thresholds |
| /knowledge | Search, categories, source details, starter guide, saved notes | knowledge search/detail; highlight matching terms safely; source URL/version/date |
| /yui | Conversation, source cards, context selection, provider state | POST ask, opt-in context, no gameplay automation; unavailable state links to settings |
| /settings | Connection, theme, overlay layout, accessibility, AI, data/privacy, diagnostics | settings API, separate key controls; explicit deletion/export |
| /hud-preview | Interactive WYSIWYG on original schematic/sample background | shared HudCanvas, anchors, safe areas, presets, keyboard edit; never connect to game in preview |

## 3. Overview detailed layout
Desktop 12 columns: hero left 8, YUI 4; 3 or 4 metrics in row with uneven widths as needed; progression chart 8 and selected quest 4; recent activity 7, inventory highlights 5. Hero contains CARDINAL sigil, adventurer name, class, level, guild optional, raw XP and last updated. No progress meter unless documented denominator. Topbar status shows 'Connected · snapshot age', 'Demo', 'Offline', 'No key', or 'Scope limited'. Metrics: Cols, playtime, last dungeon, optionally rank only when present. Trend use actual timestamped history; one point shows textual empty trend. YUI card offers quick questions ('What should I learn next?', 'Compare my equipment', 'Explain this dungeon') requiring no artificial chat history. Quest card includes source, checklist, pin/unpin and manual label. Activity derived from stored events, not simulated.
At 1024 collapse nav and place YUI below main stats; mobile single column hero -> status -> metrics horizontally in two-column grid -> tracked quest -> chart -> activity. Avoid sticky layers consuming more than 25% height. Provide table alternatives for charts and export.

## 4. Inventory and compare workflow
Sections returned by API, not a hardcoded 36-slot assumption. Present grid with 48–60px cells and list alternative; stable key section+slot. Search item name/material/ID/lore; filter section and known rarity only. Empty slots distinct from absent snapshot. Item detail right drawer desktop, bottom sheet mobile; text lore rendered safely, no raw markup. Show snapshot ID/time and server string only as provided, not current location. Durability = maxDamage - damage if both valid and maxDamage>0, label as snapshot. Comparison: select 2–3 items (same/different snapshot), show raw stats side-by-side, parsed fields only with explicit confidence, highlight known differences, optional user class stat weights and assumptions. If no reliable damage or attack speed, show 'not documented', no synthetic DPS. Equip/Buy buttons excluded, manual notes allowed. Keyboard grid navigation and accessible item name.

## 5. Quest, knowledge and gameplay research
Quest journal supports create/edit, mark step complete, planned/active/completed/abandoned, pin one quest, source URL, notes. Checklist counts are personal, not official status. Pin update transaction unpins prior quest. Optimistic UI allowed with rollback and version conflicts. Knowledge library categories: Getting Started, Controls and Menus, Classes & Builds, Stats & Combat, Floors & Towns, Quests, Monsters, Dungeons, Equipment, Professions, Economy, Updates, Server Rules.
Curate original summaries from official site and wiki; initial guide covers official launcher/Microsoft-owned Java account, tutorial, class experimentation, quests before grinding, teleport crystal if confirmed, equipment/consumables, party/dungeon preparation. Validate current specifics before publishing, cite exact source and review date, distinguish game version and anecdote. Source https://saominecraft.com/us/ ; https://saominecraft.wiki.gg/ ; official news and support links discovered during implementation. Do not conflate anime game mechanics with actual Minecraft implementation. No claims about unreleased classes or floor access without patch evidence. Store articles with title, category, gameVersion nullable, reviewedAt, source URL, confidence, license note and original summary. Search FTS5, filter and show no results with suggestion. No bulk third-party copy. Knowledge should work offline once curated. YUI retrieval uses these articles and consented player snapshot, labels manual and cached data; explain uncertainty, cite links, decline unknown facts, no commands.

## 6. Economy, dungeon and progression
Economy hero Cols plus source/time, signed daily bar chart income/expense, coverage warning if history incomplete, date range presets 7/30/90/custom, table at/kind/amount/balanceAfter/source/outcome, filters and cursor pagination. Chart aggregates applied records only, failed transactions remain visible filtered separately. Timezone selection defaults device. All Cols render locale grouped integer, no monetary conversion. Dungeon records render only fields known; run list name/difficulty/result/duration/party/time, summary denominator visible (e.g. 4/7 observed runs). Class and profession history show actual snapshots; unknown jobs or ranks hide with explicit empty state. Export JSON or CSV from user-owned data (escape spreadsheet formula prefixes for CSV).

## 7. HUD widgets, modes and behavior
HUD uses same data contract and tokens as browser, not a second application state.
| Widget ID | Content | Default |
|---|---|---|
| player | name optional, class, level, raw XP, snapshot age | on |
| quest | pinned manual title, next steps, completed count | on if tracked |
| sync | connected/stale/offline/demo, resource age | on |
| balance | last-known Cols | compact optional |
| session | local companion uptime; game playtime only upstream | expanded optional |
| inventory | last snapshot change/selected item durability | expanded optional |
| dungeon | last run and preparation guide shortcut | expanded optional |
| yui | short user-selected advice or answer snippet, source indicator | on demand |
| notes | pinned personal note | expanded optional |

Presets: Minimal (sync + small player); Adventure (player, quest, sync, optional balance); Review (inventory/dungeon/notes for menu/second monitor, not active combat). No automatic combat/exploration detection absent approved data. Manual preset switch through tray or browser; default no interaction in passive mode. No game action buttons, crosshair, target indicators or hidden coordinates. HUD does not replace official health bar; no live HP/MP/stamina/cooldown unless operator-approved future adapter and source verified. When unavailable do not render ghost values. Use timestamp and warning state. YUI in passive mode shows text only when user requests from browser or explicitly enabled voice later; no unsolicited frequent popups.
Edit mode: visible border, drag handle and resize/scale controls, snap grid, safe-area guides, preview bright/dark surfaces, reset preset, Save/Cancel, Esc exits; passive click-through restored even on error. Browser layout editor accessible alternative with numeric anchors. Native shell tray hide/show, open dashboard, exit; global toggle conflict handling. If click-through unsupported, use second monitor or preview, never silently focus game. Default hidden outside intended use; live use gate as described in PRD. Window selection manual, multi-monitor DPI clamp. Optional focus tracking only using supported OS window APIs and user permission. No screen capture at MVP.

## 8. State and interactions
Query keys ['profile',accountId,mode], ['inventory',accountId,mode,snapshotId], etc. Server owns persisted values; Zustand layout draft/filter state only. SSE event -> invalidate resource, coalesce 200ms, refetch; reconnect shows cached state. Manual refresh button disabled until next allowed time, explains upstream cache. API key mask only after storage, clear input on unmount, no localStorage. All destructive operations explicit confirm and undo only if supported. Optimistic quest mutations roll back on error and handle 409 refresh. Settings save debounced 300ms or explicit Apply for native changes, revert on failure. Keyboard shortcuts documented; respect focus and no global interception in web. Toasts for user actions only; no repeated polling errors. Route error boundary allows recovery. Loading skeleton structurally matches content; never use spinner indefinitely. Empty and scope denial have different guidance. Browser remembers route and view settings, not secrets.

## 9. Acceptance matrix
- Demo fresh install: setup -> overview -> inventory compare -> create/pin quest -> preview shows same quest -> mark step -> both update -> reload persistence.
- Live without key: explicit disconnected, no sample values. With key and only profile scope: economy/inventory/dungeons denied separately, profile works. Invalid/revoked key: safe error, cached snapshot marked stale.
- Network offline/429: cached data retained and labeled, controls usable, retry respects server. ETag 304 does not imply new observation.
- 360/390px mobile, 768 tablet, 1024,1440,1920 desktops: no overflow, keyboard reachable, labels correct, chart table alternatives. Reduced-motion and high contrast usable.
- Native Windows real environment: overlay transparency, click-through, hide shortcut, edit escape, focus, DPI/multi-monitor, borderless game/standalone test; permission gate. Linux explicitly report unsupported mode and fallback. Verify FPS and idle resource budget; no claim based only on browser preview.
- YUI without provider: search and source library functional; when enabled, citations and context permission visible; unsafe/injected instructions in lore/knowledge treated as data.
- Visual evidence: screenshot each main route, mobile overview/quest, HUD compact and edit over original demo background. No fake screenshots presented as live gameplay.
