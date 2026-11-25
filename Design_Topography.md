# CARDINAL — Design Topography & Typography
Version 1.0 | 2026-11-25 | Visual specification for React dashboard and overlay
The requested filename intentionally remains Design_Topography.md. Design topology, type hierarchy, color, motion and component rules live here.

## 1. Art direction: Aincrad Glass
A premium futuristic adventurer's workstation: restrained holographic cyan on near-black ink, blue-steel layers, precise hairline borders, glass-like surfaces, subtle orbital geometry and an occasional warm amber accent. Inspired by the feel of an RPG menu, not an imitation of copyrighted SAO screenshots or logos. Design original sigil: open hexagon with offset orbit and small diamond. Use CSS/SVG geometry and Lucide icons, no scraped game assets. Strong information hierarchy; dashboard should feel calm and sophisticated, HUD invisible when not needed. Avoid giant headers, endless uniform cards, gradient text everywhere, gratuitous glow, heavy backdrop blur or game-obscuring opaque panels.

## 2. Tokens
| Semantic token | Dark theme value | Usage |
|---|---|---|
| --bg | #080F1B | application canvas |
| --surface | #101B2B | panels |
| --surface-raised | #172538 | popovers, selected |
| --surface-hover | #20334A | hover |
| --border | #2A4055 | decorative dividers |
| --border-focus | #79E5F5 | focus ring |
| --text | #EDF6FF | primary |
| --text-muted | #A4B8CB | labels and metadata |
| --text-subtle | #91A6BC | secondary labels, verify contrast |
| --accent | #73E3F3 | active control and system status |
| --accent-strong | #32B9D5 | graph accent / non-text |
| --accent-bg | #112E3D | selected background |
| --gold | #F5CA7A | Cols / achievements |
| --success | #80E1A9 | completed |
| --warning | #F8CB78 | stale / degraded |
| --danger | #FF9B9B | error |
| --demo | #C7B5FF | simulation |
| --hud-bg | rgba(8,15,27,.88) | default HUD panel |

Test contrast actual rendered backgrounds; primary text >=4.5:1, large text >=3:1, control boundaries/focus >=3:1 as appropriate. Never use color alone: status icon + text. Text on solid cyan buttons uses --bg. High contrast variant: near-black #03070D, border #688399, text #FFFFFF, muted #C3D2E0, accent #A0F3FF, opaque HUD. Do not put translucent text directly over game world; dark scrim under it. Accent gradients limited to thin selected border or hero orbital sigil, no large bloom.

```css
:root {
  color-scheme: dark;
  --bg:#080f1b; --surface:#101b2b; --surface-raised:#172538;
  --surface-hover:#20334a; --border:#2a4055; --border-focus:#79e5f5;
  --text:#edf6ff; --text-muted:#a4b8cb; --text-subtle:#91a6bc;
  --accent:#73e3f3; --accent-strong:#32b9d5; --accent-bg:#112e3d;
  --gold:#f5ca7a; --success:#80e1a9; --warning:#f8cb78; --danger:#ff9b9b;
  --demo:#c7b5ff; --hud-bg:rgba(8,15,27,.88);
  --font-ui:Inter,system-ui,-apple-system,BlinkMacSystemFont,sans-serif;
  --font-display:Space Grotesk,Inter,system-ui,sans-serif;
  --font-mono:JetBrains Mono,ui-monospace,monospace;
  --r-sm:6px; --r-md:10px; --r-lg:16px; --r-xl:22px;
  --shadow-panel:0 8px 32px rgba(0,0,0,.24);
  --duration-fast:120ms; --duration-normal:220ms; --duration-slow:360ms;
}
```
Self-host appropriately licensed font subsets, retain license notices, no external font network call at runtime; fallback works offline. Use Tailwind CSS variables mapped to semantic tokens; avoid hardcoded hex in features.

## 3. Type system
| Role | Size / line-height | Weight / treatment |
|---|---|---|
| Display hero | 32/38 desktop, 26/32 mobile | Space Grotesk 600, -0.03em |
| Page title | 24/30 | 600 |
| Section title | 18/24 | 600 |
| Card title | 15/21 | 600 |
| Body | 14/21 | Inter 400 |
| Small metadata | 12/17 | 450, muted |
| Overline | 11/16 | 600, uppercase, tracking .09em |
| Metric | 28/34 | 600, tabular numerals |
| HUD primary | 14/19 base | 550, tabular numbers |
| HUD secondary | 12/17 base | 450 |
| Code / diagnostics | 12/18 | mono |
Use lowercase readable labels, uppercase only small identifiers. All numerals align with font-variant-numeric:tabular-nums. Avoid text smaller than 12px except nonessential overline. HUD scale .75–1.5 user-configurable, default 1; test actual legibility at 1080p and 1440p. Localization en default; support Thai fallback via system Noto Sans Thai when available, no fixed truncation on translations.

## 4. Spatial system
4px baseline: 4,8,12,16,20,24,32,40,48,64. Desktop nav 224px expanded / 72px collapsed, topbar 64px, content max 1600px, gutters 24–32px, 12-column grid gap 20px. 1024–1279 nav collapses and 8 columns; 768–1023 drawer + 6 columns; <=767 4 columns, gutters 16px, bottom nav for Home/Items/Quests/YUI/More, safe-area inset. No horizontal overflow at 360 CSS px. Panel padding 16–24; HUD 10–14. Radius 6 controls, 10 compact panels, 16 main, 22 hero. Borders 1px; subtle elevation only where needed. Use visual grouping rather than every widget a card.
HUD safe areas: 32px edge inset or user calibration, leave central 40% width x 45% height clear, reserve top/bottom official interface and chat; default widgets compact top-left + right quest + small bottom-right sync (user can move). Grid snaps 8px in edit, normalize positions, preserve at DPI changes; no overlay over game hotbar. Panel widths 200–300px at scale 1, max 25% viewport where possible. In compact mode cap panel count and hide empty/unavailable blocks unless relevant. HUD background opacity .88, user adjustable but ensure text scrim.

## 5. Component language
AppShell, SideNav, TopBar, StatusPill, ProvenanceLabel, Metric, HeroProfile, Sparkline, Timeline, SegmentedTabs, SearchField, FilterChip, EmptyState, Skeleton, ErrorBanner, ItemSlot, ItemDetail, CompareTable, QuestCard, Checklist, SourceCard, YuiOrb, ChatMessage, HudCanvas, HudPanel, HudEditHandles, SafeAreaGuide, KeybindField. Radix for dialogs/popovers/select/tabs with keyboard and focus management; native semantic tables and forms. Buttons primary cyan fill, secondary outlined, tertiary quiet; destructive text/icon plus confirmation. Use Lucide 16px inline, 20px actions, 24px nav. Item rarity tint based on explicit official/parsed value only, unknown neutral. Charts cyan primary, gold economy, muted gray baseline; direct labels, tooltip, accessible table alternative, gaps for missing samples, no false zero.
Each data component handles skeleton, populated, empty, stale, unavailable, scope denied, error, and demo; no skeleton flash under 150ms; stale retains value with timestamp; errors show retry; scope denied explains key permissions. HUD status uses short labels 'Snapshot 2m ago', 'Offline', 'Demo', never 'LIVE' for cached API. Screen reader status announcements polite and throttled. Focus ring 2px offset 2; target >=44px mobile and >=32px desktop (HUD passive has no interactive controls).

## 6. Motion and visual effects
Motion values: 120ms hover, 220ms panel/route transition, 360ms drawer/orb state; easing cubic-bezier(.2,.8,.2,1). Enter opacity + translate 4px, exit fade; avoid moving layout during combat. Numbers update without bouncing or fake interpolation; optional chart animation once on load. HUD no perpetual animation by default; YUI orb animation only when actively thinking/speaking, 1.5–3s slow ring, pause when hidden; no fake status. Reduced-motion honors OS setting plus in-app toggle: disable transform/loop, immediate transitions. No continuous blur, shaders, background videos, canvas particle fields in HUD. Browser hero can use lightweight static SVG orbit lines and animated status dot only when meaningfully active. Use compositor-friendly opacity/transform. Measure game FPS with and without overlay.

## 7. Visual layouts (schematic)
```text
DESKTOP / 1440
┌───────────────┬─────────────────────────────────────────────────────────┐
│ ◇ CARDINAL    │ AINCRAD / Overview              Sync • 1m   Settings   │
│ Overview      ├──────────────────────────────────────┬──────────────────┤
│ Character     │ ADVENTURER  / Lv 08                  │ YUI   ◉          │
│ Inventory     │ Class • XP raw • guild               │ Ask / suggestion │
│ Quests        ├───────────┬───────────┬──────────────┼──────────────────┤
│ Economy       │ Cols      │ Playtime  │ Last dungeon │ Tracked quest    │
│ Dungeons      ├───────────┴───────────┴──────────────┤ Checklist        │
│ Professions   │ Progression trend / history         │                  │
│ Knowledge     ├──────────────────────────────────────┴──────────────────┤
│ YUI           │ Recent activity               Inventory highlights     │
│ Settings      │                                                         │
└───────────────┴─────────────────────────────────────────────────────────┘
HUD / borderless
┌──────────────────────────────────────────────────────────────────────────┐
│ [◇ Level / Class]                              [TRACKED QUEST / manual] │
│ [Snapshot age]                                  [Step 1 / Step 2]       │
│                                                                          │
│                         clear gameplay center                            │
│                                                                          │
│ [optional Cols]                                     [◇ sync / YUI hint] │
│                reserve official Minecraft hotbar and chat                │
└──────────────────────────────────────────────────────────────────────────┘
```
Hero visual: original line-art hexagon/orbit motif with subdued grid, not an anime character illustration. Use subtle diagonal cut corner on section index marker only; keep panels easy to read. No filler stats. A demo fixture may show believable values clearly labeled, but live missing values use em dash.

## 8. Design review acceptance
Capture 1440x900, 1920x1080 HUD, 1024x768 tablet and 390x844 mobile screenshots. Check alignment, overflow, contrast, reduced motion, keyboard navigation, stale/empty and error states. Check HUD transparency on bright forest and dark dungeon demo backdrops, click-through and edit escape. Verify no copyrighted art, no network fonts, no unrelated branding. Any visual changes update tokens or components consistently. See Dashboard.md for information architecture and route acceptance.
