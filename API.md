# CARDINAL — API and Event Contracts
Version 1.0 | 2026-11-25 | Internal /api/v1, upstream SAO /api/v1
Normative for daemon, browser and desktop. All local endpoints below are proposed CARDINAL endpoints, not claims of official game APIs.

## 1. Upstream verified contract
Reference: https://saominecraft.com/us/developers (reviewed 2026-11-25). Base https://api.saominecraft.com/api/v1. GET only; Authorization: Bearer <personal key>; JSON UTC ISO8601. Create key in official website profile, select scopes. Server-side calls only; provider refuses third-party browser calls. Key shown once. Limits 60/min/key, 120/min/IP; reads cached 30s; inventory and balance support ETag / If-None-Match and 304. Never place key in React env, URLs, logs or repository.
| Path | Scope | Documented payload data |
|---|---|---|
| /status | none | player {uuid,name}, scopes[], version |
| /me | profile (always granted) | uuid,name,level,xp,playtimeHours,className,job,rank,guild, jobs[],updatedAt |
| /me/inventory | inventory | snapshotId,takenAt,server,sections[{key,size,items[]}] |
| /me/balance | economy | currency,balance,updatedAt |
| /me/transactions | economy | data[] with at,kind,amount,balanceAfter,outcome,sourceType,source,entries; top-level next |
| /me/dungeons | dungeons | records[], recentRuns[] with dungeon,difficulty,success,durationMs,finishedAt,groupSize |

Inventory items: slot,amount,material,id,name,lore[],customModelData,damage,maxDamage. Do not assume lore structured stats or all items have durability. Transactions last 90 days, limit 1–100 default 50, before = upstream next ISO time; preserve signed amounts, check outcome applied before totals. Recent dungeon runs limited to 20; records schema not detailed: preserve bounded raw fields, do not invent rank/time format. Upstream error envelope {error:{code,message}}; missing_key/invalid_key 401, missing_scope 403, no_inventory/not_found 404, rate_limited 429. Honor Retry-After, RateLimit headers, ETag. 304 updates checkedAt, not observedAt. No documented live HP, stamina, mana, cooldown, XP-to-next threshold, coordinates, enemy data or quest objectives. Do not invent endpoints. Class names are provider strings, not fixed enum.

## 2. Local conventions
Base http://127.0.0.1:7410/api/v1, same origin as SPA. In dev Vite proxy preserves origin and uses explicit allowed dev Host/Origin. JSON UTF-8, UTC timestamps, opaque IDs, decimal string for Cols and revision. List endpoints use cursor and limit (1–100, default 50); cursor opaque, validate, stable sort. Do not put credentials in URL/query. All protected endpoints require local session cookie or scoped native bearer. Bootstrap exchange only accepts one-time capability, Host/Origin checks, no CORS wildcard. Mutations require CSRF token (cookie sessions) plus Origin. Read-only native credential cannot mutate except scoped layout commands. Health response no private data. Use 200/201/204, 304 where supported; error codes 400/401/403/404/409/413/422/429/503. Return request ID and redact private upstream response.
```ts
type Source = 'api' | 'manual' | 'demo' | 'approved_observation';
type ResourceStatus = 'fresh'|'stale'|'unavailable'|'unconfigured'|'scope_denied'|'error';
type Meta = { source: Source; status: ResourceStatus; observedAt: string|null;
  fetchedAt: string|null; checkedAt: string|null; revision: string;
  staleAfterSeconds?: number; errorCode?: string };
type Result<T> = { data: T; meta: Meta; nextCursor?: string|null };
type ApiError = { error: { code: string; message: string; requestId: string;
  retryAfterSeconds?: number; fields?: Record<string,string> } };
```
Use data:null when no snapshot. Cached result can be stale/error with data retained; unavailable is not zero. Error messages safe for display. Requests max 256KiB except capped explicit import (10MiB), key max 256 bytes, chat input max 4000 chars, notes max 16k. Return validation errors with field paths. Version changes backward compatible within v1; document breaking v2 separately.

## 3. Endpoint inventory
| Method path | Purpose / request |
|---|---|
| GET /health | liveness {status,version}; no auth |
| GET /ready | readiness {status}; no private state |
| POST /session/exchange | {capability}; sets session, returns CSRF token |
| POST /session/logout | revoke current session |
| GET /session | mode, account id, scopes, CSRF token |
| POST /connection/test | {apiKey}; transient, checks /status, returns masked account/scopes; no save |
| PUT /connection | {apiKey}; validates, saves to vault, starts sync |
| DELETE /connection | disconnect and erase key; cached data remains until explicit deletion |
| PUT /mode | {mode:demo/live}; switch namespace, explicit confirmation for live |
| GET /sync/status | all resources, timestamps, next allowed sync, errors |
| POST /sync/refresh | {resources?: string[]}; 202 accepted/coalesced or 429 cooldown |
| GET /state | atomic overview revision and resource summaries, tracked quest, settings subset |
| GET /profile | latest profile and jobs/guild |
| GET /profile/history | ?from&to&cursor&limit |
| GET /inventory | latest snapshot sections/items |
| GET /inventory/history | paginated snapshot metadata |
| GET /inventory/snapshots/:id | selected snapshot, namespace checked |
| POST /inventory/compare | {items:[{snapshotId,section,slot},...],weights?}; read-only compute |
| GET /economy/balance | balance |
| GET /economy/transactions | ?from&to&kind&sourceType&cursor&limit |
| GET /economy/summary | ?from&to; coverage and applied-only aggregates |
| GET /dungeons | records and recent runs |
| GET /dungeons/history | ?cursor&limit; locally retained |
| GET /quests | ?status&cursor&limit |
| POST /quests | {title,description?,sourceUrl?,steps?:string[]} |
| PATCH /quests/:id | {version,title?,description?,status?,tracked?} |
| DELETE /quests/:id | requires If-Match version |
| PATCH /quests/:id/steps/:stepId | {version,completed} |
| GET /notes | ?entityType&entityId&cursor |
| POST /notes | {entityType,entityId,body} |
| PATCH /notes/:id | {body,version} |
| DELETE /notes/:id | If-Match |
| GET /knowledge/search | ?q&category&limit |
| GET /knowledge/:id | original curated summary and sources |
| POST /yui/ask | {question,includeProfile?,includeQuest?,includeInventory?} |
| GET /settings | user preferences, no secrets |
| PATCH /settings | {version,patch}; allowlisted fields |
| GET /diagnostics | redacted status and capabilities |
| GET /export | explicit user action; JSON export, no secrets |
| POST /import | validated user export, dry-run default; confirm changes |
| DELETE /data | {confirmation,scope}; explicit user action only |
| GET /events | SSE; Last-Event-ID or cursor for replay |

## 4. Representative normalized models
```ts
type Profile = {id:string; upstreamUuid:string|null; name:string; level:number|null;
 xp:number|null; playtimeHours:number|null; className:string|null; rank:number|null;
 guild:{tag:string;name:string;members:number|null}|null;
 jobs:{id:string;name:string;level:number|null;xp:number|null}[]};
type Item = {slot:number; amount:number; id:string|null; material:string;
 name:string; lore:string[]; damage:number|null; maxDamage:number|null;
 customModelData:number|null; parsedStats?:Record<string,{value:number;confidence:'verified'|'estimated'}>};
type Inventory = {snapshotId:string; server:string|null; sections:{key:string;size:number;items:Item[]}[]};
type Balance = {currency:'cols'; balance:string};
type Transaction = {id:string;at:string;kind:string;amount:string;balanceAfter:string|null;
 outcome:string;sourceType:string|null;source:string|null;entries:number|null};
type Quest = {id:string;version:number;title:string;description:string;
 status:'planned'|'active'|'completed'|'abandoned'; tracked:boolean;
 sourceUrl:string|null;steps:{id:string;title:string;completedAt:string|null}[];
 updatedAt:string};
type DungeonRun = {id:string;dungeon:string;difficulty:string;success:boolean;
 durationMs:number|null;finishedAt:string;groupSize:number|null};
```
Settings: theme 'aincrad'|'high-contrast', density 'comfortable'|'compact', reducedMotion boolean, hud {preset,visible,scale,opacity,anchors,safeAreas}, hotkeys, refreshProfile 'active'|'battery', knowledgeLanguage, ai {provider,model,enabled,shareContext}. Validate scale .75–1.5, opacity .45–1, anchors normalized 0–1, safe-area values within screen. Credentials never part of settings. HUD platform status is local capability, not game telemetry.
Overview /state returns {profile:Result<Profile|null>,balance:Result<Balance|null>,inventorySummary,dungeonSummary,trackedQuest,settings,revision,mode}. Capture in consistent transaction. Compare endpoint returns raw known values, derived metrics and assumptions; unknown stats stay null, never calculate definitive DPS on incomplete inputs. Economy summary returns income,expense,net as decimal strings, count and coverage {from,to,complete,limitations}. No guarantee net equals balance change with partial history. YUI returns {answer,citations:[{documentId,title,url,reviewedAt,excerpt}],assumptions,model}; if no provider return 503 provider_unavailable, knowledge search remains useful.

## 5. SSE
Content-Type text/event-stream; no buffering; heartbeat comment every 15s. Event ID revision monotonic string, type e.g. resource.updated, resource.status, quest.changed, settings.changed, mode.changed, reset. Example:
```text
id: 108
event: resource.updated
data: {"revision":"108","resource":"inventory","profileId":"demo-1","at":"2026-11-25T06:00:00Z"}
```
Clients treat as invalidation hints. Last-Event-ID reconnect; replay durable events if retained, otherwise reset event then GET /state; session/mode transitions close/reopen streams to avoid profile leakage. Dedup <= last revision. Exponential backoff 1–30s with jitter. SSE auth via same-origin cookie or native fetch-stream bearer; never put bearer in query. No separate WebSocket needed for one-way sync. Local UI writes via REST.

## 6. Optional upstream webhooks (not MVP)
Official types level_up, balance_change, inventory_change, dungeon_completed. Requires public HTTPS; local loopback cannot receive directly. Header X-SAO-Event, X-SAO-Delivery, X-SAO-Signature = t=<unix seconds>,v1=<hex HMAC-SHA256>. Compute HMAC of exact timestamp + '.' + raw body bytes; constant-time compare, abs clock skew <=300s, validate headers/body type and player identity, dedup delivery ID atomically, enqueue then 2xx within 5s. Invalid signature 401; duplicate valid delivery 204; retries documented after 30s/2m/10m/30m/2h/6h, ten abandoned deliveries or 410 disables hook. Inventory/balance grouped 30s and level update delayed; trigger bounded refresh, not live status. Relay minimal encrypted/authenticated outbound polling, never expose personal daemon. Record delivery id only as long as needed. Test timestamp, raw bytes, bad signature, replay, duplicate and queue failure.

## 7. Test vectors and invariants
Synthetic fixtures for status/profile/inventory/balance/transactions/dungeons, 304, 401,403,404 no_inventory,429 Retry-After, 500, timeout, malformed schema, unknown optional field, empty list, equal-timestamp pagination. Verify scope denial leaves others functional, mode switch isolates data, keys never returned/logged, Origin/Host rejected, optimistic conflict 409, transaction dedup and signed sums, source/timestamp preserved, SSE reconnect/reset, no false live telemetry. Use wiremock or equivalent; no automated requests to real account in CI. Real key verification is manual opt-in. OpenAPI generated from Rust route schemas if practical; TS type generation checked against fixtures. Document every change.
