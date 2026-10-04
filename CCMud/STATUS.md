---
title: Current status
description: HOG terrain-first architecture accepted; production integration planning is next.
reviewed: 2026-10-04
nav: status
permalink: /CCMud/status.html
---

## HOG terrain/water architecture frozen (2026-10-04)

**DESIGN ACCEPTED / FROZEN FOR PRODUCTION PLANNING.**
The [accepted HOG architecture](hog.html#accepted-terrain-and-water-architecture-2026-10-04)
is macro terrain → broad major valleys → Stage 2B-style detail → simple downhill
routing/abstract accumulation → selected water → supported natural features →
shared movement, Cormac/LOOK and World Viewer geography.

Last completed development checkpoint: private Crown-Call `7bf9abc`, committed
and pushed. Two independent regions (DEV seed 867359018957601 and seed 42) validated
the same valley recipe without seed-42 tuning, spill routing or basin flooding.
That checkpoint reported **47 focused/related tests passed** and changed-file Ruff
passed; these are prototype checks, not production integration or deployment evidence.
Full measurements, limitations and rationale are in HOG and the linked private reports.

The prototype remains offline and non-authoritative. Independent hydrology/terrain
reconciliation and drainage-first mountain constraints are no longer the intended
direction. No further experiment sweep is required. No generation behavior,
gameplay, persistence, migration or DEV/PROD deployment changes accompany this freeze.
Live server revisions were not checked for this documentation update.

Next: **HOG Production Integration — Stage 1: Implementation Plan**.
Production integration, generation versioning, cache strategy/performance,
migration, persistence handling and deployment require separate approval.
The five untracked simplified-relief experiment files in the development checkout
predate `7bf9abc`; preserve them separately from this documentation work. They are
unfinished diagnostic work, not the accepted production implementation.

## Mountain certification and viewer jump helper (2026-10-03)

Fixed two independent causes: any spring within the two-mile discovery halo blocked
an entire walking chunk, and shrubland lacked its approved brush mapping. Spring
Source v1 now reserves only a deterministic 60-by-100-foot oval around each source;
its footing remains unavailable, while exterior terrain retains all independent
checks. Source flow and HOG generation remain unchanged. All five supplied mountain
probes certify; tests protect the real source and verify nearby ordinary ground.
The viewer now provides a whole-inch jump command, Copy button and text-entry-safe
J shortcut. See [HOG](hog.html#spring-source-v1-and-mountain-ground-certification-2026-10-03).

Verification: **115 affected tests passed** (9 existing TestClient warnings), plus
**130 runtime/water-safety tests passed** (1 existing warning; one mountain test
appears in both groups). Headless Edge clipboard/coordinate/hotkey checks passed;
changed-file Ruff and whitespace checks passed. Full-suite verification remains the
normal DEV checkpoint, not repeated for this bounded fix. Walking policy version 7
preserves prior identity compatibility; no migration or environment change is needed.
Next: Thomas can deploy and playtest the supplied coordinates. No game deployment
was performed; documentation publication is separate.

## Shared web and terminal prompt modes (2026-10-03)

PROMPT COMPACT/TEXT/OFF and bare PROMPT now run through the shared session for both
clients. The web shows the same server-calculated symbols, colors and percentages beside
command input, and echoes submitted commands with the prompt visible when sent.
Changed prompt metadata travels alongside existing WebSocket events; state updates
refresh the widget in place without transcript spam, replacing typed input or moving
its selection. OFF hides the widget; new connections reset to COMPACT. Compact spaces
are preserved, TEXT can wrap on narrow screens, and the widget has an accessible text
percentage label without continuous live announcements. Existing web HUD and terminal
prompt behavior are retained. No new migration or environment setting is needed.

Verification: **47 focused Alpha/MUD tests passed**, one existing TestClient warning.
These include real plaintext/TLS connections, shared mode commands, authoritative wound/
stamina updates, unchanged-state deduplication, fresh-session defaults and terminal
HUD silence. Headless Edge checks passed for both roles, prompt/input command echo,
input-selection preservation, OFF/TEXT modes, responsive layout and existing navigation/
safe-color behavior. Changed-file lint and diff checks passed. This focused follow-up
did not repeat the preceding full-suite checkpoint. Documentation publication is separate
from game deployment; no DEV or PROD deployment was performed.

## MUD-client Alpha polish and full checkpoint (2026-10-03)

Implemented connection-local PROMPT COMPACT/TEXT/OFF, default COMPACT. Stamina uses
six fixed slots and ceiling of six times the clamped current/configured-maximum ratio.
Health follows the approved aggregate binary-wound thresholds; TEXT uses floored
stamina percentage and floor(100 × max(0, 32 − wound total) / 32). Both compact bars
are red/red/yellow/yellow/green/green from left to right and remain readable without
ANSI. Serialized output batches finish with a prompt; silent HUD ticks never refresh it.
Web clients retain their HUD. Meaningful async speech, observations and hazards remain.

PCs previously had no stored wounds or detailed health command. Thomas approved the
minimal persistence addition: migration `20261003_01` adds an empty wound list for
existing characters, and HEALTH reports stored severity names. No combat, damage,
healing or death mechanics were introduced. INSPECT SELF remains unimplemented.

Visibility investigation found that the HOG outdoor LOOK branch omitted online PCs.
Character records have names, not sdesc/ldesc/full-description fields. The fix includes
PCs using existing ready-terrain visibility, cover/range and structure-barrier checks,
with the same authoritative events for web and terminal. No description editor was needed.
Warning prose remains red while the trailing "You stop." returns to default style.
A stored object description was verified through ANSI/plain/WebSocket rendering and
safe CSS browser rendering; SAY still leaves player # markup literal.

Verification:

- Focused prompt/visibility/presentation and MUD/session suite: **45 passed** before
  the full checkpoint. Enhanced Alpha tests: **29 passed**, including an additional
  concurrent web/terminal HOG LOOK case and blocked/uncertified sight assertions.
- Full checkpoint: **1,390 passed, zero failures, 13 dependency deprecation warnings
  in 1,253.02 seconds (20m 53s)**. No exclusions or test failures were waived. Application
  code remained unchanged during the checkpoint; supplementary test assertions and
  the additional concurrent-visibility case passed separately.
- Real plaintext/TLS socket tests cover login/password handling, character selection,
  delayed certification/automatic entry, prompt modes, HEALTH, literal SAY, duplicate
  claims, ANSI and QUIT. The strengthened ON→OFF→ON socket cases: **2 passed**.
  Session tests cover ticking, stationary HUD silence, STOP, explicit STATUS,
  asynchronous observations/hazards, disconnect-as-STOP and web state preservation.
- Headless Edge checks passed for both roles, safe spans/HTML text, warning/stop colors,
  authored colors, HUD, navigation, account behavior and responsive layouts.
- Changed-file lint, diff checks, dependency checks and single Alembic head passed.
  Repository-wide Ruff has **55 unchanged pre-existing findings**: a clean archive of
  starting commit `3577909` produced the exact same file/code/message/line findings.
  The preserving wound migration was exercised against an existing SQLite character.

Design, command reference and setup instructions describe current behavior. The next
manual acceptance check is prompt placement in Thomas's real Mudlet profile, especially
async speech while editing local input, after a Thomas-authorized DEV update. PC damage
production remains outside this pass. No GMCP, public listener/firewall work or game
deployment occurred. The release includes migration `20261003_01`; normal cc-update
applies it. Do not confuse documentation publication with game deployment.

## Connected terrain preparation during entry (2026-10-03)

A cold terrain cache previously rejected entry and closed the Mudlet connection after
character selection. The shared entry flow now retains authentication and the connection,
shows one preparation notice, reserves the character, and enters automatically after
certification. Pending players remain off-world; commands are not queued. QUIT and EOF
release the pending claim. Ownership, duplicate-login and actual geometry failures still
reject entry. Interior attachment certification can also wait for cold terrain. The web
character dashboard displays preparation without enabling gameplay prematurely.

Verification: 63 focused tests across MUD sessions, terrain cache, cabin, ownership and
rooms passed. The 17 MUD tests also passed after extending real plaintext/TLS socket
coverage to automatic entry without another login. Regression cases include pending
claim exclusivity, wrong ownership, no wait spam, discarded pre-entry commands,
cancellation/disconnect, failure rejection and successful certified entry. Headless Edge
checks passed for both roles, waiting UI, HUD, navigation and desktop/mobile layouts.
Changed-file lint passed; a broader source lint check found six existing findings in
biomes, elevation and worldgen. No migration or environment changes are required.
Code deployment and real Mudlet retest remain Thomas's next step; nothing was deployed.

## Mudlet HUD transcript correction (2026-10-03)

Thomas confirmed real Mudlet login on DEV revision `24ce478` through the loopback
listener/SSH tunnel. The terminal printed every tick's `travel_status`, including
unchanged stationary snapshots. The fix treats unsolicited travel-status/HUD snapshots
as state-only terminal input; web delivery is unchanged. Explicit STATUS replies remain
readable and repeatable. Changing travel snapshots are silent too; observations, STOP
acknowledgments and repeated hazards remain ordinary transcript events. No coordinate
truncation, timing, mechanics, GMCP or infrastructure change is included.

Verification: 15 focused MUD/WebSocket tests passed, with one existing TestClient
warning. Session regressions cover plain/ANSI/web adapters, stationary and changing
snapshots, STOP, repeated STATUS, asynchronous observations, repeated hazards, preserved
web state fields and unmodified engine replies. Headless Edge navigation/HUD checks
passed for both roles and desktop/mobile layouts. Changed-file lint and diff checks
passed. The correction is not deployed; Thomas controls DEV deployment and live retest.


## Traditional MUD adapter and presentation v1 (2026-10-03)

Implemented: shared WebSocket/Telnet session ownership, command/tick/STOP lifecycle,
bounded output delivery, existing-account login/character selection, opt-in loopback
Telnet or direct TLS listener, shared fallback prose, semantic text spans, safe Shadows
color markup, CSS/ANSI/plain rendering and connection-local ANSI ON/OFF. Duplicate
characters are rejected across adapters. Existing registration, character creation,
gameplay, terrain safety and immutable Settings behavior are preserved. SAY stays literal.
No new migration, game deployment, firewall, nginx or systemd change is included.

Verification: 118 focused tests passed in the latest adapter/ROOM/builder/dwelling/object/
Cormac batch; 68 passed in the travel/water/database-concurrency batch; an earlier 108-test
web/auth/authoring batch also passed (counts overlap). Each batch reported one existing
TestClient deprecation warning. New tests use real local TCP, certificate-verified TLS,
mixed WebSocket/Telnet speech and duplicate ownership, async ticks/STOP-before-release,
markup/control/Unicode handling, and bounded/refused protocol input. Browser navigation
and safe DOM spans passed in headless Edge for desktop/mobile, along with travel
presentation and field-command checks. Full-suite and live-client milestone gates remain.

Deployment is separate: the listener defaults OFF. Before remote enablement, select the
DEV TLS port/hostname, provision service-readable certificate/key, verify one application
worker, open only that approved TLS port, and test real client profiles. HTTP health alone
does not verify public certificate trust or reachability. No actual Mudlet/MUSHclient/
TinTin++ GUI acceptance or live server inspection was performed. See the
[design contract](design.html#shared-mud-sessions-and-presentation-v1-2026-10-03) and private
application README for setup, bounds and certificate renewal behavior. GMCP packages,
terminal character creation, extra player-writing systems and account-policy changes
remain deferred. Thomas controls DEV deployment and the full-suite checkpoint.


## Configuration Phase 4: final existing-system conversion (2026-10-02)

**CONFIGURATION CONVERSION PROJECT COMPLETE.** Implementation, final audit and
full-suite verification passed. The earlier Phase 3
regression repair, pushed as `b2ea3cffebed367a7931a51f6c45d762a0513fdc`, established
**1,140 passed, zero failed** and resolved the historical failures described below.

Phase 4 adds 22 restart-required controls with unchanged defaults: interaction reach,
SAY range, local object/MOB/dwelling perception, Work Timer admission backlog,
dwelling authoring room count, 12 terrain speed factors and five uphill speed factors.
Terrain classification/identities, grade boundaries and passability remain code-owned.
The five original conversions and the explicit additional terrain decision are in
[the final contract and audit](design.html#runtime-configuration-phase-4-final-existing-system-conversion-2026-10-02).

Migration `20261002_04` preserves saved/unknown keys and existing game state. Builder
adds seven categories with server-authoritative bounds and restart status. Both
ROOM and template editors read active room-limit metadata. Existing instances and
usable templates remain usable after a limit reduction; modified authoring must
satisfy the active policy. Identity activation, earlier configuration, real-time
stamina, water safety, accepted ROOM costs and authored content remain unchanged.

Verification: **294 focused tests passed** (one existing TestClient warning).
Five browser scripts passed: seven Phase 4 categories, Identity, Travel/World/Stamina,
template authoring and live ROOM forms, including nondefault room metadata and
1280x720 layouts. Fresh Phase 1–4 migration chain produces all 50 keys at revision 4;
saved-value/unknown-key preservation tests pass. Single migration head, dependency
consistency, changed-file lint and diff checks pass. Repository-wide Ruff retains
exactly the same 55 baseline findings, with no new findings.

Final object/ROOM/dwelling rerun: **125 passed**, one existing TestClient warning,
84.80 seconds. Focused counts overlap. The full suite has no exclusions.

Full-suite result: **1,345 passed, zero failed, 13 existing deprecation warnings in 1,214.93 seconds (20m 14s)**.
Implementation commit: `0578531979de68632d10f340ee9d725ca41d9420`.

The audit finds **no known existing global administrator-tunable gameplay/design
constants improperly hardcoded** in the implemented-system scope. No unresolved
user decisions remain. Internal engine/security/safety/presentation/current combat
rules and authored content are deliberately classified in the contract. Future
features must classify and implement their own suitable Settings; no general Phase 5
is planned. No new combat, AI, LOS/SCAN, maps, protocols or HOG generation behavior.
No DEV or PROD deployment is authorized or performed by this milestone.

## Movement and stamina configuration Phase 3 (2026-10-02)

Historical initial checkpoint; the subsequent full-suite repair and Phase 4 status above supersede its pending verification notes.

Implemented/pushed in Crown-Call `18ccaf14e169d605c8791f3e40bc47f0d7ff2226`.
The legacy 24x setting is replaced by separate world.time_multiplier=1.75 and
travel.convenience_multiplier=2. Trudge defaults to 0.85 mph; Athletics now scales
physical land speed. Stamina maximum, recovery, endurance and restart fraction
are restart-only settings. ROOM accepted costs and persisted offline-rate semantics
are preserved. Water uses the shared geographic product with existing physical
swim/current rules. Compact World/Stamina Settings and client maximum/threshold
messages use configured values. Migration `20261002_03` and bounded startup
clamping are included. [Authoritative contract](design.html#runtime-configuration-phase-3-movement-and-stamina-2026-10-02).

Verification: movement/HOG-water/terrain/new-setting run 87 passed; final
settings/stamina/migration/odometer/ROOM group 68 passed (one known baseline test
excluded); HOG walking/navigation/player APPROACH rerun 63 passed plus its final
corrected-import test passed; final migration-preservation and mixed activation
checks 2 passed. Runs overlap. Water movement, prewarm, identity and offline recovery
regressions were also exercised. All 20 new Phase 3 tests passed across focused
runs. Three browser scripts passed: Identity Settings, compact Travel/World/Stamina
at 1280x720, and client navigation with configured maximum/restart messages.
Single Alembic head, migration semantics, changed-file lint and diff checks passed.

Known baseline: `test_rooms.py::test_hog_entry_exit_find_jump_and_reconnect`
expects capitalized `Bedroom` in FIND context, while runtime uses ROOM author key
`bedroom`. Reproduced unchanged on prior main `6c3ef52`; unrelated runtime behavior
was left alone. Old distance/timing assertions were updated to reach the same
boundaries under the new speed. The production guard test now isolates configuration
startup instead of attempting an unrelated database connection; the cabin assertion
uses its current entry ROOM identity.

No full suite: focused and directly affected groups were used rather than duplicating
the roughly 44-minute server checkpoint. Full DEV remains Thomas's milestone gate.
No DEV/PROD deployment. Phase 4 is not started. No calendar, combat, AI, HOG generator
or authored-content changes.

## Travel configuration Phase 2 (2026-10-02)

Implemented/pushed in Crown-Call `3480055528e844e940f15e79ccde08a1d2c8808e`.
Migration `20261002_02` seeds current pace speeds, shared geographic multiplier,
observation cadence and capacity baseline/factor. Builder Settings adds Travel;
Save marks changes pending restart. Identity activation remains identity-only.
[Settings/defaults contract](design.html#runtime-configuration-phase-2-existing-travel-2026-10-02).

Verification: settings/travel/water default regressions 124 passed; nondefault
travel plus HOG-water/prewarm regressions 37 passed; final settings/nondefault
configuration run 40 passed. These runs overlap. Identity and Travel browser
scripts passed, including compact 1280×720 Travel editing, numeric payloads,
restart status and category isolation. Migration seeding and identity preservation,
revision/activation separation, changed-file lint/diff and single Alembic head passed.
Existing TestClient deprecation warning only. No full suite; no DEV/PROD deployment.
This historical Phase 2 checkpoint preceded Phase 3 above. Cormac/HOG branding remains unchanged.

## Runtime identity configuration Phase 1 (2026-10-02)

Implemented/pushed in Crown-Call `4c9187a31a42267feeeebf2fbf722f18dffa1f6f`.
Migration `20261002_01` seeds database-backed game.name/game.short_name. Admin-only
Builder Settings offers revision-safe Save and explicit Activate; client/Builder/API
titles use an immutable cached snapshot. Cormac/HOG, authored content and gameplay
remain unchanged. [Architecture and activation contract](design.html#runtime-configuration-phase-1-identity-2026-10-02).

Verification: final settings/auth/health run 27 passed; Craft Builder regressions
passed in the earlier combined run (36 passed before the additional migration and
startup tests). Settings and Craft/Object browser scripts passed, including 1280×720
layout, revision errors, save/activation separation and escaped titles. Changed-file
lint/diff checks and Alembic single-head check passed. One existing TestClient
deprecation warning; no full suite run. Documentation publication is separate from
game deployment. DEV/PROD deployment remains pending. Phase 2 is subsequently implemented above; Phase 3 requires separate approval.

## Current bounded phase

ROOM filter interaction follow-up: [19a2915](https://github.com/twpaige/Crown-Call/commit/19a2915b2b5165757e3de2b8e8b21055794e7a11)
expands the existing selector while typing (up to six visible choices), opens a
sole match using the existing room-switch handler, and leaves multiple matches
for explicit selection. Clearing restores the full sorted dropdown. Unsaved edits
remain in the draft; filtering never saves. Both relevant headless Edge browser
checks passed, including visible option clicking, focus retention, automatic sole
selection, multiple/no matches, clearing and unsaved-edit preservation. Changed-file
Ruff and diff checks passed. No runtime/schema/loading/architecture changes or deployment.

ROOM-selector correction: [cde95d1](https://github.com/twpaige/Crown-Call/commit/cde95d192e69e01b695e9a632f1401fa9a153408)
updates the existing Objects → Rooms dropdown to show local keys, sorted
case-insensitively, with an adjacent live local-key substring filter. Clearing
restores all options. The subsequent interaction follow-up above adds sole-match selection and visible
choices while retaining the existing room-switch handler. Room names, LOOK,
FIND O and dwelling architecture are unchanged. Both relevant headless Edge checks
(`builder_rooms.cjs`, `builder_dwelling_templates.cjs`) passed, covering mixed-case
prefix sorting, name exclusion, no matches, clearing, selection and unsaved edit
retention, existing save/import flows and template controls. Changed-file Ruff
and diff checks passed. This client-only change required no Python runtime tests,
full suite, schema migration or deployment.

Admin/builder room-key improvements are implemented in
[1cd150a](https://github.com/twpaige/Crown-Call/commit/1cd150abaf222dacd042cb5c0f305707af3fb1ba).
Dwelling instance/template scrolling room lists sort by local key and filter live
by case-insensitive key substring, entirely client-side; clearing restores all
rooms without editing the graph. FIND O uses the room local key instead of its
player-facing name, preserving dwelling context and JUMP FIND identity resolution.
The authoritative [dwelling constraint](objects.html#locked-architectural-constraint-no-nested-dwellings)
now explicitly prohibits nested or ROOM-hosted dwellings permanently. Authored
building interiors in Origin remain ROOMs of the single HOG-anchored Origin dwelling.

Verification: **45 passed**, one existing dependency warning, 39.51 seconds
(`tests/test_admin_find.py tests/test_room_builder.py`). Both headless Edge browser
checks (`builder_dwelling_templates.cjs`, `builder_rooms.cjs`) passed, including a
123-room list, sorted keys, live case-insensitive filtering, clearing, scroll
retention, correct filtered editing and no filter-triggered save. Changed-file
Ruff and diff checks passed. Initial implementation/test-setup failures were
corrected before these final passes. No full suite, schema migration or game
deployment was performed for this update.

Persistent admin character bookmarks are implemented in
[cd4c20c](https://github.com/twpaige/Crown-Call/commit/cd4c20c5546e99801de477f76d8d32ae9cde668a):
MARK, MARKS, UNMARK and JUMP MARK. Names and stable numbers are per-character;
coordinate marks preserve XYZ, ROOM marks preserve exact ROOM instance identity.
Existing JUMP certification/ROOM transition authority is reused. Unsupported saved
heights are refused rather than snapped to surface ground. Migration `20261001_05`
adds marks and persistent number allocation, without changing existing locations.
See [the command contract](commands.html#persistent-admin-location-marks).


Object Builder now offers **Dwelling prototypes** alongside ordinary prototypes
and existing ROOM/dwelling instance forms. A reusable definition contains appearance,
5–100-foot rectangular dimensions, one south entrance, entry local key and ROOM
graph. Interior Rooms now provides Add/Edit/Remove, bulk blank-room creation and
compact ROOM/link/door forms with named destination dropdowns. Text import/export
is optional and uses the same template. Template edits affect future instances only.

Save draft persists incomplete rooms, descriptions, links and missing entry for later
sessions. Validate for Use runs the existing complete server validator; Save usable
prototype revalidates and persists usability. Drafts cannot LOAD. Neither form edits
nor template Save creates live objects. The main editor and room dialog fit 1280×720.

Admin `LOAD O <dwelling_prototype>` outdoors transactionally creates independent
root/ROOM objects, links, door states, entry, Space/Portal geometry and virtual key
grants. Certified dry, level ground and a clear footprint are required. APPROACH
uses existing continuous travel to 15-foot entrance range; ENTER/EXIT use the
instance's entry ROOM and actual doorway. Original Pioneer Cabin geometry remains.

Explicit local door keys pair independently authored link sides. OPEN/CLOSED/LOCKED
plus independent BARRED state is shared; KEY/BAR access is per side. The loading
admin character receives configured instance-scoped virtual keys. Closed doors
prevent departure and cancel delayed arrival without charging stamina. No physical
keys, automatic reverse links or property system.

See [the template and door contract](objects.html#24-reusable-dwelling-templates-and-installed-doors-2026-10-01)
and [commands](commands.html#reusable-dwellings-and-doors).

## Persistence and boundaries

Migration `20261001_04` adds saved Draft/Usable status and preserves existing validated
templates as usable. It changes no live instance state.

Migration `20261001_03` adds template storage, Space origin/placement identity,
shared door state and side controls, and persistent key grants. It preserves legacy
Pioneer Cabin door state, ROOM identities and occupants. Existing command collisions
with LOCK/UNLOCK/BAR/UNBAR are rejected for review before schema changes. Migration
has no destructive automatic downgrade.

ROOMs remain fixed infrastructure with same-ROOM occupancy and the existing pending
travel model. No BUILD CABIN, multiple entrances, rotation/foundations, template
upgrades, general Object/MOB import, indoor combat, door damage, windows, vehicles,
property or deployment/publishing framework was added.

## Verification

Bookmark checks cover replacement, multiple marks, deletion gaps/latest-number
preservation, concurrent same-name saves, disconnect/restart reads, isolation,
invalid selectors/admin authorization, coordinate and ROOM jumps by name/number,
ROOM↔coordinate and ROOM↔ROOM transfers, stale ROOM retention, saved-Z refusal,
queued deletion revalidation and migration constraints. Focused failures were test
fixture setup errors and a ROOM-resolution filter corrected to use exact ROOM
identity before the shared transition path. The final ROOM jump case passes.
Directly affected JUMP FIND and cold/unsafe coordinate JUMP regressions pass.
Changed-file Ruff, whitespace checks and single Alembic head pass. No full suite,
DEV/PROD deployment or live migration. PostgreSQL execution remains unverified.


Catalog follow-up: [fd5ccd8](https://github.com/twpaige/Crown-Call/commit/fd5ccd80ecba561171bb642738b1f2dc9c1e39d7)
hides internal DWELLING appearance snapshots from OLIST using capability/template
relationships. Stable draft/usable template IDs remain visible. No data/schema or
instance-reference changes. **17 focused tests passed** in 7.22 seconds, covering
catalog/LOAD behavior, validation identity preservation and independent instances;
changed-file Ruff and whitespace checks passed. Live DEV rows were not queried.


GUI/draft implementation: [8f9b559](https://github.com/twpaige/Crown-Call/commit/8f9b559ace0c214546c71b9cfbf3dea08222591a), committed and pushed to main.

Focused existing dwelling tests: **12 passed**. Draft/API, migration and affected
ROOM Builder regressions: **30 passed** in 9.70 seconds. The strengthened final
four-room save/reopen/validate/LOAD and round-trip tests: **2 passed** in 2.02 seconds.
Counts overlap. Coverage includes incomplete-draft LOAD refusal, server validation
on usable Save, revision conflicts, four independent instantiated ROOMs, changing
a template back to draft without affecting an existing dwelling, and preserving
previous usable templates during migration.

Three shipped-HTML browser harnesses pass: the full GUI-only four-room workflow,
existing ROOM form/import behavior and existing Craft/Object forms. The new browser
check covers bulk creation, reopen, room/link/door editing, named destinations, entry,
Validate for Use, draft/usable Save, text import/export, removal/cancel, revision
errors and compact 1280×720 layout. Changed-file Ruff, whitespace checks and single
Alembic head pass. **No full suite run.**

Tests use isolated SQLite and mocked browser HTTP responses with separate Python
API tests. PostgreSQL migration execution and live DEV behavior remain unverified.
Existing Starlette/httpx deprecation warning remains. The previous reusable dwelling
runtime is preserved; no HOG placement, travel, door/key/bar or occupancy redesign.

## Deployment and next action

No DEV/PROD deployment, live migration or restart performed. Thomas controls FAST DEV
and full verification checkpoints. After deployment, create a dwelling prototype,
use Add Rooms → Create, Save draft and reopen it. Edit the rooms/links, select entry,
Validate for Use and Save, then LOAD it. Text import is not required. Existing usable
templates remain loadable and existing dwelling instances retain their state.
