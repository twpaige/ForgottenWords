---
title: Game design
description: The MUD's accepted decisions, preserved in full and separated from tabletop rules.
reviewed: 2026-09-28
nav: design
permalink: /CCMud/design.html
---

## Authoritative design record

The existing **[CCMUD_Design.txt](/CCMUD_Design.txt)** remains the detailed game-design source. It is preserved in full, including its consolidation and numbered decisions. This installation does not migrate, shorten, or reorganize that record.

For the existing illustrated reading edition, see [CCMUD_Design.html](/CCMUD_Design.html). That older HTML is a separate legacy presentation, not an automatically generated version of the text. When wording differs, consult the text and current explicit decisions. The new Markdown-to-HTML publishing system applies to this `/CCMud/` knowledge base; it does not silently convert old pages.

The record begins with consolidated decisions C01–C11, followed by preserved numbered decisions. Read its reconciliation instructions before interpreting older entries. Old questions are historical records and do not automatically reopen a completed design interview.

## Admin navigation and testing (2026-09-28)

APPROACH can resolve the audited Pioneer Cabin's exterior door point through ordinary
travel. Cardinal commands accept optional feet through the same integrator; bare
directions remain continuous. JUMP retains relative miles and adds absolute comma-separated
XY inches with authoritative destination ground Z. Ready destinations execute immediately;
cold destinations complete automatically after certification, without a second command.
Missing prose never grants or denies placement; genuine geography failures still deny it.
Admin HUD coordinates and compact PROSE/RAW/precision controls use server state and
commands. LK/STAT shortcuts are available to all players. No browser movement engine,
new generator, global safety bypass or deployment is included. The
[field utility contract](hog.html) owns units, cancellation, permission and safety details.
Four editable command shortcuts retain their text after use and save per account in
the current browser. Run/Enter uses ordinary command dispatch, including local CLEAR;
stored commands never execute automatically.

## Pioneer Cabin correction (2026-09-28)

The existing cabin/door/ENTER behavior is restored through a narrowly reviewed HOG
ground attachment, preserving persistent coordinates and the full collision footprint.
Only the exact seeded layout with certified dry local geometry qualifies. Nearby LOOK
can expose this audited structure; OPEN requires real door reach without an intervening
wall, and ENTER requires an open portal. Place resolution precedes water entry.
Interior transitions stop outdoor travel and survive reconnect. This is not general
structure certification, targeted LOOK, or continuous interior movement.
The [HOG cabin contract](hog.html#pioneer-cabin-attachment-and-regression-invariant-2026-09-28)
owns the audit conditions, regression cause and invariant.

## Cormac v1 (2026-09-28)

Cormac is a deterministic server-side wilderness LOOK narrator: **HOG supplies facts;
Cormac classifies and describes them.** It does not generate geography, decide movement
permission, implement LOS, or call an LLM. The existing LOOK command and transport remain
in use; ordinary prose gets a descriptive title and body. Admin `HOGDISPLAY RAW`, explicitly enabled admin coordinate diagnostics and
periodic travel observations keep their existing diagnostic/presentation paths.

`LOOK` defaults to NORMAL. `LOOK BRIEF`, `LOOK NORMAL`, and `LOOK MAXIMUM` (also `L`)
select a session preference; following plain LOOK uses it. Disconnect clears it.
There is no database migration or new persistent preference framework. Targeted object
LOOK remains a separate future command milestone.

The temporary input adapter assumes ordinary perception for nearby geographic features,
using ready local water/restriction shapes and nearby candidates from the existing
background discovery catalog. The default narration radius is 1,000 feet. Unavailable
LOS and diagnostic perceived=false do not suppress these local geographic candidates.
No distant 50-mile view, people, objects, or unaudited structures are promoted into prose.
Cold geometry/catalog work is queued through existing bounded mechanisms, never awaited
by LOOK; descriptions can gain a landmark when its source becomes ready. Certification
and physical safety gates remain unchanged. A future perception layer should replace
candidate selection and continue supplying the same immutable Geography/Landmark input.

Cormac v1.1 merges local/regional representations by authoritative HOG feature ID
before landmark selection and rendering, preferring local geometry for relationships.
Distinct IDs remain distinct even at identical distance/direction; distance bands still
refresh as the character moves. Light-woodland titles use **Among Light Woodland**.
Other title wording, prose caching, movement and RAW diagnostics are unchanged.

Local Waterway Geometry membership, depth and current are authoritative; stale top-level
RAW water strings are not narration inputs. In-water descriptions come first, followed
by local terrain and nearby landmarks. Prose is playerless: it describes the scene and
what exists here without addressing "you". It invents no weather, species, sounds,
smells, lighting, water color, rocks, mud or physical bank geometry. Unknown or ambiguous
bank/egress distances are omitted. Existing floored depth prose is retained (1-6 feet;
below 1 shallow; >=7 deep), rather than copying the reference example's near-three-foot
rounding. Reliable existing deeper-water orientation is included at MAXIMUM.

Classification lives in `src/crown_call/cormac.py`, not HOG. `ProseConfig` centralizes
thresholds, distance words, local radius and cache bounds. Initial defaults:

| Fact | Descriptive classification |
| --- | --- |
| Tree cover | <15% open grassland; 15-<50% light woodland; 50-<75% woodland; >=75% dense woodland, for grass/woodland surfaces |
| Absolute slope | <0.5% nearly level; <5% gently sloping; <15% sloping; otherwise steep |
| River/stream width | >=100 ft broad |
| Distance | <25 ft a few feet; <75 roughly fifty; <150 roughly a hundred; <300 roughly two hundred; <750 roughly five hundred; otherwise roughly a thousand within the local radius |
| Direction | Eight compass points, nearest 45-degree sector |
| Current | Existing qualitative Water Movement v1 bands; no exact mph |

Simple hysteresis holds the previous distance bucket within 10 feet of an edge, tree
cover within 3 percentage points, slope within 0.1 percentage points, current within
0.05 mph, detailed tree-cover estimates within 2 percentage points of ten-point rounding boundaries, and compass direction an extra 3 degrees beyond its ordinary sector. Water
membership and depth descriptions are not delayed by hysteresis. These are descriptive
choices, not changes to terrain speed, swimming thresholds or current physics.

BRIEF includes immediate terrain/water and the nearest landmark. NORMAL adds vegetation
context, local current/channel orientation and up to two nearby landmarks. MAXIMUM adds
up to four landmarks, rounded tree-cover estimates and reliable deeper-water orientation. Sparse scenes stay short;
no word-count padding. A richer scene should supply more useful detail, not atmosphere.

A scene signature contains descriptive terrain/slope, grassland context, selected
landmark identities and classified relationships, and local water/depth/current facts.
Raw coordinates, elevation fluctuations, diagnostic flags, exact distances and hashes
are excluded. Same signature plus verbosity returns the same cached Description.
Small changes such as 188->181 feet, 37->38% tree cover and 109->106 degrees stay stable;
water entry/exit, significant terrain changes and landmark changes update the scene.
The process cache and per-character scene memories are bounded at 256 entries each.
Recent scene signatures and recent generated language are separate eight-entry deques;
these are expansion hooks, not a grammar/variation engine. Cache hits do not vary prose.

Expand vocabulary/thresholds in ProseConfig and the small renderer, and geographic inputs
in HogGame.describe_look. Cormac v2 should first improve the allowed-feature/perception
input and useful landmark detail, then add deterministic language variation for genuinely
new scenes. Full travel narration, advanced LOS, weather prose and LLM generation are
outside v1. No game deployment accompanies documentation publication.

## Water Movement v1 (2026-09-28)

Riffles are descriptive, nonblocking natural features. Their metadata remains available
for discovery and prose; traversal is governed by the underlying represented waterway
geometry and existing water movement rules. Riffle extents do not reject walking chunks.
Other unsupported natural features retain their certification restrictions.

FLOATING does not itself imply geographic movement or periodic travel observations.
Automatic movement observations require a changed accepted authoritative position since
the previous movement observation (or initial position before the first). Stationary
shallow/zero-current floating and certification-blocked drift produce no periodic
travel/RAW output. Explicit LOOK/STATUS, transitions and safety messages remain available;
observations resume after accepted displacement resumes. STOP while SWIMMING says
"You stop swimming and begin floating." without a generic stop message. ENTER-water
completion says "You wade into the river and stop." (using the resolved feature kind),
without internal geometry/certification language. These are presentation fixes only.

The server's existing TravelController now owns LAND, WADING, SWIMMING and FLOATING.
This supersedes ordinary <=3-foot water walking; certification is geometry, not
permission. HOG geography, the 25-foot depth cap, deterministic currents, ground Z,
0.1-second integration and configurable 15-second observations remain unchanged.

| Mode | Precise legality | Geographic movement | Stamina |
| --- | --- | --- | --- |
| LAND | Certified dry terrain | Existing selected pace, terrain and slope | Existing rules |
| WADING | Represented water, depth <4 ft | Existing pace; no current displacement | Existing ordinary movement rules |
| SWIMMING | Represented water, depth >1 ft | 1 mph effort plus authoritative current vector | Existing JOG recovery/drain |
| FLOATING | Any represented water depth | Current only above 1 ft; zero displacement at <=1 ft | TRUDGE recovery component; no movement drain |

The deliberate 1-to-4-foot overlap preserves the chosen mode. Exactly 1 foot
cannot be swum; exactly 4 feet cannot be waded. No feature label, minimum width,
minimum duration, crossing-success heuristic or artificial loop prevention changes
these rules. A narrow deep strip and repeated encounters with the same river are valid.
Selected land pace persists through water; swimming uses its specified effort speed.
Existing runtime travel-time conversion is retained; the separate future clock redesign
is not implemented here.

Commands are server-side: `WADE <direction>`, `WADE`, `SWIM <direction>`, `FLOAT`,
`STAND`, `STOP`, `SET`, `SET AUTOWADE`, `SET AUTOSWIM`, `STATUS`, and `ENTER <water feature>`.
WADE without a direction and STAND enter WADING without initiating movement when
local depth permits. SWIM in shallow water explains an actual WADE command.
FLOAT works even in an inch of represented water, but fails on dry land.
STOP while swimming enters FLOATING; STOP while floating does not cancel current.
Swimming exhaustion also enters FLOATING, visibly. Restart requires one integration
interval of net JOG stamina. Floating recovery is the existing TRUDGE gross recovery
component (90% of standing at current rules), without TRUDGE's movement drain.

AUTOWADE and AUTOSWIM are persisted per-character booleans, both initially OFF.
Plain SET lists them; each SET name toggles it. AUTOSWIM never implies AUTOWADE.
With AUTOWADE OFF, ordinary travel stops before the bank; manual WADE authorizes
entry. AUTOWADE ON continues LAND -> WADING. At 4 feet, AUTOSWIM ON with sufficient
stamina continues WADING -> SWIMMING; otherwise stop before invalid water.
At <=1 foot SWIMMING becomes WADING: continue with AUTOWADE ON, otherwise stop and
explain WADE. With both enabled, direction survives LAND -> WADE -> SWIM -> WADE -> LAND.
Manual wading with AUTOWADE OFF stops on exiting to land and needs a new direction.
Automatic settings never select FLOATING.

**Disconnect acts like STOP**, as explicitly approved: swimming becomes persisted
FLOATING. No offline drift is simulated. Elapsed offline time applies normal resting
recovery, capped at maximum; rapid reconnect cannot instantly restore stamina.
Reconnect resumes floating/current behavior. Persisted swimming recovered after an
unclean disconnect also becomes FLOATING. Offline characters receive no recovery ticks.

ENTER resolves a nearby represented channel by kind or stable feature identity,
reports ambiguity rather than guessing, and uses ordinary checked movement to the
first representable wadeable point just inside its bank. It stops there in WADING;
it neither teleports to the center nor crosses the whole feature. Unsupported water
nouns do not create geometry. Admin JUMP uses endpoint certification and explicit
wading legality (<4 feet), preserving the existing placement-only scope.

Ordered Waterway Geometry events intercept banks and 1/4-foot contours inside each
movement interval, including exact four-foot touches and narrow bands. Full shapes
are gathered from a bounded certified chunk corridor and deduplicated by feature,
preserving repeated crossings and seam continuity. No new intersection mathematics
or water sampling grid is introduced. Independent cliffs, structures, unsupported
water/terrain and incomplete chunks remain blocked. Safety interception can halt drift;
STOP itself is not an anchor. A new valid movement/FLOAT command retries after a safety stop.
Dangerous non-water consequences remain gated.

Normal prose floors depths 1-6 feet; below 1 is shallow water, >=7 is deep water.
Current is described by natural compass and deterministic qualitative bands (below
0.25/1/2/3/4 mph: barely moving/slowly/steadily/briskly/swiftly; otherwise strongly).
Exact values remain internal/RAW. STATUS identifies mode, effort direction, depth,
current and resulting geographic movement. LOOK adds reliable local deeper-water
orientation for a single unambiguous nearby channel; ambiguous bank directions are
omitted. Depth/current prose is change-driven on the existing observation cadence,
not a full LOOK every integration step; mode changes echo immediately.
The web client renders server text and contains no water rules. Server command tests
exercise behavior independently of browser rendering; no new Telnet transport is added.

No drowning, knockdown, current-induced wading displacement/hazard, skill/failure roll,
panic, unconsciousness, temperature, wet clothing, armor/encumbrance swimming penalty,
buoyancy, rapids/waterfall consequence, rescue, boat, tide, wave, undertow, surf,
automatic compensation, intelligent crossing or hydraulic simulation is introduced.
Lakes/coasts/oceans still need compatible certified geometry; absolute water-surface
and riverbed Z are not invented. No game deployment accompanies this implementation.

## Water geometry correction (2026-09-28)

Historical prerequisite scope; Water Movement v1 above now supplies player behavior.

Waterway Geometry v1 remains authoritative: maximum depth is now min(width/5,25)
feet, with the existing parabolic bank taper and unchanged deterministic currents.
[Geometry certification and ordered segment queries](hog.html#water-geometry-integration-2026-09-28)
are separate from player movement permission. No WADE/SWIM/FLOAT commands or new
water movement rules are implemented by this integration pass. Keep 0.1-second
integration and configurable 15-second observations; older 5/30-second references
do not supersede the current implementation.

## World time, travel and seasonal light (2026-09-27)

**Settled design, not yet implemented:** the leading September 27 continuation in
the [authoritative design record](/CCMUD_Design.txt) now owns the 1.75× world-time
target, separate 2× normal travel convenience (future 0.25×–2× pressure range),
and smooth seasonal daylight anchors. It supersedes conflicting older values;
read its rationale, cadence, gameplay boundaries and implementation caveats before
reopening these decisions. A complete world-calendar service and dynamic travel
controller are not present in the inspected application.

The [HOG efficiency decisions](hog.html#step-10-efficiency-decisions-and-evidence-2026-09-27)
distinguish chunk dimensions, terrain representation and certification, retain
500/25 production geometry, and define the recommended query/perception separation.
The [evidence index](evidence.html) preserves both full investigations separately
from these maintained decisions. Benchmarks are evidence, not release authorization.

## Finding a decision

After the September 27 continuation, the design text preserves Thomas's
**2026-09-26 terrain movement and offline rest decisions**: configurable surface/grade factors, independent precise
boundaries and elapsed-time offline stamina recovery. These supersede the old
on-foot factor-below-0.50 impassability rule, without redesigning horse/wagon rules.
The following **2026-09-26 continuous
travel and stamina addendum** supplies the 150-pound reference weight, persistent selected
pace, continuous recovery/drain, endurance targets and the 20% start threshold.
This supersedes the older deferred values and recovery heartbeat; consult it
before reopening a movement question.

| Topic | Starting point in the design text |
| --- | --- |
| Starter inventory, survival, crafting | C01 and C02 |
| Sight, recognition, watching, trails | C03 |
| Movement and encumbrance | C04 |
| Combat foundation and encounters | C05 and C06 |
| Surrender, prisoners, mortality, unconsciousness | C07 |
| Death aftermath and inheritance | C08 and C09 |
| Preserved foundation and reconciliation | C10 and C11 |
| Original detailed decisions | Preserved numbered decisions after the consolidation |

This index points to decisions; it is not a replacement summary of them.

## Relationship to tabletop rules

The [TTRPG rules](/CC_Ruleset.html) govern the tabletop game. They are inspiration and input to CCMUD, not automatic instructions to change the server whenever a tabletop page changes.

When adopting a tabletop rule, record the source section/revision, the MUD behavior accepted by Thomas, any adaptation, and what existing behavior must remain. MUD-specific decisions never flow back into tabletop rules implicitly. HOG has no role in the TTRPG rules.

## Architecture and present state

Thomas's 2026-09-26 Step 10 decisions are recorded in [HOG](hog.html#step-10-mud-integration-and-persistence).
Current generated geography supersedes stale cached geography while persistent
objects survive. This supersedes any older implication that exploration freezes
terrain. DEV now supports certified dry/gentle HOG walking with bounded ahead-of-travel
materialization. Hazard consequences and unresolved terrain remain gated. Thomas
chose admin-only RAW diagnostics for distant geography in the current milestone;
certified distant LOS/player discovery remains separate. Waterway Geometry v1 defines analytic channel banks, depth/current and explicit
water modes under Water Movement v1. Inferred lake footprints retain unresolved-interior exclusion. No complete swimming or
drowning design is inferred from discharge/width. Automatic safety stops explicitly
say `You stop.` See the current Step 10
exploration contract at the beginning of HOG for the accepted scope and limitations.

The bounded field-testing utilities are documented in [HOG](hog.html#bounded-field-testing-commands):
client-only CLEAR, exact-heading TRAVEL, admin diagnostic APPROACH, and admin JUMP
to a certified destination's GROUND elevation. Heading/APPROACH reuse continuous
travel; JUMP is explicit test placement and does not certify the intervening route.
These utilities and the simple speed/heading status display do not implement the
prose generator, distant perception, crossing consequences, or new geography.

Use [HOG](hog.html) for detailed generator architecture and [Status](status.html) for current progress. Historical progress notes in the design text do not establish the current implementation or live environment state.

The existing [world-design page](/CCMud.html) and [design-gap notes](/CCMUD_Holes.txt) remain available for context. Treat older gaps as historical until reconciled with accepted decisions and current code; they do not override the consolidated design record.

## Updating design

Make focused edits to the authoritative record when an approved decision changes. Keep durable rationale beside the decision. Update dependent architecture only where affected, and update STATUS when development state changes. A major restructuring of the legacy design record is a separate task.

## Ravine containment and exterior certification (2026-09-27)

Thomas approved [Ravine Reservation Contract v1](hog.html#ravine-reservation-contract-v1-2026-09-27).
It is a prospective permanent containment promise: all future ravine effects must
fit inside its deterministic rotated rounded rectangle, returning deformation
and gradient to background terrain by the outer boundary. Expansion requires a
version change. Catalog H is a vertical construction budget, not surveyed depth.
Current gameplay certifies qualifying exterior terrain while the entire reservation
remains blocked. The safety boundary is not a perceived rim. No interior traversal,
climbing, falling or crossing is approved by this milestone; other natural features
retain their existing rules. The approved formula and version/placement requirements
have one maintained home in HOG architecture, linked above.

## Ordinary wetlands and simple footprints (2026-09-27)

Thomas approved [Wetland Footprint v1](hog.html#wetland-footprint-v1-2026-09-27):
a deterministic area-preserving ellipse creates stable game geography. Ordinary
marsh, swamp, bog and wet meadow are traversable classifications; neither proximity
nor membership implies unsafe water or unknown footing. Existing explicit water,
springs, ravines and independent hazards retain their own contracts. Gross area,
stable seasonal footprint, overlap and version semantics are maintained in HOG.
No movement penalty, stamina/mount/wagon effect or seasonal state change is implemented.
No persistent occupant/object is relocated merely for wetland membership.
This deliberate gameplay decision supersedes the investigation's more elaborate
hydrology-guided proposal; it does not authorize broader geography redesign.


## Waterway geometry and ordinary wading (2026-09-27)

Historical policy, superseded by Water Movement v1 above.

Thomas approved [Waterway Geometry v1](hog.html#waterway-geometry-v1-2026-09-27):
existing centerline/width, depth min(width/5,25) after the September 28 cap correction,
simple analytic depth/current profiles,
and deterministic class-based current. Ordinary ground movement may enter and traverse
water <=3 ft deep; it stops before >3 ft. Deeper water is future swimming territory.
Current/direction remain data, without forced drift or resistance. No new stamina,
speed, swimming, drowning, boats, bridges or ford mechanics. The exact versioned
formulas, source/join semantics, limits and live river values are maintained in HOG.
Ordinary wetlands remain traversable; independent lakes/springs/ravines retain safety rules.
