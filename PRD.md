# CARDINAL — Product Requirements Document
Version 1.0 | 2026-11-25 | Status: implementation baseline
Owner: repository maintainer | Product: independent SAO Minecraft companion

## 1. Vision
A polished, local-first RPG companion for the existing Triggers Studio SAO Minecraft game. One personal character, one local daemon, two synchronized interfaces: an external transparent desktop HUD and a full localhost browser dashboard. YUI is an optional evidence-grounded gaming adviser. The official game client stays unchanged. The app is unofficial and must not imply endorsement.

## 2. Problem and users
The game client is optimized for play, not long-term planning, gear comparisons, economic analytics or reviewing mechanics. Primary user is a solo adventurer who also plays parties and wants glanceable guidance without obscuring combat. Secondary workflow: reviewing progression on a second screen or after closing Minecraft. Default target Windows 11; Linux/Hyprland supported via an adapter or separate browser display. Mobile browser design is responsive; remote access is not enabled by default.

## 3. Success criteria
- Fresh clone can run a seeded demo with no SAO account, cloud services or AI provider; actual live mode only after user enters a valid key.
- One backend owns state and serves both surfaces; changes to tracked quest, notes, settings and imported snapshots appear on both within 1 second on localhost (not an upstream freshness promise).
- Native HUD toggles between click-through and edit mode, hides instantly on request and does not steal focus in play mode.
- User can determine source, last update and whether data is stale or unavailable; never fabricate HP, XP percentage, cooldowns or quests.
- User can inspect inventory, plan a quest, compare known item stats, review Cols transactions, inspect dungeon history and ask a cited gameplay question.
- No SAO API secret in frontend, logs, Git or browser storage. No game process modification, network interception, input automation, or hidden game data access.
- Beautiful, consistent, accessible dark UI: no default admin template, no excessive neon or illegible text.

## 4. Scope and priorities
| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| P01 | P0 | Local backend and persisted state | SQLite migration, health endpoint, clean restart; single instance |
| P02 | P0 | Setup/demo | Guided onboarding, fixture badge, key test, revoke/remove local key, scope display |
| P03 | P0 | Official API | Profile, inventory, balance, transactions, dungeons; partial scope handling; cached data |
| P04 | P0 | Browser overview | Character summary, level/XP raw, balance, last dungeon, selected quest, sync states |
| P05 | P0 | Inventory | Search/filter sections, item lore, durability when supplied, compare selected items |
| P06 | P0 | Quest journal | User-defined quests/checklists/notes; tracked quest shared across views |
| P07 | P0 | External HUD | Compact preset, manual visibility, click-through/edit, safe areas, position persistence |
| P08 | P0 | Design system | Shared React components and tokens, keyboard navigation, 360–1920px browser |
| P09 | P0 | Security & privacy | Loopback default, origin checks, secret vault, no game hooks, clear data deletion |
| P10 | P1 | Economy | Signed movements, pagination, verified totals, category filters and history coverage |
| P11 | P1 | Dungeons | Records/recent runs, success and duration metrics with coverage disclaimer |
| P12 | P1 | Progression/professions | Historical snapshots, jobs, guild, class labels as returned by provider |
| P13 | P1 | Knowledge | Curated gameplay articles, FTS5 search, source/version/review date |
| P14 | P1 | YUI | Read-only retrieval and advice, citations, local Ollama optional, no AI fallback required |
| P15 | P1 | Settings | Theme/density/scale/keybinds/refresh, diagnostics, export/import personal data |
| P16 | P2 | Linux HUD | Hyprland layer-shell prototype, explicit supported environment, browser fallback |
| P17 | P2 | LAN / mobile | Explicit opt-in authenticated TLS access; threat-model and test before release |
| P18 | P2 | Webhook relay | Optional public HTTPS relay, signed delivery validation; no default tunnel |
| P19 | GATED | Local observation | Screen/log reading only after documented server approval and opt-in; no injection |
| P20 | P2 | Voice | Push-to-talk, local speech where available, explicit microphone permission |

P0 release is a useful vertical slice; P1 completes the personal companion. P2 must not delay P0. Exclude full server recreation, automated combat, macros, trading bots, packet inspection, memory readers, player tracking, hidden entity detection, competitive targeting, and automatic game input. No unsupported live HP/MP/stamina/position/minimap. No required SAO login password handling; create the official personal API key on the provider site.

## 5. User journeys
1. Launch daemon -> open localhost -> select demo or connect -> test scope -> first snapshot -> open HUD preview -> choose compact layout -> open actual overlay only once permitted.
2. During game: show selected quest/checklist and snapshot level, Cols, sync status; keyboard toggle hides overlay; edit in dashboard or explicit edit mode; native game remains focused.
3. After play: review inventory, compare visible values, inspect Cols and dungeon runs, mark personal objectives complete.
4. Learn: open Knowledge, choose starter path/class/professions/boss guide, check citations and update date; optionally ask YUI.
5. Offline: cached snapshot remains visible with age, manual notes still writable, reconnect without duplication.

## 6. Data provenance and eligibility
Official source: https://saominecraft.com/us/developers (verified 2026-11-25). Personal read-only account endpoints; upstream cached 30 seconds, inventory is snapshot-based. Keep upstream observedAt distinct from fetchedAt. Local/manual and demo records never masquerade as API facts. A disabled or denied scope is an explicit UI state. Upstream values are not estimates of live combat. Unknown XP target means raw XP only.
Game knowledge: https://saominecraft.com/us/ and https://saominecraft.wiki.gg/ ; manually validate pages, patch dates, content license and access before ingesting. No bulk scraping by default. No claims of exact current class balance without evidence. Check current Code of Honour and obtain operator confirmation for live overlay/client observation before enabling it; browser companion and standalone demo preview can be built without game modification.

## 7. UX and quality budgets
See Design_Topography.md and Dashboard.md. Web initial route target < 2s on developer PC, p95 local cached read < 100ms, SSE state updates < 1s, idle HUD target < 2% CPU and < 200MB RSS incremental (measure on hardware, not guarantee), no GPU-heavy continuous animation. Reduce motion and preserve game frame rate. At least WCAG AA for primary text and keyboard-accessible browser workflow. Use signed releases only after a release pipeline exists.

## 8. Release milestones
M0 scaffold and demo (P01,P02,P04,P08); M1 real connector and inventory/quest (P03,P05,P06,P09); M2 native Windows HUD P07 with preview/demo and permission gate; M3 analytics, knowledge, YUI (P10–P15); M4 optional Linux/remote/webhooks/voice (P16–P20). Each milestone has a build, focused checks, screenshots or manual smoke evidence. See AGENT.md for execution.

## 9. Risks, decisions and open questions
- Actual game UI and approved overlay behavior must be checked with the operator. No official telemetry for HP/MP/coordinates/quest state.
- Windows exclusive fullscreen may not display a normal external window; use borderless/windowed or second monitor.
- Wayland does not provide general access to global window positions; use user-positioned output overlay or browser fallback.
- Localhost is device-local. iPad access is deferred until secure LAN mode exists; never expose daemon by default.
- Upstream schemas and gameplay change. Contract adapters tolerate unknown fields; version knowledge.
- No actual account key or game client supplied; demo and contract fixtures must be labeled and real verification explicitly deferred.
- Working product name CARDINAL and original visual motifs are chosen for this design; use no ripped game assets or trademarked logos. Re-evaluate name before public distribution.
