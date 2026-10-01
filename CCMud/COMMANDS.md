---
title: Commands
description: Current CCMUD commands and reviewed legacy command candidates.
reviewed: 2026-10-01
nav: commands
permalink: /CCMud/commands.html
---

# CCMUD command reference and adoption review

This is the **CCMUD command-design authority**: what a player or staff member can type now, and which familiar Shadows/SOI verbs CCMUD should adopt as their systems become ready. It does not change game code. “Adopt” below records a design decision, **not** an assertion that a command works today.

The September 28 [historical handoff](https://github.com/twpaige/Crown-Call/blob/main/docs/reference/Shadow_CMD_Bible.md) is the survey input, not a verified inventory of every Shadows handler. Some of its verbs are explicitly conditional (“if identified”) and must not be represented as proven historical commands. The [game design](design.html), [HOG rules](hog.html), [current status](status.html), and actual server behavior take precedence over old examples. In particular, current water movement replaces the former ordinary three-foot wading rule. The server owns game behavior; the web client is one of its clients.

* Contents
{:toc}

## Reading the decisions

| Field | Meaning |
| --- | --- |
| **Live** | Documented as implemented on the current source branch; deployment to any particular server is separate. |
| **Adopt: next** | A useful command for an existing or near-term gameplay system. Still needs explicit behavior, code and tests. |
| **Adopt: later** | A good fit once its owning system exists. Do not invent that system just to add a verb. |
| **Merge** | Retain the useful interaction under another command or movement mode. The historical word may become an alias after review. |
| **Review** | Meaning, historical provenance, authority, access or interaction policy is unresolved. |
| **Omit** | Do not carry the independent verb into CCMUD; the reason appears in the table. |

Only the **Live** section gives current syntax. Examples elsewhere describe intent, not usable syntax or promised aliases. Staff access, object selection, privacy and character permissions are checked on the server.

## Live server commands

These are the documented current source-level commands. Release and feature gates still apply; consult [status](status.html) and the private implementation before asserting what any deployed instance accepts.

| Family | Commands | Current meaning |
| --- | --- | --- |
| Observe | `LOOK`, `L`, `LOOK <object\|mob>`, `LOOK BRIEF`, `LOOK NORMAL`, `LOOK MAXIMUM` | Cormac Eyes describes HERE plus meaningful surrounding terrain, regional biome/cover differences and geographic landmarks, clockwise using absolute eight-point directions. BRIEF/NORMAL/MAXIMUM select from the same observation; MAXIMUM is prose, not RAW. Cold regional features may appear on a later LOOK. A separate Nearby lists supported perceived entities (the audited cabin and supported loose objects), nearest first, with description before parenthesized location, at every verbosity. Verbosity is a session preference, initially NORMAL. Admin RAW remains separate. Temporary ordinary local perception does not imply distant certified LOS. |
| Travel direction | `N`, `NE`, `E`, `SE`, `S`, `SW`, `W`, `NW` and full direction names; `TRAVEL <0–359>`; `north [feet]`, `south [feet]`, `east [feet]`, `west [feet]` | Bare directions set continuous travel. Cardinal directions/aliases with positive feet stop after that distance, subject to all normal safety rules. North is 0°, east is 90°. |
| Travel pace | `TRUDGE`, `WALK`, `JOG`, `RUN`, `SPRINT` | Select land pace, distinct from direction. |
| Stop | `STOP` | Stop active voluntary travel; when swimming, enter FLOATING. Floating does not cancel current. |
| Water | `WADE <direction>`, `WADE`, `SWIM <direction>`, `FLOAT`, `STAND` | Choose water movement or state as permitted by represented depth. See [Water Movement v1](design.html#water-movement-v1-2026-09-28). |
| Preferences | `SET`, `SET AUTOWADE`, `SET AUTOSWIM` | List settings or toggle the two independent, persisted water-travel preferences. Both default OFF. |
| Character | `ODOMETER` | Lifetime ODO and resettable TRIP distances; persistent server-side integer-inch counters. |
| Character | `TRIP RESET` | Reset TRIP only; responds `Trip odometer reset.` without stopping travel. |
| Character | `STATUS` | Current CCMUD status, including travel state; historical Shadows STATUS has different, unresolved semantics. |
| Physical possessions | `APPROACH <object\|mob>`, `GET <object>`, `TAKE <object>`, `INV`, `INVENTORY`, `I`, `DROP <object>` | Slice 1: approach a perceived outdoor OBJECT or MOB through normal movement to interaction range, with ordinary player arrival prose; GET never closes distance and stops voluntary travel even on failure. Right hand first, then left; full hands fail. Inventory shows hands only in this slice. DROP leaves the same object at your feet. See [object architecture](objects.html#14-slice-1-implementation-2026-09-30). |
| Rabbit wound/corpse interaction | `THROW <object> <target>`, `REMOVE <object> <target>` | Throw a held throwable item at a perceived outdoor MOB using opposed RANGE/ATHLX hit resolution. A miss lands the same item at the target; a hit wounds and embeds it. Rabbit MORT creates a corpse retaining that knife. REMOVE recovers from a visible MOB/corpse or own held corpse into a free hand. GET/DROP use normal corpse handling. See [bounded wound/death increment](objects.html#16-throw-wounds-death-and-corpse-2026-09-30). |
| Rabbit corpse processing | `SKIN CORPSE` | With a cutting tool held, consume an accessible rabbit corpse (held or ground) into carcass, raw pelt and guts at your feet. Remove embedded items first; the tool stays in hand. See [bounded SKIN](objects.html#17-rabbit-corpse-skin-2026-09-30). |
| Survival materials | `GATHER FIREWOOD`, `MAKE SPIT` | Existing HOG woodland and supported dry ground supply one firewood bundle or wooden spit at your feet. Each immediately adds five real minutes of Work Timer debt; entry requires debt below 48 hours. MAKE SPIT requires a held cutting tool; GATHER needs none. These commands are persisted definitions executed by the generic [Craft Engine](objects.html#19-craft-engine-v1-and-web-builder-2026-09-30). |
| Places and objects | `ENTER <place>`, `EXIT`, `GET <object>`, `DROP <object>`, `INVENTORY`, `CUT <resource>`, `OPEN <door>`, `CLOSE <door>` | Interact with implemented structures, resources and objects. ENTER can deliberately enter a nearby represented waterway under its current rules; existing feature gates still apply. |
| Communication and session | `SAY <message>`, `HELP`, `QUIT` | Speak to nearby listeners, list help, or close the session. |

For ordinary OBJECT/MOB targets, no ordinal selects the first eligible match in
nearest-first order. Use `2.knife`, `3.rabbit`, etc. for later matches; equal distances
use compass sector then stable ID. GET excludes held items. LOOK inspection,
APPROACH, THROW, REMOVE, DROP and SKIN share this convention.

For the audited Pioneer Cabin on the DEV seed, approach the south-facing entrance,
use `OPEN DOOR` when closed, then `ENTER CABIN`; `EXIT` returns through the same
door. Door reach and intervening walls matter. A nearby valid place resolves before
water ENTER. Ordinary movement still collides with the cabin even with its door open.
Other unaudited structures remain gated; see the [cabin attachment contract](hog.html#pioneer-cabin-attachment-and-regression-invariant-2026-09-28).

The web HUD shows ODO/TRIP in feet or miles. RESET TRIP dispatches `TRIP RESET`.
COPY copies the whole current transcript, including scrolled-out text, and reports
success/failure outside it. These presentation controls never alter game state.

`CLEAR` (typed or its button) clears the **web transcript locally**. It does not send an in-world command or stop travel. Do not assume its availability or effects in a telnet client.

### Web content authoring

Builder/admin accounts open `/builder` from the account screen to create/edit
Craft definitions and Object prototypes in compact **Crafts | Objects** tabs, using validated server APIs. Both offer **Clone**: copied editable data, a new editable ID, revision zero and `Clone of <name>`, with no write until Save and no source changes. Copied Craft commands must still be unique. Aliases are exact commands; the
server rejects conflicts with ordinary game verbs or another craft. Future craft
commands come from definitions, not additional hard-coded handlers. There is no
in-game Builder command language or MOB Builder in v1.

### Staff diagnostics

| Command | Access and role |
| --- | --- |
| `OBJECT <persistent object ID>` | Admin read-only current object identity, placement/parent and hand diagnostics. |
| `OLIST <search>` / `MLIST <search>` | Admin prototype discovery by stable ID, name and keywords. Bare commands show usage, never the catalog. |
| `LOAD O <prototype_id>` / `LOAD OBJECT <prototype_id>` | Admin creation of one ordinary OBJECT at the current feet position or in the current ROOM. Uses normal persistence, placement and timed-morph creation rules. |
| `LOAD M <prototype_id>` / `LOAD MOB <prototype_id>` | Admin creation through shared MOB/world authority at the current valid outdoor location or in the current ROOM; the authored `rabbit` prototype is currently supported. |
| `FIND O <search>` / `FIND M <search>` | Admin search of actual persistent OBJECT or MOB instances, including cold/unloaded locations; 20 results per page. Separate from definition catalogs such as OLIST/MLIST. |
| `JUMP FIND <number>` | Admin placement at the current effective location of an exact displayed FIND instance. Re-resolves identity and physical parents; never moves/extracts the target. |
| `APPROACH <id|feature_id>` | Admin diagnostic steering to known geography or an audited persistent entrance. `approach pioneer_cabin` targets the exterior south door without opening or entering it. All normal safety restrictions apply; no pathfinding or perception claim. |
| `JUMP <direction> <miles>`; `JUMP <x>, <y>` | Admin endpoint jump. Relative distances are miles; absolute XY values are signed integer inches. Destination ground supplies Z. No confirmation: ready valid destinations execute immediately; cold generation completes automatically, or fails safely. STOP cancels a pending request. Presentation/prose is not a placement prerequisite. |
| `HOGDISPLAY RAW` / `HOGDISPLAY PROSE` | Admin session presentation toggle. RAW is diagnostic; PROSE is the normal path. |

### Prototype discovery and loading

`OLIST knife` lists matching OBJECT definitions, for example `hunting_knife — Hunting
Knife`. `MLIST rabbit` lists the authored `rabbit` MOB definition even if no rabbits
currently exist. Search is case-insensitive over stable prototype ID, name and
keywords; multiple words must all match. Results sort by prototype ID. OBJECT
searches show at most 50 matches with a request to narrow broader searches.
Bare `OLIST` and `MLIST` return `Usage: OLIST <search>` and `Usage: MLIST <search>`.

OLIST, MLIST and FIND O/M support a single wildcard: `*` matches any sequence
of characters, including none. `*` alone explicitly requests all matches (catalog
limits and FIND paging still apply). Examples: `OLIST *sword*`, `MLIST *rabbit*`,
`FIND O *knife*`, and `FIND O hunting_*`. Searches retain case-insensitive substring
matching; each search word must match an ID, name or keyword field, and wildcard
segments must occur in order within that field. `%`, `_`, `?` and brackets are
literal characters, not additional pattern syntax. No regex is supported.

`LOAD O hunting_knife` creates one new persistent Hunting Knife, without occupying
a hand or modifying another instance. Outdoor placement uses the same certified
dry-ground, slope and structure checks as ordinary object placement, including a
ground attachment. Cold/unsupported locations are refused rather than guessed.
Indoors, the new object belongs to the admin's current ROOM. Timed prototypes use
the ordinary creation hook to capture their lifecycle deadlines. Specialized
`corpse` instances require the normal death process and cannot be loaded directly.
ROOM/dwelling infrastructure uses validated web Builder authoring; LOAD cannot
create orphan ROOMs or incomplete dwellings. ROOM prototypes are hidden from OLIST.

`LOAD M rabbit` uses the shared MOB creation function and the authored rabbit
defaults: athletics 0, severity modifier +2, no wounds, and a new instance UUID.
Outdoors it uses the same supported-ground authority. Indoors it creates a MOB
in the current ROOM without coordinates; indoor combat remains unsupported.
The small read-only MOB prototype catalog is independent of existing instances;
this feature does not add a MOB Builder or change existing MOB persistence.

Each successful LOAD reports the name, prototype ID and placement. Repeating LOAD
is a new creation request. These commands require an admin account; Builder role
alone is insufficient. They do not edit definitions or create an in-game authoring
language. The web Builder remains the content-authoring interface.

Human-readable prototype IDs identify **what to create**; instance UUIDs identify
**particular existing things**. No numeric vnums are introduced. Ordinary players
continue using names and contextual ordinals. FIND/JUMP FIND remain separate tools
for existing instances.

### FIND instances and jump to results

`FIND O knife` searches existing OBJECT instances by stable prototype ID, name,
and keywords. `FIND M rabbit` searches the current persistent MOB species, name,
and keywords; the current MOB implementation has no separate prototype table.
FIND searches the database independently of active areas and HOG caches. It does
not spawn, move, warm, reconcile timed morphs, or otherwise alter found instances.
Definitions without instances do not produce results. `FIND O *` and `FIND M *`
search all resolvable instances of the requested kind through the same pager.

Results show a temporary number, name, horizontal map distance in feet, compass
direction, and physical state: ground, ROOM, held by a character, or embedded.
Parented objects derive their map position from the current physical parent chain.
Unresolvable parent chains/cycles do not receive invented coordinates. Sorting is
nearest first from the admin's position when FIND begins, with persistent identity
breaking ties. No UUIDs or vnums appear in ordinary results.

Each page contains at most 20 results. When more remain, the footer is
`[ENTER to continue, Q to quit]`. Empty ENTER advances; Q ends paging while keeping
the displayed results available. Other commands leave the pager and execute normally.
Numbers continue across pages. A new `FIND O` or `FIND M` replaces the previous set;
disconnect/restart clears it. Only displayed identities are retained. Pages use live
database queries, so instances that move across the ordering cursor between pages
may be omitted from that traversal; rerun FIND for a fresh ordering. Displayed
instances are not repeated.

`JUMP FIND 27` re-reads the exact instance shown as result 27 and follows its current
location, even if it moved or changed physical parent. Missing instances are reported
plainly. Held and embedded items remain untouched. Outdoor jumps retain existing
Zone C, ground, water and structure checks. Cold destinations prepare through the
existing bounded JUMP process, rechecking identity/location and admin permission
before placement; STOP cancels. ROOM destinations resolve the actual containing
ROOM and its owning dwelling (currently the audited Pioneer Cabin), with unsupported interiors
refused. This admin placement can cross the ROOM boundary without opening a door;
it does not change player ENTER/EXIT rules. Travel stops and teleport distance is
not credited to odometers. Plain `JUMP <number>` is not a FIND shortcut.

The compact web toolbar sends these same commands. `LK` and `STAT` send LOOK and
STATUS. Admins also see PROSE/RAW and compass-arranged N1/N10, S1/S10, E1/E10, W1/W10
(feet), plus authoritative X/Y/Z inches beside Heading. Privileged command checks and
coordinate disclosure are enforced by the server. Buttons disable when disconnected.
Four editable shortcut fields each have a Run button; Enter in a field also runs it.
Their text persists per account in this browser and remains after use. For example,
save `clear` to clear the local transcript repeatedly. Saved commands never auto-run.

### STATUS name collision

**DUPLICATED / NEEDS ADDITIONAL ATTENTION.** Shadows/SOI also used `STATUS`. Keep current CCMUD `STATUS` authoritative. Compare the historical handler in a targeted review before importing any of its fields. Do not silently replace, merge or drop either meaning.

## Adopt next: familiar verbs that support existing gameplay

These are the highest-value additions to the interaction vocabulary, ordered by the system they need. None is marked implemented. Define target selection, permission, failure messages and tests before adding each to the command parser.

| Family | Proposed verbs | Why they fit; design boundary |
| --- | --- | --- |
| Inspection | `LOOK <target>`, `EXAMINE <target>`, `LOOK IN <container>` | Players need deliberate inspection of nearby objects, doors and features. Reuse one perception and target-selection contract. EXAMINE can be an alias or a more detailed view after behavior is settled; do not claim unimplemented distant sight. |
| Possessions | `PUT <object> IN <container>`, `GIVE <object> TO <character>` | Complete GET/DROP with physical transfers. Require actual containers, ownership, nearby targets and atomic persistence. |
| Equipped items | `WIELD`, `WEAR`, `REMOVE` | Equipment matters for weapons, armor and future tool use. Establish inventory slots and Crown's Calling rules first; HOLD may merge into this family. |
| Expression | `EMOTE`, `WHISPER`, `SHOUT` | Preserve the old game's social feel. Delivery must use physical range, hearing, obstacles and permissions; YELL is a candidate alias for SHOUT, not a second sound engine. |
| Tutorial survival | `LIGHT` or `START FIRE`, `ROAST` or `COOK`, `EAT`, `DRINK` | Directly support the planned hunt → skin → fire → roast → eat opening. Define resources, tools, food and fire safety before implementation. Choose one canonical fire/cooking verb and document aliases only when accepted. |
| Survival water | `FILL <container>`, `POUR` / `EMPTY` | Needed for the optional waterskin path. Use actual nearby water, containers and quantities; do not equate potable water with merely represented water. |
| Immediate help | `HELP <command>` | Familiar discoverability; filter staff-only details by access. Current `HELP` alone is live; targeted help needs verification or implementation. |

**Practical order:** inspection and targeting → object transfers and equipment → physically ranged speech → survival tutorial and containers. Each depends on the corresponding authoritative world state, not on a separate old room model. This order is a documentation recommendation, not a code release commitment.

## Adopt later: good vocabulary with prerequisites

| Family | Verbs or capabilities | Prerequisite and decision |
| --- | --- | --- |
| Hunt and gather | `HUNT`, `FISH`, `TRACK`, `BUTCHER` | Adopt as distinct verbs only after creatures, tracks, catches and carcass stages exist. If BUTCHER adds no action beyond SKIN/harvesting, merge it there. |
| Doors and structures | `LOCK`, `UNLOCK`, `KNOCK`, `PUSH`, `PULL` | Adopt with real locks, keys, doors and movable objects. OPEN/CLOSE remain the current base; physical state and access govern effects. |
| Physical handling | `DRAG`, `LIFT`, `CARRY` | Adopt with encumbrance and persistent carried/dragged targets. CARRY may become inventory/equipment state; no duplicate position system. |
| Books and boards | `READ`, `WRITE`, `COPY`, `POST`, `REPLY`, `NEWS` | Adopt with physical pages, writing materials, boards, permissions and provenance. COPY must respect book versus parchment rules. Embedded artwork is content, not a magic command. |
| Orientation | `MAP`, `COMPASS`, map bookmarks | Adopt when persistent map knowledge and possession are defined. Compass and map are different affordances; avoid revealing admin geometry. |
| Groups | `FOLLOW`, `LEAD` | Adopt for consent-aware following, companions and mounts through the same continuous travel controller. No room-by-room follower cloning. |
| Rest and posture | `SIT`, `REST`, `SLEEP`, `WAKE` | Adopt when posture, awareness and recovery states are designed. Existing STAND water behavior must remain valid. |
| Stealth and perception | `HIDE`, `SNEAK`, `SEARCH` | Adopt when observers, cover and detection are modeled in continuous space. SNEAK should modify movement, not fork it. SCAN may merge into LOOK/SEARCH. |
| Terrain actions | `CLIMB`, `CRAWL` | Adopt when certified geometry and movement rules can support them. No invented room exits or bypasses of hazard refusal. |
| Economy and MOBs | `ASK`, `BUY`, `SELL`, `PAY`, `HIRE` | Adopt as MOBs, trade, currency and contracts become real. Keep transactions auditable; ASK requires actual dialogue, not invented responses. |
| Equipment care | `REPAIR`, `SHARPEN`, `EXTINGUISH` | Adopt when condition, materials and fire/light rules exist. MEND is a possible REPAIR alias; LIGHT shares the tutorial fire family. |
| Frontier firearms | `AIM`, `FIRE` / `SHOOT`, `LOAD`, `UNLOAD`, `RELOAD` | Adopt with era-appropriate firearms and Crown's Calling ranged combat. Select one canonical fire verb; do not import Shadows combat or modern weapon mechanics. |
| Combat | `ATTACK`, `FLEE` | Adopt only with CCMUD combat state, opposed rolls, wound rules, explicit intent and safe transitions into/out of travel. KILL is a candidate alias requiring review; HIT/STRIKE should not create parallel attack systems. |
| Riding | `MOUNT`, `DISMOUNT` | Adopt when horses, ownership, terrain and mounted travel exist. Ordinary directional TRAVEL should still own the world position. |
| Magic and faith | `CAST`, `PRAY`, healing action | Reserve vocabulary for deliberately adopted MUD magic/faith mechanics. The tabletop Caps do not themselves create server commands or grant inherited spells. |
| Character and world info | `TIME`, `WEATHER`, `WHO` | Adopt when clock, weather and visibility/privacy policy exist. Report only appropriate player knowledge. |
| Homesteads | `CLAIM`, player `BUILD`, property-description submission | Adopt after claims, construction, ownership, version history and review rules are settled; never reuse old room builder semantics. |

## Review before committing to independent commands

| Vocabulary or capability | Decision needed |
| --- | --- |
| `TELL`, OOC channels, `THINK` | Communication/privacy policy; distinguish private remote messages from local roleplay. THINK may remain narrative only. |
| `NOTES`, `PETITION`, `REPORT`, `WATCH` | Scope, visibility, moderation retention and player/staff workflow. Do not conflate player notes, safety reports and staff observation. |
| `STEAL`, `PICKPOCKET`, lock `PICK` | Theft and PvP consent, inventory ownership, lock mechanics and sanctions. PICKPOCKET may merge under STEAL if meaningful. |
| `TEACH`, `LEARN`, `SPEAK` / language selection | Advancement, languages, literacy and player interactions remain unresolved. Caps and languages are systems, not automatic verbs. |
| `BARTER`, `HAGGLE`, `ORDER` / NPC `COMMAND` | Trade and companion semantics. Resolve the ambiguity with ordinary player commands before reserving names. |
| `USE` | A generic fallback only if concrete verbs cannot clearly cover an action; do not hide every interaction behind USE. |
| `RIDE` | Likely merge into MOUNT plus ordinary direction/pace; retain only if it has a separate player meaning. |
| Group/regiment map transfer, vehicle or boat commands | Need settled objects, ownership and transport; boats are explicitly deferred by Water Movement v1. |
| Character-description customization; names and approval | Ownership, moderation, identity and permissions; keep distinct from exterior/property descriptions. |
| Staff `WARN`, bans, communication blocks, naming/description approval and builder verbs | Adopt capabilities through permissioned, validated and logged administrative interfaces; decide exact syntax when the subsystem exists. CCAI may call the same restricted interfaces, never a privileged database/shell shortcut. |

The historical handoff mentions several of these conditionally. Their appearance here is a **CCMUD design candidate**, not proof that a named `do_xxx()` handler existed in Shadows.

## Merge or omit independent verbs

| Candidate | Decision | Reason |
| --- | --- | --- |
| `TAKE`, `INV`, `I` | Implemented aliases | TAKE uses GET; INV and I use INVENTORY. See the Live section. |
| `YELL` | Merge with SHOUT | One loud-speech range and hearing policy. |
| `HIT`, `STRIKE`, `KILL` | Review as ATTACK aliases | One combat-intent and Crown's Calling rules engine; KILL carries a different intent and needs explicit review. |
| `SCAN` | Merge or review under LOOK/SEARCH | Avoid redundant perception code unless directional scanning has a real distinct function. |
| `HOLD` | Review within equipment | An actual occupied hand may justify it; otherwise WIELD/WEAR/equipment state already covers the action. |
| `RIDE` | Likely merge with mounted TRAVEL | Mount changes locomotion context; heading continues in the shared world. |
| `CRAFT` | Review as recipe/workflow entry | Favor concrete FIRE, CUT, ROAST and other physical verbs where they communicate the task. |
| Separate `ART` command | Omit | `<art>…</art>` denotes content in writing, not an independent magic action. |
| `AUTOWADE` or `AUTOSWIM` as standalone commands | Omit | These are live `SET` preferences, not independent commands. |
| `CORMAC`, Cap names, HOG metadata or admin feature IDs as player commands | Omit | Prose renderer, attributes and diagnostics are underlying systems or data. |
| Old room creation/exits, legacy combat/wound state, old account implementation | Omit old semantics | Coordinate geography, current combat decisions and current account system own these behaviors. Useful builder intent can be adapted separately. |

## Organization and implementation rules

1. Keep the live index above the candidate lists. Each implemented command eventually gets a compact entry with syntax, aliases actually accepted, access, intended behavior, important failure cases and links to detailed rules.
2. Group commands by **player action**: observation/navigation, water, objects/equipment, communication, survival, combat, writing/maps, social/economy, then staff. A verb may act on different target types but should share one clear parser contract.
3. Design disposition (adopt/merge/review/omit) and implementation state (live/planned) are independent. No candidate table grants permission to a player or implies deployed behavior.
4. Reuse continuous coordinates and authoritative HOG geometry for movement, perception, sound and nearby targets. Never turn an uncertified crossing into an implicit success.
5. Use specific failure messages. Ambiguous targets should request clarification; water entry should require the explicit current permission path; missing objects should not create guessed objects.
6. When building new verbs, verify current code and relevant tests, then update this reference and [status](status.html). Maintain separate evidence for source implementation, remote push, DEV release and PROD release.

### Focused open questions

- Compare the real historical Shadows `STATUS` handler with current CCMUD `STATUS` before choosing shared fields.
- Determine whether EXAMINE, HOLD, SCAN, RIDE, BUTCHER and CRAFT merit a distinct action after their owning systems exist.
- Confirm old Shadows provenance and syntax only for commands where it changes a concrete implementation decision.
- Establish communication privacy, theft/PvP consent, staff moderation and travel targeting rules before implementing those families.


### Pioneer Cabin ROOM travel

The cabin now contains Main Room, Bedroom and Loft as owned ROOM objects.
`ENTER CABIN` reaches Main Room through the existing exterior door. Only Main Room
supports `EXIT`; the door must be open. Inside, `north`/`n` moves Main Room → Bedroom
in one real second, and `south`/`s` returns. `up`/`u` moves Main Room → Loft in two
seconds, and `down`/`d` returns. These links cost no stamina. Other indoor directions
fail plainly; they never start outdoor travel.

Departure is echoed immediately and arrival renders normal LOOK. LOOK and SAY do
not cancel the delay. STOP or a conflicting physical/movement command cancels it
automatically; disconnect/restart also cancels without cost. ROOM contents use
membership rather than distance; nearby accessible contents show `(here)` and
ordinal ties use stable identity. Other ROOMs' contents remain inaccessible.
FIND reports nested contents with ROOM/dwelling context; JUMP FIND enters that
actual ROOM without moving the target. See the [POC contract](objects.html#22-pioneer-cabin-room-object-poc-2026-10-01).


### ROOM/DWELLING authoring

Builder/admin accounts use `/builder` → **Objects** → **Rooms** or **Dwellings**.
Select the dwelling, edit its ROOM descriptions/links or entry, and Save. The cabin's
existing rooms are available after migration. The exterior/door association is
read-only; changing a ROOM description is visible on the next normal LOOK.

**Import / Export** provides paste/upload → Validate & review → Import reviewed
text, plus Export saved and Download text. Import merges by local ROOM key and
replaces each supplied ROOM's outgoing links; omitted ROOMs remain. A stale revision
requires reload. No rooms or links are created during validation. See the
[format and authoring rules](objects.html#23-roomdwelling-builder-and-room-text-2026-10-01).
There are no new player/in-game Builder commands, no ROOM deletion, and no HOG
geometry editing. Pioneer Cabin travel defaults above remain the initial authored
layout; builders may deliberately edit its links and entry ROOM through this API.
