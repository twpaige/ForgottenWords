---
title: Game design
description: The MUD's accepted decisions, preserved in full and separated from tabletop rules.
reviewed: 2026-09-29
nav: design
permalink: /CCMud/design.html
---

## Authoritative design record

The existing **[CCMUD_Design.txt](/CCMUD_Design.txt)** remains the detailed game-design source. It is preserved in full, including its consolidation and numbered decisions. This installation does not migrate, shorten, or reorganize that record.

For the existing illustrated reading edition, see [CCMUD_Design.html](/CCMUD_Design.html). That older HTML is a separate legacy presentation, not an automatically generated version of the text. When wording differs, consult the text and current explicit decisions. The new Markdown-to-HTML publishing system applies to this `/CCMud/` knowledge base; it does not silently convert old pages.

The record begins with consolidated decisions C01–C11, followed by preserved numbered decisions. Read its reconciliation instructions before interpreting older entries. Old questions are historical records and do not automatically reopen a completed design interview.

## Regional lake integration (2026-09-29)

Existing continental lake cell unions now reach local walking as conservative water
footprints. Dry shoreline-chunk portions can certify; unknown lake depths remain blocked.
No new lake placement, bed, fine shoreline or world regeneration is introduced. Discovery,
APPROACH and Cormac share the stable lake ID, area, extent and exposed shoreline segments.
Physical area bands inform lake wording independently of diagnostic visibility classes.
Eyes remains bounded and LOOK generates no surrounding walking chunks. The
[HOG lake contract](hog.html#regional-lake-resolution-2026-09-29) records the reproduced
identity mismatch, geometry limits, safety checks and walking-version compatibility.

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

## Character odometers and transcript controls (2026-09-29)

ODOMETER is a persistent lifetime travel counter; TRIP is a separately resettable
counter. Both are server-side character features available to every client through
`ODOMETER` and `TRIP RESET`. The reset response is `Trip odometer reset.` and does not
change lifetime distance, selected pace or active travel.

Counters measure accepted physical travel segments using the existing travel engine's
horizontal world-distance metric, including walking/running/sprinting, wading,
swimming and actual current-driven drift. Rejected movement and the uncompleted
portion of a requested distance never count. Ground-relative Z maintenance, JUMP,
teleports/admin placement, login restoration, coordinate correction and placement-only
coordinate-space transitions do not count. Future ordinary travel can use the same
accepted-displacement accounting hook rather than command-specific increments.

The lifetime and trip counters persist as integer inches, with integer millionth-inch
carry for diagonal segment lengths so per-tick rounding does not lose whole inches.
No floating-point miles are stored. Distance is credited in the same database transaction
as the accepted position; its receipt is consumed only after commit. The migration
initializes existing characters to zero; historical distance cannot be reconstructed.

The web HUD displays compact ODO/TRIP readings in whole feet below a mile and tenths
of miles thereafter. RESET TRIP sends the normal server command. COPY copies every
current transcript entry as plain text, including off-screen entries; its brief success
or failure notice is outside the transcript. CLEAR uses the existing local clear path,
leaving gameplay, odometers and saved shortcuts untouched. The client currently has no
Up/Down command-history subsystem; this change adds none. Responsive viewport sizing
reserves space for command entry and controls while the transcript scrolls normally.

## Ordinary biome transitions (2026-09-29)

Transitions between reviewed HOG biome classes remain traversable. The selected pace
persists while the terrain factor refreshes at the represented regional boundary;
ordinary transitions require no confirmation. Missing or unsupported geography and
independent hazards remain gated. [Walking policy version 5](hog.html#reviewed-regional-biome-transitions-2026-09-29)
validates all intersecting cells, including sub-sample strips, without changing HOG
biome/elevation generation or saved positions.

## Cormac v1 (2026-09-28)

Cormac is a deterministic server-side wilderness LOOK narrator: **HOG supplies facts;
Cormac observes, interprets and describes them.** No LLM, world generation or movement
permission lives in prose. `LOOK`/`L` defaults to NORMAL; BRIEF/NORMAL/MAXIMUM select a
session preference cleared on disconnect. Login and explicit LOOK share the same path.
Interiors retain authored text and do not invoke geographic Eyes sampling.

### Cormac Eyes v1 (2026-09-29)

The active gameplay provider supplies the observation; no admin context or new provider
is constructed for LOOK. Immutable measurements record direction, distance, source,
regional cell identity, values and known/unavailable status. Separate interpreted facts
record terrain trends, biome changes and independently established cover differences,
with indices back to their evidence. Certified HERE state and deduplicated landmarks
remain structured too. This can support later travel comparisons without parsing prose.

`EyesConfig` centralizes the initial sampling and interpretation defaults:

| Source | Bounded observation | Interpretation/limits |
| --- | --- | --- |
| Continuous HOG elevation | HERE plus eight directions at 500, 2,640 and 5,280 feet: 25 evaluations | A rise/fall needs at least 8 feet and 0.15% mean rise/run over the mile, with no sampled reversal greater than 1 foot. Existing trends retain at 80% of those thresholds. No safety, cliff or intervening-profile guarantee. |
| Regional biome/cover | HERE plus eight directions at one-eighth and one-half of the smaller regional cell spacing, capped at 16 miles | Current seed: approximately 3.856 and 15.425 miles. Repeated source cells are read once per observation; 17 references are not 17 independent local vegetation surveys. |
| Tree-cover differences | Compare actual regional percentages independently of biome names | At least 15 percentage points to introduce greater/lighter cover; retain at 12. Equal-cover biome changes never imply denser/thinner trees. |
| Geographic landmarks | One pass over the existing ready discovery catalog, up to 32 candidates | Ordinary small features within 1,000 feet, medium waterways within one mile, large features within five miles, landmark-class features within 20 miles. Local features take precedence, then larger-scale features, then other distant waterways. |

Regional biome names describe broader country, not a surveyed forest edge or its exact
distance. Unknown cells and failed elevation probes are omitted, never interpreted as
empty geography. The optional four-mile vegetation-detail raster is not used in v1.
Fine vegetation, individual trees/species and local canopy/undergrowth remain unknown.

Eyes reuses RegionalDiscovery's four cached 16-mile-anchor catalogs and existing single
background worker. Catalog construction retains one hydrology viewport query and four
bounded natural-feature quadrant queries, not a query per ray/ring. A new presentation
selector avoids RAW's two-per-distance-band truncation; RAW retains its original path.
Regional channel distances are to supplied centerlines; lake distances use supplied
shorelines; most natural features remain estimated anchors (ravines use their existing
reservation polygon). Ready local water/restriction geometry overrides matching regional
IDs. Distinct IDs remain distinct. Wetland/spring prose is not newly integrated here.

Cold catalogs are requested asynchronously. LOOK uses currently available information
and never waits for their completion. Warm catalogs can add features on subsequent LOOK;
missing candidates are not evidence of absence. Directional Eyes uses no walking chunks.
The former LOOK neighborhood-prewarm call is removed: ready local geometry supplies
HERE/water, while movement retains its own prewarming and certification. No Origin hot
zone, generator/version change, certificate invalidation or database migration is added.

The current geographic-context policy assumes ordinary perception; it does not certify
LOS, terrain obstruction, darkness or weather visibility. Broad regional facts are not
proof of visual access or safe passage. `ordinary_perception` isolates this policy.
Structured facts carry a channel, allowing future authoritative audible information to
be admitted independently of visual information. No current audible-source feed exists;
audibility metadata alone produces no sound prose. Sound is a future supported channel;
scent and acoustic simulation remain out of scope.

### Clockwise LOOK and Nearby (2026-09-29)

Composition establishes **HERE first**, with precise local water taking priority over
regional vegetation, then selected facts in **N → NE → E → SE → S → SW → W → NW** order.
It is one playerless geographic paragraph, not eight headings or mandatory sentences.
Equivalent adjacent conclusions merge, including across NW/N; absent/uninteresting
sectors create no filler. Feature direction and channel-flow orientation stay separate.

BRIEF identifies the place with at most one surrounding fact. NORMAL gives a compact
mental map with up to three; MAXIMUM uses up to six useful surrounding facts. Landmarks
are limited to one/two/four respectively. Immediate water/current and HERE context are
separate from those limits. Nearby defining landmarks take precedence, followed by
regional biome changes and terrain relationships; selection precedes clockwise ordering.
Stable titles use HERE and at most one landmark within the original 1,000-foot range;
distant minor features cannot change the heading. Light woodland retains **Among Light
Woodland**. Sparse scenes can legitimately have identical NORMAL/MAXIMUM prose.

Tree cover becomes sparse/scattered/substantial/dense coverage prose, not percentages.
The existing thresholds remain <15%, <50%, <75%, then dense; HERE slope bands remain
<0.5%, <5%, <15%, then steep. Distances remain humanized, extending the original feet
bands to roughly a quarter/half mile, about one/two miles, several/many miles. Existing
distance, compass, slope and cover hysteresis remains; local water entry/exit is immediate.
Cormac invents no weather, species, sound, scent, lighting, water color or bank material.

**Nearby:** remains a separate live text list, clockwise then nearest within each sector,
with approximate feet and a functional label. All verbosity modes retain supported
entities; empty sections are omitted. The audited Pioneer Cabin within its existing
100-foot observation range is the current supported subset. Door state and distance to
its footprint/floor update independently of geographic prose. No generic wilderness
player/NPC/item feed or large-structure landmark policy is introduced.

### Stability and diagnostics

The prose signature includes classified HERE, stable landmark identities/relationships
and interpreted directional conclusions, excluding sample coordinates/raw elevations,
timings and evidence indices. Existing 256-entry prose and character-memory bounds and
eight-entry recent-scene/language hooks remain. Repeated stable scenes reuse prose.

There is no new observation-result cache. Eyes recomputes the bounded survey, retaining
only the latest observation for at most 256 characters to support diagnostics and simple
hysteresis. Generation identity includes zone/seed/version; prior interpretations cannot
carry across identities. Disconnect removes the retained observation. Default per-character
measurements are bounded at 42; regional landmark candidates at 32 plus existing bounded
ready-local geometry. No entity state is stored in that geographic observation.

The server-side admin helper `HogGame.cormac_debug(character_id)` checks admin permission
and returns the last HERE state, measurements, interpreted facts/evidence, candidates,
feature availability, unique regional-cell reads, query count, construction/feature time,
and prose reuse flag. It performs no new survey. This is a Python diagnostic helper,
not a new player command or RAW display mode. Ordinary players receive no instrumentation.
RAW and live optional Position remain separate and unchanged.

LOOK is the spatial survey; future travel prose can compare structured observations for
meaningful changes. Travel narration, authored outdoor overrides, certified perception,
vegetation-detail integration and language variation are not implemented in this phase.


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
