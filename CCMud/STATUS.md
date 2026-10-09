---
title: Current status
description: Certified JUMP restoration and ASPEED implemented, awaiting DEV certification. World Viewer optimization accepted; Stage 1 COMPLETE; Stage 2 unstarted.
reviewed: 2026-10-09
nav: status
permalink: /CCMud/status.html
---

## Certified JUMP and ASPEED — ordinary-movement regression corrected, awaiting DEV retest (2026-10-09)

Prompt #6.4 was DEV-certified, completed and archived. Thomas approved follow-up
#6.45: restore certification-dependent administrative JUMP and add persistent
Character-specific `ASPEED 1–100`. New JUMPs prepare/certify before relocating;
failure, timeout, STOP and disconnect preserve departure. Existing coordinate,
directional, FIND, MARK, ROOM and authorization rules remain authoritative.

Legacy unresolved rows retain their recorded coordinates and placement marker
until authoritative certification at that XY or an explicitly requested certified
JUMP resolves them. Startup/login retain the narrow recovery exception; no new
markers, invented former positions, silent staging relocation or false footing.

Additive migration `20261009_01` adds constrained integer `admin_speed`, default 1.
ASPEED changes stop travel, persist per Character, and require current Account
admin role. Above 1, movement loses no stamina and movement exhaustion does not
block starting/continuing. Ordinary rules return at 1 or on permission revocation.
No refill or unrelated stamina exemption. Existing pace, ATHLX, load, terrain,
slope, water resultant, world-time and convenience factors remain authoritative.

Every intermediate segment uses existing certification/water/structure/collision
validation. Only for currently authorized effective ASPEED above 1, segments are
bounded to 128 inches and ticks attempt at most 128 iterations
with a 100-ms cooperative elapsed-work budget checked between segments and at
completion. A bounded query already in progress completes before that check.
Processing limits stop at the last validated position with an explanation and keep
the selected multiplier. Cold geography stops safely without automatic restart.
This is safe administrative acceleration, not a promise that 100× can traverse
cold or expensive geography without stopping.

The approved World Viewer optimization remains unchanged, as do HOG seed,
generation formulas/identity, certification, worker scheduling and activation
safeguards. The exact reviewed code/schema compatibility mapping is refreshed;
activation receipts are not rewritten. Local tests and real-provider performance
measurements are documented in Crown-Call's README and performance evidence.
Across three destinations, product-ready 1× ticks took 0.6–1.9 ms and 10× took
3.3–9.8 ms; high-speed first-use certificate work caused safe budget stops in
101–133 ms, including the in-progress bounded query. The existing grade refusal
still stops movement at the negative-coordinate destination. These are local
measurements, not DEV timings or a guarantee of uninterrupted 100× travel.

DEV testing of `600a9e0b2cc3f727b54bc7df32ed0243ae8ab021` found ordinary WALK
stopping at ASPEED 1. The new processing budgets had incorrectly applied to every
Character; an otherwise valid tick exceeding 100 ms stopped even after successful
certification. The correction restricts subdivision, iteration limits, elapsed-time
stops and hazard-backtracking budgets to effective authorized ASPEED above 1.
Ordinary players, admins at 1× and revoked admins retain the original integration,
scheduler-delay, certification, collision/water/slope and stamina behavior. No
migration, higher timeout or weaker HOG check is introduced.

The focused correction suite passed **301 tests**; changed-file lint and exact HOG
compatibility checks passed. Correction verification covers forced slow processing at 1×, exact ordinary
integration behavior, 5/10/100× acceleration and limits, stamina, hazards, cold
boundaries and permission revocation. Production-provider comparisons against the
pre-#6.45 integrator cover NORTH, EAST and distance-limited travel at `(1000,1000)`,
`(1493,2780)` and `(-300000,250000)`, comparing movement state and messages rather
than volatile cache receipt metadata. Private evidence:
`docs/evidence/normal-travel-budget-regression.json`. All nine ordinary movement
comparisons matched; one normal NORTH integration took 141 ms without stopping.
Nine accelerated checks at 5/10/100× also passed: zero-stamina movement at 5/10×,
and explicit last-validated-position budget stops at 100×. These are bounded local
correctness checks using the real provider, not DEV certification.

**Correction not deployed or DEV-certified. #6.45 remains open.** Use normal FAST
DEV and the accepted fix-forward procedure; stop and diagnose readiness failures.

No queue/archive edits, Origin warming, #6.5 or Stage 2 work are included.


## World Viewer optimization — DEV-certified and accepted (2026-10-08)

Thomas deployed implementation `724d7c00dcd6e9ce1027ce2b75c349af418c0dc3`
and reported healthy application, database, MUD and HOG status. DEV testing
confirmed substantially faster new-geography loading, more responsive pan/zoom,
and correct interim rendering: previously prepared geography remains anchored at
its world coordinates, and newly exposed areas fill in after preparation. Thomas
is satisfied and explicitly accepted the World Viewer optimization.

Detail responses select viewport-relevant complete feature records through the
existing generators instead of constructing full regional catalogs. During pending
pan/zoom, the previous response is clipped to its prepared bounds; newly exposed
areas remain blank with the preparing indicator. No quarter-mile pan buffer,
Origin warming, generation/seed/world-identity changes, activation relaxation or
worker-scheduling changes were introduced.

Matched local regional-edge measurements reduced cold viewer latency and queued
gameplay wait from **119.39 seconds to 3.70 seconds**; warm recomputation fell from
**248 ms to 8 ms**. These are local measurements, not measured DEV latency. Thomas's
DEV report independently confirms the practical responsiveness improvement.
Private performance evidence remains in
`docs/evidence/viewer-viewport-integration.json` in Crown-Call.

Thomas accepts the automated correctness coverage and documented limitations:
real production-seed positive lake/landform cases were not independently verified,
although accepted analytic fixtures passed; the historical viewer-hash test failure
also reproduces on the pristine accepted baseline. Those limitations remain recorded,
not silently resolved. Implementation verification included 14 passing viewer-specific
tests, desktop/mobile browser checks and HOG activation checks; the broader focused
run recorded 97 passed, one optional-dependency skip and that pre-existing failure.

**Prompt #6.4 was subsequently DEV-certified and archived.** The accepted viewer
optimization remains separate from the approved #6.45 navigation follow-up. Stage 1 remains
COMPLETE; no Stage 2 or subsequent prompt work has begun. This certification update
changes documentation only and does not deploy a game release.


## Craft drafts, validation and Builder certification — DEV-certified and COMPLETE (2026-10-08)

Craft draft/lifecycle support in #6.3 is DEV-certified, COMPLETE and archived.

Thomas DEV-certified implementation `b4fd87b0e117638720254babe515bd47bf75e1b1`
and explicitly authorized completion and archival on 2026-10-08. Existing Craft
lifecycle compatibility, incomplete DRAFT persistence, invalid activation refusal
and atomicity, successful DRAFT-to-ACTIVE transition, Craft Audit, ACTIVE-to-INACTIVE
retirement, and Content Portability round trips passed. All existing Crafts remained
intact. Thomas accepted the 511 focused automated tests for complex dependency,
unresolved-reference and rollback cases.

DRAFT and INACTIVE definitions persist under their existing stable IDs and cannot
start new execution. ACTIVE definitions use shared activation validation across
Builder and Content Portability. Incomplete fields and unresolved references remain
editable; imports diagnose unresolved dependencies and retain approved incoming
Crafts as DRAFT. Resolving dependencies does not automatically activate drafts.

Prerequisite cycles and DRAFT branches block activation. INACTIVE prerequisites are
allowed with audit warnings. Changes that would invalidate kept ACTIVE dependents
are rejected atomically. Craft Audit is read-only. Retirement preserves Character
knowledge, work timers, physical objects and completed receipt replay. Existing
v1 execution/timing and #6.2's authored-only future fields remain unchanged.

Migration `20261008_02` adds one lifecycle column. It preserves raw definitions and
classifies legacy rows once: valid definitions stay ACTIVE; unresolved/invalid rows
and dependent branches become DRAFT, with their authored aliases/references intact.
Only non-executable derived alias-index entries are removed. No receipts, knowledge,
Characters or WorldObjects are rewritten. Classification requires an online database;
static offline migration SQL cannot determine authored-content validity. Normal
additive DEV fix-forward and exact HOG code/schema safeguards apply; no bridge.

Verification: **511 distinct focused automated tests passed** across Craft lifecycle,
Engine/Builder/authoring, Content Portability, Object Builder/classifications, Wear,
Character Core, dwelling templates/drafts, ROOM Builder and HOG activation. Coverage
includes legacy classification with raw data preservation, actual Alembic upgrade,
atomic imports, dependency protection, read-only audit, blank partial fields, and
completed-receipt replay after retirement. Four Builder browser checks passed:
forms, Craft drafts, Content Portability and dwelling templates; desktop/mobile
controls and unresolved-reference round trips were checked. Changed-file Ruff,
dependency, migration-head and diff checks passed. Tests used isolated SQLite;
PostgreSQL column DDL was compiled, but no live PostgreSQL migration was run.
One unrelated real HOG-worker test was deselected; existing Starlette/httpx and
Alembic configuration deprecation warnings remain. No full suite or game deployment
was run by Codex during implementation. Thomas subsequently reported successful DEV
certification as recorded above; no additional test or deployment run is claimed here.

Stage 1 Builder Foundation and all five Stage 1 priorities are COMPLETE after
Thomas's DEV certification and explicit completion authorization. Prompt #6.3 is
COMPLETE and archived with its specification and acceptance notes preserved.
Stage 2 and subsequent prompts remain unstarted; further work requires authorization.

## Craft prototype authoring foundation — DEV-certified and COMPLETE (2026-10-08)

Craft prototype authoring #6.2 is DEV-certified, COMPLETE and archived.
The existing Builder/JSON/portability paths now support category and canonical command
identity, prerequisite and BASE CAP metadata, separate minute timers, bounded AND/OR
object requirement predicates/modes, and crafter/observer echoes. Current Craft
execution remains unchanged through explicit `legacy_work_timer` compatibility:
`work_seconds` stays exact and independent of authored minute timers. Existing aliases,
receipts, WorldObjects and Character knowledge are preserved. The new BASE CAP timing
helper rounds nearest whole second, ties up, and is not active in gameplay.

No migration is required; Alembic stays `20261008_01`, with an exact HOG code-pin update.
Defaults and the authored-data/runtime boundary are documented in the Object architecture
reference's Craft prototype authoring section. #6.3 draft/activation is DEV-certified, COMPLETE and archived.
Builder and Stage 1 are COMPLETE; Stage 2 is unstarted.

Verification: **433 distinct focused tests passed** across expanded Craft authoring,
existing Craft Engine/Builder, Content Portability, Object Builder/morph/classifications,
Character Core/presentation/LOOK, survival materials and HOG activation. Coverage includes
exact 0/17/27-second legacy execution, half-up timing (including fractional BASE ATHLX),
API persistence, canonical/legacy collisions, rollback, unchanged receipts/knowledge,
read-only legacy normalization and preserving schema checks. Builder forms and content
browser checks passed save/reload, clones, CAP/timers/echoes, AND/OR sampled requirements,
explicit import conflicts, ordinary desktop layout and expanded mobile overflow.
Changed-file Ruff, dependency, Alembic head, diff and reference checks passed. Tests
used isolated SQLite; no live PostgreSQL, full suite or game deployment was run.
One unrelated real HOG-worker test was deselected. Existing Starlette/httpx and
Alembic path_separator deprecation warnings remain. Thomas accepted these tests for existing execution/timing compatibility.

Thomas DEV-certified #6.2 authoring, persistence, minimum CAPs, AND/OR predicates,
all three requirement modes, authored echoes, legacy definition preservation and
Content Portability round trips. He accepted the 433 automated checks for execution
and timing compatibility. Manual execution was impractical because suitable terrain
was inaccessible through the currently unreliable JUMP mechanism; this work does
not claim to fix JUMP. #6.2 completion and archival were explicitly authorized.

## Object TYPE / MATERIAL - DEV-certified and COMPLETE (2026-10-08)

Object TYPE/MATERIAL authoring is DEV-certified and COMPLETE in Prompt #6.1. It uses small developer-owned registries, canonical ordered lists
on ObjectPrototype, empty legacy defaults, existing Builder multi-select controls,
and the shared revision/live-instance/pending-morph safeguards. Classification edits
and imports never change WorldObject identities or placement. FEATURE/ObjectCapability
and exact-prototype Craft execution are unchanged. Migration `20261008_01` is additive
and uses the accepted DEV fix-forward policy; exact HOG code/schema pins are updated.
Prompts #6.1–#6.3 are DEV-certified, COMPLETE and archived. Builder and Stage 1 are COMPLETE.

Thomas DEV-certified existing prototype compatibility, multiple TYPE/MATERIAL
authoring, persistence, clone independence, portability round trips, live-instance
protection and GET/DROP/INV/LOOK ME. Remaining automated coverage was accepted;
#6.1 was marked COMPLETE and archived by explicit authorization.

Object Builder authors multiple TYPE and MATERIAL tags without converting them into
FEATURE capabilities. Duplicates, unknown IDs and malformed lists are rejected;
accepted lists use deterministic registry order. Existing definitions migrate empty
without guessed classifications. Portability preserves both lists in its existing
version-1 envelope; absent legacy fields mean empty lists, with explicit conflict
replacement required to remove existing tags. Live objects and pending morph targets
remain protected against mechanical edits through both Builder and import paths.

Verification: **357 focused tests passed**, covering classification validation,
legacy/default behavior, persistence/reload, live and pending-morph safeguards,
revision/auth protection, portability conflicts and atomic rollback, existing
Craft execution, Wear/visibility/armor/JUNK, dwelling definitions and HOG activation.
Migration checks preserved all old prototype/capability/WorldObject rows, exercised
Alembic from `20261007_03` to `20261008_01` in isolated SQLite, and confirmed exactly
two additive JSON columns in PostgreSQL offline DDL. Live PostgreSQL was not tested.
Shipped Builder browser checks passed controlled multi-selects, selection summaries,
save/reload, clone/removal, ordinary controls, desktop/mobile layout and portability
review/import. Changed-file Ruff and diff checks passed; one Alembic head remains.
One unrelated HOG worker test was excluded. Existing Starlette/httpx and Alembic
configuration deprecation warnings remain. No full suite or game deployment ran.

## Final Stage 1 certification review (2026-10-08)

**Stage 1 COMPLETE — 2026-10-08.** Thomas's #6.3 DEV certification and explicit
completion authorization close the final Builder requirement. All five priorities
are satisfied within the approved foundation scope; no remaining Stage 1 blocker
was identified.

| Priority | Certification finding |
| --- | --- |
| 1. Content Portability | COMPLETE. #1 DEV-certified Object/Craft/dwelling round trips, conflicts and live-instance safety; #6.1–#6.3 extend the same system with accepted classification, authored Craft fields and unresolved drafts. |
| 2. Character Core | COMPLETE within accepted Prompts #2, #3 and #3.1: shared presentation, persistent CAPs, hands/equipment, knowledge and permission foundations. |
| 3. Wear & Equipment | COMPLETE. #4–#6 DEV-certified physical roles, visibility, armor and persistence, with accepted automated calculation/regression coverage. |
| 4. Builder foundation | COMPLETE. #6.1 TYPE/MATERIAL, #6.2 Craft authoring and #6.3 drafts/validation/audit are DEV-certified and archived. Existing Object/ROOM/Dwelling authoring and live-object safeguards are preserved. |
| 5. Persistence & Recovery | COMPLETE within the established discipline: persistent identities/state, revisions, preserving migrations, backups and isolated PostgreSQL restore evidence. DEV-certified persistence and accepted automated atomicity/rollback checks remain applicable; ordinary additive DEV recovery uses the accepted fix-forward policy. |

Craft Learning, progression, full permission wiring, Character combat, containers,
NPC authoring and other deferred mechanics remain outside this accepted foundation.
No new recovery architecture or matched-backup exercise is required. Completion
records Thomas's DEV acceptance and the existing verification evidence; no new
full-suite checkpoint, restore exercise or PROD certification is claimed.
Stage 2 and subsequent prompts remain unstarted, awaiting explicit authorization.

## Armor foundation - DEV-certified and COMPLETE (2026-10-08)

Prompt #6 extends the existing Object Builder and Content Portability with a separate
validated armor capability. Classes are No Armor (+1), Leather (0), Chainmail (-1),
Plate (-2). Abstract regions are HEAD/TORSO/ARMS/LEGS. Thomas clarified strongest
single class per region, without stacking bonuses, then weakest of all four regions;
uncovered regions count as No Armor. Only currently worn instances protect, including
visually concealed armor. Physical wear roles and existing Character presentation remain.

Disposable runtime snapshots provide regional/overall protection and the severity
modifier. Warm reads are query-free. Existing object/capability transactions invalidate
snapshots, including WEAR/REMOVE and JUNK; worn morph deadlines also force refresh.
Login rebuilds from persisted equipment and a restarted service begins empty. No
independently editable or persisted Character armor value exists. The established
wound pipeline remains unchanged; Character combat is not implemented.

Focused automated certification covers the Stage 1 Wear & Equipment checklist:
ordinary wearable/armor authoring and portability, physical roles/capacity, persistence,
visual concealment, shared LOOK/LOOK ME, private INV, armor derivation and reconstruction.
Thomas DEV-certified armor prototype authoring, WEAR/REMOVE, layer visibility and
concealment, reconnect persistence, and JUNK deletion of worn armor. INV and LOOK ME
updated correctly throughout. Automated coverage for armor calculations, coverage
rules, runtime derivation, Builder portability and wound ordering was accepted.
Thomas explicitly authorized completion; later Builder certification is recorded above.
Prompt #6 and Stage 1 Wear & Equipment are COMPLETE. The Builder gap is now closed;
the final review above records Stage 1 completion.

Verification: **417 distinct focused tests passed** across armor, Wear, visibility,
JUNK, Builder/morph, portability, Character Core/presentation/LOOK, MOB wounds, HOG
activation and MUD sessions. The combined run passed 416; the remaining new API test
passed after correcting its authorization fixture to submit a valid body. Invalid
bodies are rejected by existing validation before the route's authorization check.
Shipped Builder browser checks passed armor save/reload, controlled values, ordinary
Craft/Object forms and portability download/explicit import, desktop/mobile layout.
Changed-file Ruff and diff checks passed. One unrelated real-worker geography test
was excluded; the existing Starlette/httpx deprecation warning remains. Tests used
isolated SQLite, not live PostgreSQL. No full suite or game deployment was run.

No migration:
At the armor release, Alembic remained `20261007_03`. The exact HOG destination code fingerprint is updated;
source/schema pins, generation, configuration, placement and receipt safeguards remain.
Thomas subsequently reported successful DEV certification of the implementation
release `5d62469348ed14835d71d17b0eaad56d8c463ce0`. This documentation update does not
deploy or modify application code. No PROD deployment is claimed.

## Separate admin utility: JUNK - DEV-certified and accepted (2026-10-08)

JUNK permanently deletes one selected WorldObject instance using existing keywords,
numbered targeting and account-admin authority. Eligible objects are the acting
Character's held/worn objects or visible ground objects within normal reach.
Infrastructure, embedded objects, parents with embedded children and other
Characters' possessions are refused. Prototype records and other instances remain.
Metadata removal shares the deletion transaction; failed deletion rolls back.

Thomas certified held, worn and nearby ground deletion and unchanged inventory/equipment,
and accepted automated authorization, numbered-targeting and protection coverage.
JUNK is accepted separately from Wear. No additional utility work is required.

## Equipment visibility - COMPLETE after DEV certification (2026-10-08)

Prompt #5 connects current worn WorldObjects and wearable prototypes to the existing
Character presentation pipeline. Strictly outer layers conceal physically concealable
items only where authored visual coverage includes their anchor. Same-layer neck items
remain visible together; non-concealable items remain visible. Concealed garments still
provide their authored coverage. No geometry or implicit anchor coverage is invented.

LOOK/LOOK ME share observer-visible equipment with existing FDESC, PMOTE and held-object
presentation. Scene LOOK uses the same projection. INV retains every held/worn item,
marks concealed entries and preserves registry anatomical/layer/capacity order. Worn
HANDS coverage cannot conceal held objects. Private names and unsupported wound details
remain suppressed. Web/Telnet share server semantic spans.

Observer LOOK reconciles the target's carried timed objects before reading current
prototype coverage/concealability, including disappearance. It releases prior observer
object locks before taking target locks. Distant scene candidates are excluded before
refresh. No persistent visibility cache or new migration is required; schema stays
`20261007_03`. Exact HOG code compatibility pins are updated, geography unchanged.

Verification: **260 distinct focused/regression tests passed** across equipment visibility,
Character presentation/LOOK, Wear, Object Builder/morph, Content Portability, world/ROOM,
HOG activation and MUD sessions. Coverage includes all 16 layer pairings, selective and
cross-anchor coverage, non-concealable items, neck capacity, held-hand separation,
private INV ordering, self/observer equality, indoor/outdoor scenes, reconnect, immediate
WEAR/REMOVE changes, observer-only morph/disappearance and simultaneous mutual LOOK.
Changed-file Ruff, dependency checks, single unchanged Alembic head, diff checks and
pinned documentation verification passed. Isolated SQLite was used; live PostgreSQL
was not tested. The unrelated real-worker geography test was excluded. One existing
Starlette/httpx warning remains. No full suite or game deployment was run.
Thomas certified visible clothing, overcoat concealment, INV markers, self/observer
LOOK, immediate WEAR updates and held/worn presentation; additional layering, selective
coverage, morph freshness and client equivalence have accepted automated coverage.
The earlier hat issue was authored visual coverage, not a code defect. Prompt #5 is
COMPLETE. Prompt #6 and final Stage 1 Wear acceptance are recorded above.

## Wear physical foundation — COMPLETE after DEV certification (2026-10-07)

Prompt #4 implements the canonical 27-location registry, legal layers and capacities,
wearable prototype authoring, WEAR, worn REMOVE and deterministic private INV. Existing
WorldObject identities and Character ownership are preserved; nullable wear-role fields
extend the existing exactly-one-location constraint. Database capacity and free-hand
constraints, serialized transfers and conditional updates protect concurrent changes.
Only single held objects can be worn. Worn objects remain carried weight but cannot
serve as held Craft inputs/tools, THROW items, DROP targets or SKIN tools.

The normal Object Builder supports anchor, layer, visual coverage and concealability.
Existing Content Portability, authorization and live/pending-instance mechanical edit
protections apply. Compatible timed morphs retain the same wear location/layer; terminal
disappearance frees capacity. Incompatible stages are rejected, without relocation.
Coverage and concealability are authored data only: no worn LOOK, concealment, armor,
containers or Character presentation redesign is implemented.

Migration `20261007_03` preserves existing held, loose, ROOM and embedded placements.
It checks for authored WEAR command conflicts before changing schema. Exact HOG code
and schema pins are refreshed; geography and activation receipts remain unchanged.
Normal FAST DEV deployment and fix-forward recovery apply. Thomas subsequently
completed the DEV deployment and certification recorded below.

Verification: 347 distinct focused/regression tests passed across wear, persistence,
capacity/concurrency/rollback, migration preservation, Builder, portability, Craft,
embedded REMOVE/throw, GET/DROP, Character presentation, ROOM/dwelling, HOG activation
and MUD sessions. Builder browser checks passed save/reload, portable validation and
controlled layer choices. Changed-file Ruff, dependency checks, diff checks and single
Alembic head passed. Tests used isolated SQLite; live PostgreSQL was not tested.
The unrelated HOG real-worker geography test was excluded. One existing Starlette/httpx
deprecation warning remains. No full-suite run was needed.

Thomas DEV-certified and explicitly accepted Prompt #4: wearable Builder authoring,
WEAR/REMOVE, hand transfers, full-hand refusal, occupied capacity, numbered targeting,
inventory consistency, reconnect persistence and existing gameplay all passed. Repeated
wear/remove transfers of two identical cowboy hats lost or duplicated no objects.
Prompt #4 is COMPLETE. Subsequent Prompts #5/#6 completed Stage 1 Wear & Equipment.

## Character Core persistent foundations — COMPLETE (2026-10-07)

Prompts #2 and #3 are COMPLETE after Thomas's DEV certification. Shared Character
presentation, LOOK ME/observer LOOK, targeting, hands, movement clearing and private
identity protection remain unchanged. No Wear or presentation redesign is included.

Prompt #3.1 adds the six missing BASE CAP fields (WEAPN, THIEV, SENSE, MAGIC, ACADX,
FAITH), each defaulting to zero, alongside existing ATHLX/RANGE storage. Numeric
values validate 0–20. Existing ATHLX/RANGE values are retained exactly, including
fractional ATHLX and the legacy NULL/unresolved marker; unresolved ATHLX continues
to block HOG activation. No movement, stamina, ranged or combat formula changes.
`available_cap` is nonnegative integer unspent currency, default 0. No earning,
spending, training, credits or starting allocation is implemented.

Characters initially know zero crafts. Unique Character-to-stable-Craft-ID rows
persist knowledge, with transactional query/add/remove helpers. Prototype edits
retain knowledge; duplicate relationships and dangling IDs are prevented. Existing
MAKE eligibility is unchanged. No learning, timers or GRANT CRAFT command is added.

Character ADMIN rank persists as integer 0–100, default 0, and grants no permissions
by itself. A code-owned registry initially contains `communication.say` (ALLOW)
and `builder.content` (DENY). Sparse Character GRANT/DENY overrides resolve explicit
DENY before GRANT before registry default, including contradictory rows. Unknown
names are rejected. These foundations are not yet wired into gameplay; existing
account player/builder/admin roles and authentication remain operational unchanged.
No administration commands, account bans or moderation systems are implemented.

Migration `20261007_02` is ordinary additive DEV work. It preserves existing identity,
presentation, movement, preferences, wounds and object relationships. Exact HOG
code/schema compatibility pins are updated; generation and activation receipts are
unchanged. DEV recovery follows the accepted fix-forward policy, with no special
bridge release or backup exercise.

Verification: **391 distinct focused/regression tests passed** across Character
Core, presentation/LOOK, authentication, Craft Engine/Builder, ranged combat,
stamina, world movement, HOG walking/activation and web/Telnet sessions. Two old
rabbit-migration fixtures were corrected to drop the new RANGE constraint when
reconstructing the pre-RANGE schema; both passed on rerun. Changed-file Ruff,
diff checks, dependency checks and single Alembic head passed. PostgreSQL offline
DDL confirms eight additive columns and two new tables with no data rewrites/drops;
persistence/migration tests used isolated SQLite, not a live PostgreSQL database.
The unrelated real-worker geography test was excluded. One existing Starlette/httpx
deprecation warning remains. No full suite or game deployment was run.

Character Core reevaluation found no additional persistent structural gap within
Prompt #3.1's scope. Thomas certified deployment, migration, persistence, gameplay,
Craft and Builder access, and accepted the new-foundation automated coverage. #3.1
is COMPLETE. Wear & Equipment remains a separate Stage 1 priority. PMOTE authoring, safe observer-visible wound details, description approval,
progression/learning and full authorization wiring remain their separately scoped
follow-ons. Overall Stage 1 completion is recorded in the final review above.

## Stage 1 Content Portability certification — COMPLETE (2026-10-07)

Automated certification passed for existing Object, Craft and dwelling-template
portability. Fixed one validation gap: imported Craft commands are now checked
against the final set of saved dwelling templates, including absent-from-file,
unchanged and kept-conflict templates. Explicitly replacing a template to free
its old command remains supported. No new portable categories or architecture.

Verification: **170 focused Python tests passed** across content portability,
dwelling templates/drafts, Craft Builder/engine and Object Builder/morph coverage.
The shipped Builder content browser check passed at 1280×720: downloads, unsaved
edit guard, explicit replacement/repreview and no premature import. Round trips
preserve authored fields and identities; tests also cover real-session permissions,
changed approvals, pending-morph mechanical protection, rollback of new and replaced
definitions, and unchanged live dwelling graphs, doors, keys and occupants.

Python checks used isolated SQLite. After the exact HOG compatibility-pin correction,
Thomas reported successful DEV certification: all five manual checks plus additional
dwelling-template modification/load tests passed, and test content was restored.
Thomas explicitly authorized completion; Dex Prompt #1 is COMPLETE. The final
review above now records completion of all five Stage 1 priorities.

## Builder blueprint portability v1 (2026-10-05)

Implemented: versioned UTF-8 JSON download/import for Object Builder prototypes,
Craft definitions and complete reusable dwelling templates, including saved drafts.
Export All/Import All use the same envelope and validators. Review performs no
writes; conflict replacements require explicit approval and a fresh preview.
Approved changes commit atomically without creating instances. Live dwelling/ROOM
graphs, Settings and world state are excluded; existing ROOM text tools remain.
[Portable-content contract](objects.html#portable-builder-blueprints-v1-2026-10-05).

Verification includes isolated export/delete/import semantic round trips,
cross-category references, rollback/stale-preview protection, authorization,
existing Builder/Craft regressions and desktop browser download/import checks.
No full suite and no deployment. Human annotations are not persisted; uneditable
legacy engine prototypes are excluded with explicit file notes.

## Production HOG World Viewer restored (2026-10-05)

Implemented/pushed in Crown-Call `db20f79b6c11213a7c284ce84e516212d9bfb0c2`,
based on the reported active DEV commit `fda1d1c`. `/admin/world` reuses its large
viewer controls with worker-generated production samples, coastline and close-up
physical water/natural features. Cold requests are preparing/retryable; legacy
geography remains disabled in production mode. Active-world seed authority and
jump/Copy/J are retained. [Viewer contract](hog.html#production-geography-in-the-world-viewer-2026-10-05).

Verification: viewer/preview/activation regressions **28 passed**; final new viewer
tests **9 passed** (overlapping runs). Worker/product tests produced 20 passes and
one existing diagnostic viewer-hash failure, reproduced unchanged on `fda1d1c`:
`test_hog_products.py::test_real_world_baseline_hashes[0]`. That unrelated stored
hash was not changed. Production and legacy viewer browser checks passed, including
preparing recovery, pan/zoom, coordinates, Copy/J, favorites and no legacy requests.
Changed-file Ruff and diff checks passed. No full suite.

Actual isolated worker smoke checks on seed 42 produced a 1,521-sample continent
view in about 5.9 seconds and a 1,849-sample close-up with two water features in
about 12 seconds, including RAW equality verification. Enqueue returned in about
13 ms and under 1 ms respectively. These are local workstation measurements, not
DEV latency claims. The close-up physical sample and identities matched RAW.

No generation, worker architecture, world state or database changes. No game
deployment. Standard `sudo cc-update dev --fast <commit>` is appropriate; Thomas
controls deployment. This viewer task is complete; no follow-on work started.


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
