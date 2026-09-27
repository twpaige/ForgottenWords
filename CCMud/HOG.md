---
title: Heart of Gold
description: Deterministic world-generation architecture, physical constraints, and preview limits.
reviewed: 2026-09-27
nav: hog
permalink: /CCMud/hog.html
---

This is the canonical HOG architecture reference, carried forward from Crown-Call's existing `docs/heart-of-gold.md` at revision `a6b2fc76dfa27f38eaf623034c98dd6f67c3975e`. Its detailed engineering content is preserved. Current progress and live-environment evidence belong in [Status](status.html). HOG applies only to the MUD.

* Contents
{:toc}

## Wetland Footprint v1 (2026-09-27)

**Implemented and locally verified; not automatically deployed.** Private code/evidence
revision `3812799921c4fa5ecfaef070fd82fcaf36d861d9`. Read the [release report](https://github.com/twpaige/Crown-Call/blob/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/wetland-footprint-v1-release.md) and [preserved evidence](https://github.com/twpaige/Crown-Call/tree/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/evidence/wetland-footprint-v1).
This section supersedes earlier wetland-refusal descriptions. Ravine Reservation
Contract v1, springs, water and unrelated hazards retain their existing rules.

### Traversable game geography

Ordinary marsh, swamp, bog, wet meadow and seasonal variants are traversable terrain
classifications, not automatically dangerous water, unstable footing or exclusions.
Wetland Footprint v1 deliberately defines simple deterministic game geography rather
than recovering a nonexistent saturation polygon. There is no hydrological growth,
flood fill, drainage orientation or groundwater simulation in the footprint.

Keep each existing stable ID, center, generated area A, subtype and seasonal flag.
A is **gross wetland-complex area**: represented lakes/channels inside it retain
independent water rules, and do not cause compensating ellipse expansion.

### Exact shape contract

For label `aspect` or `orientation`, SHA-256 hash UTF-8
`wetland_footprint:1:{stable ID}:{label}`; interpret the first 8 bytes as unsigned
big-endian, shift right 11, and divide by 2^53. Each u is in [0,1).
R=1.25+0.75*u_aspect; theta=180*u_orientation degrees. R is semi-major/semi-minor
ratio, bounded by the approved 1.25-2.00 range. Orientation is **counterclockwise
from +X along the major axis**, not a gameplay compass bearing.

With A in square miles, a=sqrt(A*R/pi), b=sqrt(A/(pi*R)). Full dimensions are 2a by
2b and pi*a*b=A. Translate the coordinate and rotate into local axes; membership
is (x/a)^2+(y/b)^2 <= 1+1e-8. Boundary contact is included; tolerance extends generated
footprints less than about 0.001 inch, independently of the terrain lattice.
Mathematical ellipse area remains A. No coarse raster or polygon determines membership.

Rotated half-bounds are hypot(a*cos(theta),b*sin(theta)) and
hypot(a*sin(theta),b*cos(theta)), padded by sqrt(1+1e-8). Exact chunk intersection
checks normalized rectangle-edge minima plus ellipse-center containment, not just
bounding-box overlap. Source discovery expands separately by the maximum possible
semi-major axis sqrt(12.03*2/pi) including tolerance. Runtime/source-cell seams do
not clip footprints; water connector refinement is unchanged. Existing bounded
queries may require a smaller viewport if the expanded source set exceeds 64 cells.

### Runtime, RAW and identity

Whole-guard wetland refusal is removed. Ordinary background terrain still must pass
all independent grade, interpolation, biome, coast, water, spring, natural-hazard
and placement checks. Chunk wetland metadata and exact surface membership preserve
the underlying biome. Crossing an ordinary marsh boundary does not stop movement.
Overlapping footprints retain all distinct IDs sorted deterministically; no costly
merging or subtype precedence is introduced. Explicit water remains independently
blocked under its existing safety contract. Playas stay in the lake/water system.

Seasonal metadata does not change the fixed ellipse. **No speed/stamina penalty,
mount/wagon effect, dangerous mud/deep-pool model or seasonal state change exists.**
Existing occupants and objects are not automatically moved because they are in marsh;
no migration is introduced. Springs remain a separate unresolved feature class.

Identity adds `wetland_footprint=1` to regional sampling/walking/cache dependencies;
`dev_walking=4`, `ravine_reservation=1` and local hydrology=3 remain. Historical
walking identities require current-coordinate revalidation, not stale cache reuse.
Default identity: `0aafbfa9e975252672aa4734f03973d82bc99e32d57b751472c641ed6211a86e`. Stable wetland feature IDs do not change.

RAW exposes all current membership IDs and up to eight compact local ellipse records
from ready nearby chunks, with an omitted count. Records include ID/subtype/version,
center/gross area, ratio, geometry angle/convention, dimensions/bounds, seasonal flag,
inside/outside and perceived=false. No sampled boundary coordinates or expensive
per-LOOK generation are added. Normal underfoot facts retain biome plus memberships.
The admin map draws and selects the same ellipse. General RAW/DEBUG redesign is deferred.

### Live acceptance and evidence

`867359018957601:wetland:5822:80` retains area 10.738035272632617 square miles. Its
aspect is 1.9673120430452131; angle 139.09105493907856 degrees CCW from +X; full dimensions
5.186257760372709 by 2.6362151234253983 miles. The prior `JUMP SOUTH 500` destination
`[69363083,-31266049]` in chunk `[11560,-5212]` is outside and certifies locally.
Rounded anchor `[69588139,-31352317]` is inside marsh and also certifies locally.
No test coordinate is special-cased. Thomas must separately deploy through the
unchanged Linux pytest/migration/activation/health gate, then verify JUMP and RAW.
Local test/measurement details and limitations live in the release report.

The earlier 2,554-record investigation and raw evidence are retained privately.
Its hydrology-guided recommendation is historical and superseded by this explicit
simple-ellipse decision; do not repeat that study to rediscover the approved scope.

## Ravine Reservation Contract v1 (2026-09-27)

**Implemented and locally verified; not automatically deployed.** The bounded
release is private Crown-Call `2aabec9dd523a0e3a8efd325c76ca0a8525ce7df`. Read the [release report](https://github.com/twpaige/Crown-Call/blob/2aabec9dd523a0e3a8efd325c76ca0a8525ce7df/docs/ravine-reservation-v1-release.md)
and [preserved investigation, sample and release evidence](https://github.com/twpaige/Crown-Call/tree/2aabec9dd523a0e3a8efd325c76ca0a8525ce7df/docs/evidence/ravine-reservation-v1). This
section supersedes earlier whole-chunk refusal descriptions for **ravines only**.
Other natural-feature classes remain unresolved under their existing rules.

### Approved prospective containment

This is a generator-owned reservation, not a claim that catalog dimensions already
describe a physical rim. With catalog width W, length L and vertical dimension H
in feet:

```text
floor width budget  = 0.20 W
floor length budget = 0.20 L
B = max(W, 0.20 W + 2 H)
A = max(L, 0.20 L + 2 H)
T = max(5 feet, 0.25 H)
```

Center an A×B rectangle at the catalog anchor, expand it outward by a disk of
radius T, and rotate it using the stored angle. Orientation is **counterclockwise
from +X, with the length axis aligned to the angle**, not a compass bearing.
The existing hashed angle gains these semantics through the versioned contract;
catalog values and stable feature IDs do not change. Outer dimensions are
A+2T by B+2T; exact area is AB+2T(A+B)+πT².

H means **maximum vertical construction budget**, not actual or surveyed depth.
The centered floor budget plus H wall run per side/end permits nominal 1:1 walls
at full H relief before the separate transition band. This reserves construction
space, not stable or walkable slopes. The five-foot minimum is independent of
the 25-foot terrain lattice.

**All ravine geometry, terrain deformation, floor, walls, rim, hazards and
transition effects governed by v1 must remain wholly inside the reservation.**
Ravine-induced deformation **and its gradient** must return to the authoritative
background terrain by its outer boundary. Future geometry may use less space;
geometry that cannot fit must be rejected or require an explicitly versioned
reservation expansion. Future implementations must validate this contract;
there is currently no admitted interior geometry or traversal system.

The reservation is not necessarily the visible rim, wall, floor or eventual
physical outline. Outside it, background terrain is authoritative subject to
every independent certification requirement.

### Representation, identity and spatial completeness

The collision shape is a conservative 128-side circumscribed support polygon,
never an inscribed polygon. Axis normals preserve straight sections; corner
intersections overbound the circular arcs. Tiny outward numerical padding is
independent of terrain sampling. The live ravine's maximum outward approximation
is about 0.20 inch. Exact contract dimensions/area and conservative polygon/bounds
are labeled separately in metadata.

Walking identity includes **dev_walking=4** and **ravine_reservation=1**. Natural
catalog generation remains v1 with stable IDs; query metadata separately identifies
the reservation contract. New caches use the new identity. Existing character
references from walking versions 1–3 require current-coordinate revalidation;
compatibility is not permission to reuse old terrain or bypass exclusions.

Per-feature lookup uses a conservative radius derived from hypot(A,B)/2+T plus
polygon allowance. A source-context bound, derived once from maximum regional
relief and the generator's dimension limits, also expands source-cell discovery.
This is essential: updating only the per-feature filter would not establish
complete lookup. Queries remain bounded; no per-chunk global catalog generation
or new worker is introduced. Whole identical exclusion polygons are attached to
each intersecting chunk, avoiding clipped-edge seam holes.

### Exterior gameplay and diagnostics

Only ravines replace whole-chunk natural-feature refusal with certified exterior
plus blocked reservation. Existing slope/interpolation, biome, water, springs,
other unresolved features and persistent-overlay checks remain active. Ordinary
wetlands now follow Wetland Footprint v1 above.
Whole-chunk background-field checks are retained; this is not a terrain
certification redesign. Interior standing and GROUND resolution are refused.

Full movement segments intersect polygon boundaries, including crossings whose
endpoints are both outside. Boundary contact is conservative; parallel exterior
travel and routes around it remain possible where independently certified.
APPROACH continuously resolves the nearest represented perimeter and stops
outside, within the existing two-inch arrival tolerance. The message is:

> Unresolved ravine boundary; descent/crossing safety is unknown.

RAW has separate local natural-hazard diagnostics: feature ID/kind, contract and
version, W/L/H meaning, angle/convention, A/B/T, exact dimensions/area/radius,
collision polygon/bounds/approximation, nearest boundary point and distance/bearing,
certification and hazard. **perceived=false** remains explicit. Regional diagnostic
targets use reservation boundaries without claiming LOS or a visible rim.
Existing water geometry, navigation and messages remain unchanged.

Existing GROUND placement audits flag unresolved geography for review; no object
or character is silently moved. A character already inside an expanded reservation
fails current-position validation and requires an explicit administrative decision.
ABSOLUTE heights remain absolute, not ground-compatibility proof. Object footprints
still need object-specific review; structure blockers remain. Admin JUMP retains
endpoint-certified teleport semantics and cannot place its destination inside
the reservation; no ravine jumping/crossing mechanic is implemented.

### Exact regression and measured cost

`867359018957601:C:natural:v1:site:7502:0:1` retains W=307.48, L=614.96 and
H=219.64 feet. A=614.96, B=500.776, T=54.91 feet; outer dimensions
**724.78×610.596 feet**, exact area **10.100082 acres**, enclosing radius
**451.442598 feet** before conservative collision allowance. None is hard-coded.
Chunks **[11560,68], [11560,69], [11559,69]** generate successfully with blocked
reservation and independently certified exterior. Expanded-edge lookup, fresh
source reproduction, through-crossing, exterior routes, RAW and actual game
APPROACH are covered by local tests. Live acceptance awaits Thomas's deployment.

Local Windows measurements: no-ravine warm generation median 384.3→389.4 ms;
certificate 23.63→23.88 ms. Ravine certificate medians 22.0–22.3→23.0–23.3 ms;
new successful full generation 388–391 ms. Old ravine calls **refused** at ~22 ms,
so that is not a successful-generation performance comparison. Cached alongside
collision median ~0.30 ms, crossing ~0.16 ms, geometry construction ~0.17 ms.
Twenty controller replays all stop outside about 1.05 inches from the boundary.
These small local samples are not Linux/network/DB latency guarantees.

Full suite: **309 passed, 13 warnings in 411.55s (0:06:51)**. Three fresh-process concurrency/session runs:
**12 passed, 1 warning in 55.29s; 12 passed, 1 warning in 56.23s; 12 passed, 1 warning in 54.71s**. Changed-file lint passes; 55 pre-existing repository
findings remain in untouched files/evidence. No tests overlapped timing runs.

Failure counters are unchanged: cumulative failures can include repeated
geography-unavailable attempts. Bounded records distinguish that category from
unexpected exceptions; queue rejection is separate. A category alone is not a
complete cause diagnosis. The historical 13 failures remain unexplained.

No ravine descent, floor/wall movement, climbing, falling, crossing consequences
or bridges exist. Other natural-feature classes are unchanged. Retain 500-foot
chunks, 25-foot samples, current movement/stamina/time and bounded prewarm behavior.

## Bounded efficiency implementation (2026-09-27)

**Implemented and locally verified; not deployed to DEV or PROD.** Private
Crown-Call `0472cce96f168e775616ab3b39c0d121c0fa1ec6` delivers regional-product reuse and ordered prewarm plans.
The [release report](https://github.com/twpaige/Crown-Call/blob/0472cce96f168e775616ab3b39c0d121c0fa1ec6/docs/hog-efficiency-release.md) and [before/after evidence](https://github.com/twpaige/Crown-Call/tree/0472cce96f168e775616ab3b39c0d121c0fa1ec6/docs/evidence/hog-efficiency-release) record exact changes,
measurements and tests. This section supersedes baseline implementation descriptions
in the preserved investigation below, not its measured findings or safety limits.

Runtime chunks remain **500×500 feet**, samples **25 feet**, and generator identity,
formulas, two-mile guard, certification thresholds, precise water geometry and
movement/stamina rules are unchanged. No world-clock, travel multiplier, daylight,
LOS/prose, speculative corridor or distributed worker was implemented.

### Immutable products and shorter critical sections

LocalHydrology retains one immutable regional network per fixed source context,
avoiding repeated edge reconstruction and river-mouth clipping. Bounded catchment
and natural-feature catalogs are immutable; natural queries filter before copying
returned records. Public query/cell results receive shape-preserving isolated
mutable copies, including nested geometry and hydraulic metadata. Refined edge
halves are per-query overlays; they never mutate the retained base. Replace the
context for changed seed/source layers/versions; no cross-identity global cache is
used, and in-place mutation of source inputs is not an invalidation API.

Cold products build outside short synchronized LRU/publication sections. Concurrent
cold misses can duplicate deterministic work, but cannot corrupt shared products.
RegionalDiscovery no longer holds the provider-wide movement lock across its
catalog queries. Movement retains its certificate lock; cache publication remains
synchronized. No database/session or worker ownership checks were weakened and
no generation workers were added. CPU/GIL contention remains; this is not a
preemptive scheduler or a guaranteed latency ceiling.

### Ordered plans with cache-owned reconciliation

Every travel substep projects the existing physical footprint into an ordered,
deduplicated current/ahead/neighborhood plan. Equality includes generation/policy
and ordered chunk coordinates, not floating-point steering or only set membership.
Movement inside the current chunk can change its ahead plan. APPROACH steering and
ray accounting still update continuously.

A receipt on active TravelState records cache-instance token, plan, state epoch
and maintenance deadline. Completion, failure, future consumption, eviction/expiry
and closure invalidate cached satisfaction. Active reconciliation checks at most
250 ms later, or earlier for known retry/expiry deadlines; queue-rejected obligations
remain required. Only absent eligible chunks call request. The equality fast path
does not scan the cache or refresh idle times. Existing get/request maintenance
remains. STOP, arrival, target replacement and disconnect discard the receipt;
there is no retained subscription registry. Cache replacement invalidates old
receipts, including a replacement with the same generation identity.

The default footprint retains the same dominant-axis offsets at 500 and 1,000
feet plus eight ±500-foot neighbors, mapped to chunks; diagonal physical distances
are unchanged. The general forward/lateral/behind-feet and ready-seconds policy
remains future work. Already-admitted jobs retain ordinary single-flight/FIFO
behavior; this release does not reprioritize or preempt a running job.

### Measured results and boundaries

Same 26 dry / 28 water jobs and physical union: wall time **16.994 → 10.428 s**
and **18.055 → 10.875 s**, respectively (38.6% / 39.8% less). This is less work
per job, not fewer generated chunks. Captured dry/stream/lake chunk, water and
natural-feature hashes agree. The synthetic already-ready APPROACH replay changed
150 prewarm calls / 1,650 requests to **six plan reconciliations / zero requests**,
with the same endpoint and zero generation jobs in either version. Actual retry
or cold-cache obligations still submit requests.

Cold-catalog movement lock wait measured **1.772 s → 0.0000039 s** locally;
movement completion was 2.461 → 1.237 s. In the existing sustained/cold command
harness, SAY/LOOK p95 improved; STOP p95 was slightly higher, while maxima and
heartbeat tails were lower. These are single local Windows runs with isolated
SQLite, not Linux/PostgreSQL/network guarantees. See the report for CPU, phase
costs, all latency values, cache metrics and limitations.

The authorized last-32 bounded failure diagnostics are now included in this code
release, rather than only preserved as an uncommitted patch. Retention, fields,
admin RAW exposure, separate queue-rejection counter and restart loss are unchanged.
The historical 13 failures still have no known cause. Remaining source spatial
indexes, reusable local water products and explicit priority admission can be
considered separately if new evidence warrants them; no claim that all repeated
work or CPU contention is eliminated is made.

## Step 10 efficiency decisions and evidence (2026-09-27)

These findings apply to investigation base
`e54acc982c1b68450b85e9c428fd0e82daa64d9c`, principally seed
`867359018957601`. They distinguish **current implementation**, **benchmark
findings**, **settled unimplemented design**, and **future investigation**.
They do not authorize an optimization release or game deployment. See the
[durable evidence index](evidence.html) for both full reports, result JSON,
edge audits, methodology and reproduction scripts. Do not rediscover these results
merely because a future development session lacks the original conversation.

### Three independent resolutions

| Concern | Current implemented behavior | Decision / future investigation |
| --- | --- | --- |
| Runtime chunk dimensions | 500×500 feet | Retain for the measured architecture |
| Terrain storage/interpolation | 25-foot sample spacing, continuous bilinear interpolation | Uniform 100-foot spacing is a promising candidate, not approved production behavior |
| Terrain certification | Coupled to sample-lattice corners and patch centers | Investigate sufficiently fine independent safety coverage before coarsening representation |

**Chunk dimensions, representation resolution and certification resolution are
separate architectural concerns.** Positions remain continuous; a 100-foot
interpolation patch would not be a MUD room. Exact local hazards and persistent
object coordinates are not forced onto the terrain lattice.

### Measured chunk-size and terrain-spacing findings

The equal-physical-coverage dry benchmark took approximately **16.1 / 42.4 / 207.4
seconds** for 500/250/100-foot chunks, holding terrain samples at 25 feet. The
same footprint had the same unique elevation coordinates; subdivision repeated
shared-edge evaluations, regional certification, scheduling/chunk overhead and
cache requirements. **Retain 500-foot chunks now.** This is not a universal
permanent optimum: major certification/cache changes may justify rebenchmarking.

The distinct spacing experiment kept chunks at 500 feet. Equal-weight means of
eight accepted sites' three-repeat medians were:

| Terrain spacing | Local elevation + certificate-center evaluations | Wall / CPU per chunk | Approximate recursive Python object size |
| --- | ---: | ---: | ---: |
| 25 feet | 841 | 0.656 / 0.400 seconds | 85.8 KB |
| 50 feet | 221 | 0.422 / 0.293 seconds | 26.9 KB |
| 100 feet | 61 | 0.322 / 0.258 seconds | 11.3 KB |

At 100 feet this is **92.7% fewer local evaluations, 50.8% less wall time, 35.6%
less CPU time and 86.8% smaller chunk objects**. Wall savings partly reflect
fewer cooperative sleeps (nominal 205/105/55 ms); they are not all CPU savings.
Regional certification remained approximately 0.25–0.27 seconds per chunk.
Illustrative compact/compressed serialization also shrank substantially. These
are local Windows measurements, not process RSS, an implemented persistent chunk
format, a full-world pregeneration recommendation or live-server capacity proof.

On accepted real terrain, maximum elevation difference from the 25-foot reference
at 100 feet was **under 0.001 inch**; slope, directional grades and uphill-bearing
differences were very small. Dry travel, stream blocking, stream APPROACH and lake
APPROACH had equivalent tested outcomes. One accuracy probe crossed an integer-Z
rounding boundary, changing rounded GROUND by one inch despite a sub-thousandth-inch
interpolation difference; none of the four movement endpoints changed.

All 13 main-site certification outcomes agreed (eight accepted, five refused).
An additional 64 real basin-edge chunks showed no differing local decisions.
Rejected real cutoffs showed substantial slope smoothing, but remained rejected.
Synthetic counterexamples showed finer checks detecting irregularities that
50-foot and/or 100-foot checks missed. **These do not prove those synthetic
irregularities exist in the generated world.** They prove that changing the
lattice changes safety coverage; unchanged numerical thresholds do not establish
equivalent certification. The current sampled certificate is itself not a
universal mathematical proof.

**Keep 500-foot chunks + 25-foot production samples.** A separately approved
bounded prototype should evaluate uniform 100-foot storage/interpolation with
independent finer certification and validation of the coarse interpolant itself.
Fifty feet is not automatically a safe compromise. Adaptive 100/50/25-foot
representation is optional future investigation; prefer uniform resolution if
adequate. No decoupled/adaptive design has been proven or implemented.

### Geography independence and movement safety

Runtime spacing does not generate waterway centerlines, widths, boundary polygons
or lake footprints. Their existing inferred vector representations, hydraulic
facts and tested water membership were unchanged. An approximately 18-foot stream
does not become 100 feet wide at 100-foot terrain spacing. Exact exclusion remains
independent, while overall movement permission still requires terrain certification.
Unknown bed/bank geometry, water depth and crossing consequences remain blocked.

Natural-feature anchors, regional feature catalogs, biome source classification
and regional tree-cover fields do not consume runtime terrain samples. Local
validation/ground placement can be affected indirectly; slope/grade and terrain
certification depend directly on the interpolated lattice. The regional biome
boundary refusal remains necessary even if fewer sample nodes are inspected.
The full sampling report contains the explicit system-by-system dependence matrix.

### Repeated regional work is the main optimization target

`_certify_area()` runs for each runtime chunk and scans coastline, regional lakes,
hydrology and natural-feature information. Profiling found substantial repeated
hydrology work: `regional_edges()` reconstructs regional waterways/network data
and clips river mouths before bounded query filtering. Natural-feature queries
also repeat catalog copying/query work. Regional discovery and movement
certification contend on the same regional lock: one cold catalog operation
blocked movement's lock acquisition for approximately **1.67 seconds** locally.

Investigate immutable bounded regional products, spatial indexes, reusable water
geometry and reduced reconstruction/copy work. Preserve API isolation: current
query refinement mutates returned edge points, so simply sharing cached mutable
edge dictionaries is unsafe. Reduce shared-lock contention while preserving
movement priority. **Do not weaken conservative certification or precise water
exclusions to gain speed.** Reduce unnecessary work before adding CPU-heavy workers.

### Ordered prewarm plans and speculative APPROACH

At the investigation base, APPROACH steering updated frequently and invalidated prewarming on vector
changes. A representative 15-second probe produced **150 prewarm submissions and
1,650 underlying request calls**, but only six meaningful ordered plan changes
(five unordered footprint changes). This is investigation evidence, not a deployed fix.

Compare **ordered, deduplicated required chunk plans**, preserving current chunk,
then forward chunks, then safety/neighborhood priority. Keep steering continuous
while suppressing redundant submissions. Bounded reconciliation must preserve
retries after failures, queue rejection, expiration and eviction; an unchanged
plan does not mean its chunks are still ready. Express preparation in physical
distance/time so changing chunk dimensions cannot silently shrink the envelope.
The report's proposed envelope values are experiments to review, not new defaults.

Do not enable a long speculative APPROACH corridor as the first optimization in
the current single-worker FIFO executor. Lower queued priority cannot preempt an
already-running speculative job when urgent work arrives. At proposed 3.5× travel,
the tested capacity model had no preparation delays without speculation; this is
a model result, not a live guarantee. Revisit only with safe preemption/resume,
independently available spare capacity, or measured live need.

### Three query contracts and progressive detail

**Intended architecture; not yet a complete runtime API separation:**

- **Movement certification:** exact local geography/hazards needed for the next
  movement. Visibility must never remove a real movement hazard.
- **Perception candidates:** geographically relevant plausible features for an
  observer. Prefer stable ID, type/subtype, bearing, distance and distance basis,
  prominence/scale, uncertainty and a detail handle. A candidate is not a visibility
  claim. Request exact geometry only when necessary.
- **Admin RAW:** retain diagnostic geometry, certification, hydraulic metadata,
  internal state and regional/distant information. Do not cripple RAW to imitate
  ordinary perception. Existing distant RAW is explicitly `perceived=false`.

HOG owns detailed geographic truth but should return the minimum truthful data
needed by each query. A creek half a mile away, if relevant, generally needs only
its ID, type, approximate distance and bearing, not width/discharge, seasonal
discharge, full centerline, polygons, hydraulic elevation, crossing certification
or bank coordinates. Query finer information as the observer approaches.

Feature-class/size/prominence relevance horizons are **starting hypotheses, not
final visibility rules**: cave entrances very local; creeks/small streams short
range, perhaps starting around 500 feet; ponds local; rivers local/moderate;
lakes moderate/long according to scale; forest/tree lines longer; cliffs/escarpments
moderate/long according to prominence; major mountains potentially 50–100 miles;
ranges potentially out to the existing major-landmark horizon. Do not apply one
universal radius. Reducing creek candidates must not hide a major mountain 42 miles away.

### LOS belongs on demand in perception

Do not precompute observer-dependent LOS into every chunk. HOG supplies geographic
truth; cheap feature-class/distance/prominence filters narrow candidates; on-demand
game-side perception/LOS then combines observer position/elevation, intervening
terrain/vegetation, weather, daylight/twilight/moonlight, persistent structures and
doors, and potentially character abilities. Cache with appropriate observer/spatial,
environment, structure-state and generator-version keys and bounded invalidation.
Urgent movement generation must not be starved by perception work. Future prose
receives established perceived facts; it decides neither existence nor visibility.

### Feature density and historical failures

The supplied large-area feature study did not establish excessive world density:
approximately one small pond per 325 square miles overall, one per 101 square
miles in sampled wet low-relief terrain, one riffle per 18.5 river-miles, one rapid
per 119 river-miles, either per 16 river-miles, and one natural-feature catalog
record per 55 square miles. These are supplied investigation context, not a new
benchmark executed by this documentation task. Crowded RAW partly reflects distant
regional diagnostics. Address relevance and payloads before reducing feature density.

The original cumulative **13 failures cannot be diagnosed retrospectively**:
their messages/logs were not saved. The plateau while hundreds more chunks
succeeded and queue rejection stayed zero argues against persistent total failure,
but identifies no cause. Unrelated reproduced refusals cannot explain those events.

At investigation preservation time the authorized observability addition was
implemented/tested locally but uncommitted. The bounded implementation release
above now includes it; deployment remains separate.
It retains the last 32 failures per TerrainCache, cumulative sequence, UTC time,
generation identity, chunk coordinates/size/spacing, exception type/category and
a single-line message capped at 512 characters, exposed through admin RAW.
Queue rejection remains separate. History is bounded, in memory and lost on
process restart. The private evidence preserves the patch without applying it
as part of this documentation release.

Settled world-time/travel/daylight decisions are in the [design record](design.html#world-time-travel-and-seasonal-light-2026-09-27).
The bounded release sequence is in [Status](status.html#environment-and-next-action).
Distributed/home workers, full runtime-chunk pregeneration and speculative corridors
remain future options, not immediate requirements. Expensive persistent source
tiles may eventually be useful; do not represent ordinary ocean as billions of
runtime chunks or infer a persistent format from illustrative serialization.

## Generator overview

### Current Step 10 DEV exploration contract (2026-09-26)

This section supersedes the activation limits in the historical implementation
phases below. Crown-Call `280aea0` enabled actual HOG ground walking at seed
`867359018957601`, `(0,0,GROUND)`. The exploration continuation retains that
continuous elevation formula and its dry/gentle certification restrictions.
It does not turn the coarse regional sampling adapter into certified terrain.

Startup still checks 64 chunks around the origin. The runtime now certifies
additional 500-foot chunks on its bounded background worker ahead of travel,
using directional prewarming, 25-foot samples and precise inch positions.
The old 2,000-foot administrative stop is removed from runtime chunks. Admission
requires dry ground outside represented water exclusions, no unresolved coast or
regional macro-lake/natural-feature extent,
no unresolved vegetation transition, grades no greater than 5%, and patch-center
error no greater than 0.25 inch against the continuous field. Springs in
the two-mile guard survey conservatively deny certification; ordinary wetlands
follow Wetland Footprint v1 rather than whole-guard refusal. These are certificate
restrictions, not newly invented global hazard thresholds. Unknown, late or failed
chunks stop travel; `direction!` cannot bypass unavailable geography. Expanded
chunks are disposable in the existing bounded cache; all Zone C is never materialized.
Generation failures have a bounded five-second retry cooldown. Initial startup
products remain bounded at 64; runtime cache capacity remains 64.

The local walking policy is version 3. The exact prior version-1 and version-2 identities remain
accepted for existing DEV test characters because the physical generator and
ground formula are unchanged; current local certification is revalidated on entry.
No character or persistent structure is relocated. A cold resumed location may
require retrying entry after background certification. Other identities stay blocked.

#### Bounded field-testing commands

`CLEAR` is recognized by the web client (case-insensitive, surrounding whitespace
ignored). It removes only the currently displayed transcript, including command
echoes. It sends no server command, preserves the status line/socket/character and
active travel, and returns focus to command entry. Later output appears normally.
There is no new button or server-state reset.

`TRAVEL <0-359>` accepts whole-number compass degrees: north 0, east 90, south 180,
west 270, increasing clockwise. For example, `travel 298` starts or redirects at
the selected pace. Invalid/out-of-range/fractional inputs and `travel 90!` are
rejected; values are not wrapped. Named directions and their eight short aliases
remain valid. Named directions, exact headings and APPROACH all use the same
TravelController, inch-coordinate integration, certification, terrain/grade factors,
stamina, 15-second observations, directional prewarming, water/hazard interception,
persistent-structure checks and STOP. Accumulated ray steps retain the small component
of nearly cardinal headings instead of rounding it away each tick. Rounded stop
positions are rechecked before movement is committed.

`APPROACH <id|feature_id>` is an **admin-only diagnostic navigation utility**. It
does not establish perception or visibility. It first resolves matching feature or
boundary IDs in ready local chunks within one chunk of the character, using their
existing inferred water-exclusion polygons. Otherwise it searches only the current
bounded regional diagnostic catalog (at most 4,096 entries, 50-mile distance ceiling).
Regional natural features use their supplied point anchors; waterways use their
represented centerlines; lakes/coastline use their represented closed shoreline.
Geothermal/sinkhole/other natural IDs work only when present with usable geometry.
An arbitrary global ID index, pathfinder, and new feature geometry are not built.

An unavailable catalog is requested asynchronously and the command returns a retry
message. Unknown, out-of-scope and geometry-less targets fail cleanly without
starting or replacing travel. Successful resolution retains immutable coordinate
values and generation identity, never live ORM state. Steering selects the nearest
point on the represented geometry every integration substep; ready local water
geometry takes precedence over the regional representation as it becomes available.
This is direct steering, **not route planning** around obstacles. No forced crossing
is requested. Ordinary certification/water/hazard/structure stops still apply.
Arrival stops within two inches of the represented target; reaching an inferred
boundary or approximate anchor does not certify the feature or its crossing.
Pace changes preserve the target; STOP, exhaustion, safety stops, a new direction,
disconnect or successful JUMP clear it. Target geometry may be retained after cache
eviction, but a changed generation identity invalidates it. Every walked segment
still requires current certified terrain.

`JUMP <direction> <positive miles>` is a separate **admin DEV field-placement
utility**, explicitly teleporting rather than walking. All eight full names and
`N S E W NE NW SE SW` work, case-insensitively. Positive decimal miles are accepted.
`jump east 192` moves 192 miles east; `jump NE 192` moves **192 miles total at 45°**,
about 135.7645 miles per axis. The destination is rounded to integer inches. It
must remain in implemented Zone C and be certified standing ground outside water
exclusions and unaudited persistent-structure footprints. A cold destination queues
normal bounded HOG preparation and returns without moving; retry after preparation.
Rejected destinations leave active travel and character state unchanged. The route
between endpoints is intentionally not inspected or materialized for a teleport.

On success, JUMP resolves the **destination's HOG ground height** and saves that Z,
outdoor context and spatial-index coordinates through the existing session/checkpoint
boundary. It uses the existing character GROUND model, not a new attachment table;
the source's numerical Z is not carried across. Successful JUMP cancels active
travel/APPROACH and any crossing warning/authorization, preserving selected pace and
current stamina. It is not a gameplay travel or hazard-consequence implementation.
No migration, generator/version change, automatic relocation or PROD enablement is added.

The existing top status line adds plain-text **speed and heading beside stamina**.
Speed is the last integrated effective ground speed in game miles/hour, including
terrain/grade/load factors (the existing 24× movement-time conversion is unchanged).
Heading is clockwise from north, to one decimal degree. APPROACH updates it as it
steers. Stopped/JUMP-completed status shows 0 mph and no active heading. Telemetry
uses existing worker results, not new database queries or browser polling.

#### Local water exclusions and responsiveness

Existing local hydrology supplies inferred centerlines, estimated widths and lake
footprint polygons. Walking now retains these as precise exclusion boundaries,
independent of the 25-foot elevation grid. Each channel segment becomes an oriented
rectangle, expanded by half its estimated width on both sides and at both ends.
The conservative square caps overlap at joins. Lake exclusions use the existing
polygon. Actual polygon/chunk intersection replaces whole-channel bounding-box
refusal. These boundaries are precise intersections with an inferred model, not
surveyed banks or inch-accurate claims about real terrain.

Only the dry exterior receives the gentle-ground certificate. Water interiors have
no certified standing surface, including intermittent/dry beds. This phase does not
derive a bed elevation, bank cross-section, bank slope, shore profile, depth or
current velocity. Existing data does not support those claims. Starting inside an
exclusion is refused; ordinary travel stops at the last safe integer coordinate
before its boundary. A water warning uses the existing scoped hazard contract, but
`direction!` remains blocked without an approved consequence consumer. Regional
macro-lakes, coasts, springs and unresolved natural-feature extents retain
their previous conservative refusal. No swimming/drowning/current rule is invented.

Nearby ready chunks contribute at most eight admin RAW water diagnostics: stable
feature/boundary ID, type, distance, bearing, exclusion polygon/closest boundary,
estimated width, flow bearing along the generated segment, modeled discharge and
seasons where available, and certification/hazard state. Unknown depth and velocity
are null. Flow bearing is not a measured current. Diagnostics explicitly identify
their inferred basis and `perceived=false`; neither local nor distant diagnostic
water is promoted into player-visible perception without the required contracts.

For seed `867359018957601`, strict east travel at Y=0 reproduced the old first refusal
at chunk (16,0), X=96,000 inches (1.51515 miles). Stream
`867359018957601:waterway:7346:617`, estimated width 17.8886874 feet, had a bounding
rectangle overlapping that chunk even though its represented corridor did not.
The old check therefore rejected unrelated dry ground. Thomas reported about 1.27
miles; the exact live stop cannot be reconstructed without its saved coordinates or
command history, and this discrepancy remains explicit. With precise exclusions,
the first represented eastward crossing is stream `867359018957601:waterway:7346:593`
at X=197,096.3648707 inches, Y=0 (3.1107381 miles), width 15.1888570 feet.
The regression stops at (197096,0); its depth and crossing safety remain unknown.

Synchronous game/ORM work now runs off the WebSocket event loop and is serialized
per character, including reconnect cleanup. Ready commands are delivered before the
next travel tick; safety integration still follows each command. A bounded travel
update loads character/load/structure facts once, while terrain and precise hazard
checks still run on every substep. STOP accounts for elapsed movement but suppresses
an obsolete optional observation. Background sampling yields briefly between rows
to reduce Python GIL contention. Aggregate operation/queue timings contain no player
text. This is not a hard latency guarantee; the benchmark distinguishes commands
issued during generation and retains cold outliers.

**Release `9e70cce` is blocked:** its DEV server-side pytest gate crashed with a
native segmentation fault during concurrent travel-snapshot and character reads.
The failing fixture used SQLite StaticPool, which can give separate sessions the
same native connection. Distinct sessions alone did not isolate those transactions.
Loaded psycopg/SQLAlchemy extensions do not establish which driver caused the fault;
the exact Linux native crash was not reproduced locally.

The correction retains worker-thread command execution and independent background
HOG preparation. Services require fresh sessions bound to an Engine whose pool
provides independent connection checkouts; shared Session callbacks, a pre-bound
Connection and single-connection/thread-local pools are rejected at construction.
Application PostgreSQL uses its normal pool. Each session is created, used and
closed in one executing thread. Travel copies immutable scalar character/load/
structure facts and closes its read session before the integration/observation work;
its ContextVar contains no live ORM objects. Existing mutation commit boundaries
remain in place, and detached response objects are not shared mutable ORM state.

Concurrent tests and benchmarks use isolated file-backed SQLite WAL/QueuePool.
Tests retain simultaneous workers, normal API requests and direct database readers;
connection auditing checks ownership without serializing SQL. Barrier tests prove
distinct native connections and reader completion while a writer/worker is held,
including isolation from another session's rollback. Repeated fresh-process stress
passes supplement the complete suite. See Status for actual results and deployment
limits; a passing local run does not establish Linux DEV readiness.

Prewarming prioritizes the current chunk and two chunks along the direction of
travel before the eight neighbors. `TravelConfig.prewarm_ahead_chunks` permits 1–3;
direction changes reset the prewarm marker. Existing bounds remain 16 pending jobs,
64 runtime chunks and one generation worker. Late or failed preparation still stops
movement. Automatic travel-stop reasons and hazard warnings explicitly end with
`You stop.` in the browser, including certification failures.

`hogdisplay raw|prose` is an admin-only, session-scoped diagnostic preference,
independent of BRIEF. Current account role is checked before returning RAW data.
Disconnect clears it. PROSE retains the simple underfoot description; no prose
generator is implemented. RAW LOOK and the independent 15-second travel observations
include precise position/elevation, surface/biome, directional grades, regional
tree-cover estimate, dry-ground state, certificate, chunk/generation and cache data.
The browser echoes each submitted command before its response using plain text.

**Thomas explicitly chose to keep distant features as RAW diagnostics for this
milestone.** Regional channels, lakes, coastline and natural-feature catalogs are
queried without walking-resolution materialization. A bounded background catalog
feeds internal bands starting at 10 feet and doubling to the existing 50-mile
search ceiling. Empty bands are omitted. Up to two significant candidates per
occupied band, twelve total, retain actual bearing and estimated distance to their
regional anchor/centerline/shoreline. Natural prominence and visibility metadata
are retained; class/range and local cover limits are applied. Results are explicitly
`OUTSIDE VISIBILITY` or `VISIBILITY UNAVAILABLE`, with `perceived=false`.
The ceiling is not a claim that the effective horizon or LOS is known. Unknown LOS
is not relabeled as clear or occluded. Certified distant LOS, physical cover blocking,
and player-visible distant discovery remain a separate milestone. These diagnostics
never enter player natural/persistent layers, and no feature is invented to fill a band.

Coordinates are inches: east +X, west −X, north +Y, south −Y, Z height. Bearings
are clockwise from north. All eight directions and their short aliases are supported
in continuous HOG travel. Diagonal inch steps account for their sqrt(2) distance,
preserving physical pace, normalized directional grade and unchanged stamina rules.
Cardinal east/west leaves Y unchanged. The reported `(23998,-23998)` followed by
`(-23998,-23998)` is consistent with an earlier southward leg to the old certificate
edge followed by east/west travel; coordinates alone do not prove that history.
No cardinal-vector defect was found. Command echo makes future path reports easier
to verify; it is not a reconstruction of earlier live commands.

The north-origin blocker is the seeded **Pioneer Cabin**, `pioneer_cabin`, from
migration `20260922_05`: footprint X −90..90, Y 1230..1410 inches, absolute floor Z=0.
Its `pioneer_cabin_door` has exterior `(0,1200,0)` and interior `(0,1260,0)`.
It is persistent legacy test content, not generated HOG geography. HOG's ground near
the origin is about 17,156 inches, and the cabin has no reviewed ground/footprint/
portal-height adaptation. The walking adapter therefore blocks its full XY footprint.
RAW identifies nearby structures diagnostically without claiming they are visible.
No automatic deletion, grounding, relocation or approval is appropriate. Any such
adaptation requires Thomas's separate decision and a footprint/interior/portal audit.
These coordinates identify the migration record; the live database was not inspected
in this continuation because the available SSH connection timed out.

Existing continuous travel, selected pace, stamina, elapsed offline recovery,
significant-boundary checks and scoped hazard confirmations remain in force.
Fall/swim consequences, outdoor object interactions and unresolved terrain remain gated.
DEV activation still requires `ENVIRONMENT=development`, `HOG_DEV_WALKING=true`
and the exact seed above. PROD remains unauthorized.

H.O.G. is CCMUD's deterministic physical-world generator. The admin viewer at
`/admin/world` previews its outputs; it is not the player's map or LOOK text.

## Nine-zone world constraint

The world is a 3×3 grid: NW, N, NE / W, Center, E / SW, S, SE. Each zone will
use the same generator with its own seed and configuration. Center contains
the coordinate reference (0,0) and is the only zone under development now. Broad ocean buffers
separate zones, so geography and climate do not need to join across boundaries.
Generator coordinates can be local to a zone; the game will apply a zone offset
to expose continuous world coordinates.

Each zone is a square 10,000 × 10,000 miles, giving a 30,000 × 30,000-mile
3×3 world with global coordinates −15,000 to +15,000 miles on either axis.
Center occupies −5,000 to +5,000 miles on both axes and coordinate (0,0) is
exactly at its center. `WorldFramework` reports these dimensions; the viewer draws the
Center-zone boundary and displays any coastline crossing it. The approved
continent is not reshaped automatically to fit the new boundary. Ocean buffer
width and zone addressing remain to be designed when adjacent zones are built.

Persist each live zone's seed, configuration, and H.O.G. generator versions.
Generator migrations and persistent local corrections will need explicit
operations; pre-alpha terrain may still be regenerated during development.

## Development stages

1. World framework and admin viewer — completed for previews.
2. Continental shape — reviewed.
3. Elevation and landforms — reviewed.
4. Hydrology — regional drainage reviewed. Step 4B adds local water, seasonal
   precipitation-driven discharge, and closed basins through a derived layer.
5. Climate — regional seasonal temperature and precipitation preview reviewed.
6. Biomes and vegetation — regional and close-up potential cover reviewed.
7. Geology and resources — regional preview implemented.
8. Civilization — skipped for the virgin initial world; Origin is hand-placed.
9. Natural features — lazy Zone C catalogs and admin inspection implemented. No procedural transportation.
10. MUD integration and persistence, including zone addressing.

The Center climate model currently assumes west-to-east moisture transport,
cooler temperatures northward and at higher elevations, and a moderated
seasonal temperature range near the coast. Its rainfall and temperature are
regional, long-term estimates, not exact weather or local microclimates. It
does not silently alter the Step 4 drainage graph. Step 4B uses those estimates
to refine runoff and plausible closed basins in its own derived graph without
changing the approved coastline or elevation.

Step 6 uses the shared elevation and climate grids to estimate broad potential
tree cover and biome categories. It does not create species, individual trees,
walkable forest boundaries, or local riparian and wetland polygons. The grid
spacing ranges from tens to nearly a hundred miles depending on seed. Water
continues to use Step 4 drainage and is drawn over vegetation in the viewer.
Users can select contour intervals down to 10 feet, but the fine lines only
interpolate the coarse grid; they are not a local terrain survey. Small intervals
are only rendered when zoomed in enough to see individual sampled cells.

Close-up Step 6 previews request a bounded, world-anchored vegetation raster at
4, 8, 16, 32, or 64 miles per sample as zoom and viewport size require. The
regional tree-cover, climate, interpolated relief, coastline, and lake mask
constrain a separate seed-specific patch field. Overlapping queries reuse the
same underlying coordinates and never regenerate coast, mountains, or water.
Recent seed geography is cached in process (up to three seeds) to avoid
recomputing the expensive elevation field for each pan; a restart clears it.
The endpoint remains admin-only. The finer cover is a subregional estimate,
not species, individual trees, narrow riparian belts, or surveyed local land.
The model currently stops at 4-mile samples; truly walkable vegetation needs
finer terrain, soils, and hydrology in a later iteration.

Step 7 adds inferred bedrock families and surface materials on the shared
regional elevation grid. Bedrock provinces respond to relief and to the
existing broad basin candidates; river alluvium follows the Step 4 network.
The viewer's Regional bedrock, Surface materials, and Resource potential
layers show building stone, limestone, clay, coal, iron, copper, gold-bearing
bedrock, and gold in river sediments. Gold in river sediments follows drainage
downstream from eligible gold-bearing source rock. Resource values 0–3 are
relative potential, not a count of discovered deposits or mine sites. The
regional grid cannot identify outcrops or mineral veins at walking scale.
Geology uses its own deterministic generator version and reuses cached
elevation and hydrology; it does not change the reviewed coastline, terrain,
climate, or vegetation.

Seed 13 and seed 89 put coordinate (0,0) in water. That is acceptable: the
coordinate is an arbitrary reference point, not a required land tile. The
starting settlement named Origin is hand-placed on suitable land after its
zone's geography is chosen; it need not sit at (0,0). The safety gradient and
remote-start distances belong to the settlement's location, not to the
coordinate reference. No coast adjustment is needed for those seeds.
World positions are stored in inches; for example, (22,000, 42,000) is about
0.35 miles east and 0.66 miles north of (0,0). Whether a chosen coordinate is
on land is determined by the selected geography.

## Step 4B local hydrology preview

Local hydrology version 3 distinguishes runoff from channels. The local routing
mesh carries diffuse hillslope water internally; it is not itself a creek map.
A surface channel starts only after sufficient contributing area and flow have
accumulated, or at a supported spring or substantial lake outlet. The initiation
area increases in dry climates and has a resolution-dependent lower bound.
Once a channel starts, its downstream connection is retained, and tributary
discharges accumulate. Zoom reveals smaller existing channels, never all runoff
mesh edges.

Local catchments drain toward their regional downstream boundary, not an
artificial sink in the middle of every regional cell. Priority flooding finds
spill elevations, physical distance resolves filled flats, and descending
ground uses the steepest available downstream neighbor. Rendering curves stay
within a narrow corridor around the routed reaches and retain exact junctions.
Neighboring refined catchments share a boundary confluence and inject the full
upstream discharge there. Refined routes replace the corresponding coarse
connector lines, preventing straight regional shortcuts from crossing creeks
without joining them. Unrefined halves remain at the edge of the detail region.
The regional elevation, climate, geology, and original hydrology inputs are
unchanged. Fine beds remain inferred terrain, not a surveyed walking-scale DEM.

The admin-only local-water endpoint limits fine queries to 64 regional cells;
each seed context retains at most 96 detailed cells. Stable feature identifiers
and geometry do not depend on viewport or season. The viewer provides separate
Major Rivers, Local Waterways, Lakes, Springs, and Wetlands controls.

Regression coverage includes the reported seed-103 area near (-70.59, 33.74)
miles, channel density, steepest descent on a slope, shortest drainage across
a flat, downstream connectivity, discharge budgets, and deterministic reloads.
Civilization and procedural transportation are not part of Step 4B.

Lake populations are allocated by coherent lake-country regions, precipitation,
and relief. The 10,000–15,000 continental target applies to lakes of at least one
square mile. Version 3 preserves their allocation, IDs, sizes and footprints,
and also preserves the 100–639-acre medium class. The smallest class (under 100
acres) retains about 40% of its former population. Filtering occurs after the
existing placement/collision pass so larger footprints do not shift.
Most large-category lakes are 1–5 square miles; 100+ square-mile lakes are rare.
Counts are density targets, not a reason to force overlapping footprints.

Ponds and first-order headwaters share a broad wet-region preference: existing
lake districts, cold northern lowlands, foothills near the continental spine,
and rainy lowlands retain more water. Dry plains retain substantially less.
Cold northern terrain is a glacial-country proxy, not a simulated glacial
history. The preference is normalized separately against pond candidates and
inland catchments, with probabilities capped at 1. Individual regions can keep
most features or lose almost all; the percentages are continental targets.

Roughly half of eligible original first-order channel heads are omitted, with
their reaches removed only up to the first confluence or protected node. Lake
connections, cross-cell entries, regional major channels and the outlet remain
protected. This is a single pass, never recursive pruning of higher orders.
All runoff still accumulates through the full routing mesh; hidden drainage
continues to feed downstream streams and rivers. Regional edges and discharge
are unchanged. Pond removal can change inferred local beds/routes; larger lake
footprints remain fixed, but their inferred hydraulic levels can change.

Module tuning constants in `local_hydrology.py`:

| Parameter | Default | Meaning |
| --- | --- | --- |
| `SMALL_POND_RETENTION` | `0.40` | Weighted target fraction of smallest ponds retained |
| `SMALL_POND_MAX_AREA` | `100 / 640` | Smallest-class upper bound in square miles |
| `FIRST_ORDER_RETENTION` | `0.50` | Weighted target fraction of eligible heads retained |

The summary exposes these values and reports planned smaller lakes after
thinning, plus the original candidate count. Routing diagnostics expose eligible
and hidden first-order heads, hidden samples, and regional retention probability.
No new viewer controls or dependencies are required. Fine catchments remain lazy
and bounded; the continent view still generates only substantial lakes and major
rivers. Walking-scale drainage can later be derived deterministically without
pre-rendering every minor runoff path.

Version 3 validation against main commit `6ef635d`: a full footprint census
found the following changes. Medium (100–639 acres) and larger lake footprints
were retained; the headwater check sampled 32 catchments per seed, evenly spaced
across the dry-to-wet region ranking (128 total). The headwater percentage is for
eligible first-order sources, not all river segments.

| Seed | Ponds before → after | Pond reduction | Eligible heads removed | Lakes ≥1 sq mi, unchanged |
| --- | --- | --- | --- | --- |
| 103 | 60,534 → 24,263 | 59.92% | 169 / 323 (52.3%) | 12,048 |
| 47 | 62,557 → 25,224 | 59.68% | 168 / 332 (50.6%) | 10,815 |
| 13 | 67,396 → 26,885 | 60.11% | 140 / 285 (49.1%) | 11,157 |
| 867359018957601 | 71,192 → 28,491 | 59.98% | 157 / 320 (49.1%) | 13,422 |

The same samples preserved regional edges exactly and outlet discharge to
floating-point tolerance. Regression tests cover unchanged medium/large
footprints, regional concentration, downstream continuity, major channels,
water budgets, cache eviction, viewport/season determinism and API version 3.

Springs redistribute each catchment's water budget and favor aquifer geology,
rainfall, slope, elevation, and proximity to the continental spine. The older
Step 4B geothermal labels use volcanic bedrock; Step 9 derives non-volcanic
deep-circulation heat separately without changing the reviewed water network.
Wetlands distinguish marsh, swamp, bog, and
wet meadow using moisture, relief, temperature, vegetation, and bedrock.
Dry-region closed lakes may have no surface outlet; wet-region lakes have one
resolved outlet and may have multiple inflows. Waterfall and rapids flags are
hydrological potential only; Step 9 resolves physical features separately.

Detail level 0 resolves large lake footprints and regional rivers; lake outlet
metadata is resolved with the fine catchment at levels 1–3. Local channels and
lake bowls infer missing fine relief beneath a coarse regional model. This is
not yet the persisted, walkable terrain and hydrology integration of Step 10.

## Step 9: Natural Features

`natural_features.py` derives a versioned catalog from the existing local hydrology
context. It does not regenerate coastlines, mountain ranges, water, vegetation,
or bedrock. Only Zone C, −5,000 to +5,000 miles on each axis, receives features.
Existing continental land outside that boundary is left unchanged. IDs include
the seed, zone, natural-feature version, and stable source cell/site or water ID.

Implemented families include:

- Riffles, rapids, cascades, waterfalls, river gorges, dry sandstone slot canyons,
  substantial confluences, and low-gradient river islands.
- Solution, fracture, erosion, and sea cave entrances; sinkholes and karst terrain.
- Cliffs, escarpments, ravines, mesas, buttes, hoodoos, arches, stone spires,
  exposed rock faces, rocky outcrops, talus and scree. Extremely rare supported
  residuals can be balanced boulders or stone needles.
- Sandy/rocky beaches, sea cliffs, sea caves/arches/stacks, and lake islands.
- Natural mountain saddles/passes, isolated sampled peaks, and high basins.
- Warm/hot springs, geothermal pools, carbonate terraces, rare geysers and
  coherent geothermal fields. These use groundwater and inferred deep crustal
  circulation, with no generated volcanoes, lava, cones, or lava tubes.

Physical suitability precedes deterministic occurrence decisions. Water features
use the actual Step 4B channel paths, mean and seasonal discharge, width and
available hydraulic drop. A waterfall consumes only a fraction of its reach's
drop; a rare resistant ledge may concentrate that drop even when the multi-mile
mean gradient is modest. Significance considers height, width and flow rather
than imposing an Earth-landmark height cap. Geological features use bedrock,
relief and weathering climate. Coast sites intersect the approved outline;
they do not redraw it. Passes require opposing rising and descending terrain
samples, and imply no road, trail, or guaranteed walkable crossing.

Common/notable/exceptional are internal admin categories. Physical significance
sets the category; deterministic occurrence gates retain 16% of notable candidates
and 0.2% of exceptional candidates after environmental and site eligibility.
Unusual balanced-boulder/needle forms have their own much rarer gate. These are
not percentages of land or all features. Unsupported terrain stays ordinary;
there is no quota forcing landmarks into every region or seed.

All names start null. Metadata records dimensions, extent, orientation,
prominence, vegetation/terrain concealment, unobstructed visibility and audibility
estimates, plus placement evidence. These estimates are inputs to future
discovery, not actual visibility tests. Cave metadata groups nearby compatible
openings into deterministic potential-system regions; connectivity remains
unresolved and no underground graph or explorable rooms are generated.

### Viewer and API

Open `/admin/world`, select a seed, and **Generate Details**. Enable **Natural
Features → Show features**. At increasing zoom, the overlay reveals exceptional,
then notable, then common features (1, 5 and 20 pixels/mile, subject to the query
budget). Continental views generate no icon catalog. Family filtering works
independently of the existing water and map-layer controls. Hover over a diamond
for dimensions, rarity (admin only), bedrock and placement reasons. Use **Inspect
location** with X/Y in miles to jump to a close view. Example review coordinates:

| Seed | X miles | Y miles | Feature |
| --- | ---: | ---: | --- |
| 103 | 1304.97 | -879.09 | Notable talus slope |
| 47 | -1928.27 | -362.30 | Notable exposed rock face |
| 13 | 884.20 | -1128.32 | Notable talus slope |
| 867359018957601 | 1872.23 | -655.63 | Notable talus slope |

The admin-only endpoint is `/api/admin/world/origin-natural-features`, accepting
`seed`, `west`, `south`, `east`, `north` (miles) and `detail` 0–3. Bounds must be
finite and ordered. Local requests exceeding 64 regional catchments including
the extent halo return HTTP 400 with a zoom-in instruction. Detail filters the
same canonical catalog; viewport, season, family filter and cache eviction do
not change feature identity or geometry. Responses are copies, not mutable cache
objects. The viewer cancels outdated requests and rejects stale seed responses.

Each natural-feature seed context retains at most 96 cell catalogs, with at most
three cached natural-feature contexts. Regional land sites have only 64 candidate
positions per cell; channel sites reuse the bounded Step 4B mesh. No full-zone
microfeature census is stored. A 40-catchment sample for each requested seed took
about 1.1 seconds cold and 0.025 seconds cached on the development machine,
excluding the existing geography/context setup (roughly 8–10 seconds). Those
samples contained 828–934 common and 3–9 notable features per seed, and no
exceptional features; the small sample is not a zone-wide rarity estimate.

### Validation and limits

Automated coverage checks bounds, admin authorization, stable overlap/zoom/season
results, cache eviction and copy isolation, all four requested seeds, unchanged
water inputs, limestone eligibility, ordinary flat/dry terrain, rare extreme
waterfall budgets, flow-sensitive significance, cave potential IDs, and
non-volcanic thermal clusters. The full existing suite remains applicable.

Geometry remains inferred from regional terrain and the Step 4B mesh. Feature
extents and island residuals are metadata/markers, not new walkable terrain or
carved water polygons. Coastal interpretation currently covers the families
listed above; detailed bay/lagoon/barrier-island/fjord morphologies and full
erosion or subsurface simulations are not introduced. Steam vents/mud pots are
not inferred without a stronger heat/chemistry model. Review coastal character,
waterfall density and unusual landforms before selecting a permanent world seed.
No naming, player discovery, underground exploration, persistence, civilization,
transportation, additional zones or zone knitting is part of this stage.

## Step 10: MUD integration and persistence

Thomas authorized Step 10 on 2026-09-26 with the requirements below. During
implementation he explicitly chose **gated runtime contracts pending a separate
walking-geometry milestone**, and **gated confirmed hazard transitions pending
consequence handlers**. These are deliberate phase boundaries, not completed
walking gameplay. Step 10 as a whole remains incomplete.

### Ownership and regeneration

HOG owns natural geography. Generated caches are disposable performance data;
the game database owns constructed cabins, roads/bridges, possessions, permanent
modifications and placement requirements. Live characters, creatures and transient
objects form a separate layer. Do not convert terrain cells/chunks into MUD rooms.

Current HOG generation wins after generator changes, even in explored areas.
This supersedes older wording that could freeze explored geography. Cache identity
includes zone, seed, relevant generator versions and configuration. Objects survive
cache deletion and regeneration. GROUND placement resolves an offset against current
ground; it must not retain an obsolete absolute elevation. Preserve explicitly
absolute placements and interior context; never reinterpret legacy z values blindly.

Ground-dependent placements require object-specific compatibility checks after
relevant generation changes: water, slope, river intersection, cliff clearance and
other placement constraints. A bridge/dock may accept water that invalidates a
cabin. Reports count objects checked, compatible cases and review-required conflicts.
Unknown geometry or missing requirements cannot establish compatibility. Flag
questionable cases for administration; never automatically relocate them.

### Local and regional geography

Materialize complete gameplay-resolution chunks around active interaction sites.
Do not progressively regenerate a chunk at different quality levels on approach.
Coordinates retain inch precision independent of sample spacing. Significant
shorelines, cliff edges, channels and paths need separate precise geometry so a
coarse grid never erases them. Overlapping generation and chunk boundaries must
agree; movement checks the whole segment, not just endpoints or cell centers.

500-by-500-foot chunks with 5-, 10- and 25-foot sampling are benchmark candidates,
not approved final gameplay dimensions. Measure fidelity and latency before choosing
walking defaults. Neighbor prewarming is permitted. Distant mountains, ranges,
valleys, lakes, forests, coasts, landmarks and broad biomes use regional summaries
and indexes, never force walking-resolution chunk generation just for LOOK.

### Structured perception contract

Search outward at 10, 20, 40, 80, 160 feet and so on to the effective horizon.
Bands are query bounds only: omit empty bands and preserve actual/estimated
feature distance. Favor believable selection over exhaustive enumeration.

| Candidate visibility class | Maximum useful range |
| --- | --- |
| Tiny: item, rabbit, small bush | 250 feet |
| Small: person, horse, wagon, cabin, large tree | 1 mile |
| Medium: grove, pond, creek corridor, large building, hill | 5 miles |
| Large: forest, sizable lake, major valley, prominent ridge | 20 miles |
| Landmark: mountain/range, huge lake, coastline | 100 miles |

These ceilings are not guaranteed visibility. Apply terrain obstruction/LOS,
size/prominence, local cover, observer elevation, significance and later weather
limits. Open/light/heavy woodland candidate range multipliers are 1/.5/.25.
Future weather ceilings: exceptionally clear 100 miles, clear 50, haze/cloud 20,
rain/snow 5, heavy precipitation 1, fog 500 feet, dense fog 100 feet. No weather
system is required now. Nearby/significant features outrank distant ordinary ones;
named/unusual/prominent features may receive extra significance. Do not reveal
hidden creek water; a visible valley/tree corridor is a distinct perceptual fact.

Return natural facts first, persistent objects second, live facts third. People
precede other live objects. Up to six visible people may be individually listed;
more than six are summarized by count. Apply the same threshold to live objects.
Return structured data for future normal/BRIEF descriptions, not a prose engine.
Unknown visibility must not disclose a candidate merely because HOG knows it.

### Movement and hazards

Immediate terrain access must be fast. Insignificant gradients do not interrupt
travel. Stop before an immediate dangerous drop, cliff, deep-water/river entry or
other hazard, identifying the exact threat. `north!` confirms only the previously
warned transition, revalidated against the current origin, destination, boundary
and generation identity. It never disables later protection. Missing/failed/timed
out geography stops movement safely.

The game has no implemented falling, drowning or terrain-injury consumer; detailed
rules remain deferred in design Q71–72. Thomas approved emitting a structured
confirmed transition while keeping the crossing blocked until a consequence
handler exists. Do not replace this with consequence-free dangerous movement.

### Implemented contract phase

Crown-Call reuses signed inch coordinates, the existing database object/Space
models, independent spatial-index chunks, HOG elevation interpolation, existing
version constants and the LocalHydrology context. No reviewed generator output
is changed. The optional WorldService geography dependency supplies a development
integration seam; the running application does not automatically switch to it.

- Immutable generation identities and complete chunk sample products.
- One-worker, bounded, single-flight background generation; nonblocking cache
  queries; LRU/idle eviction; explicit nine-neighbor prewarm; hit/miss/failure,
  generation-time and movement-query counters. A cache belongs to one identity.
- Exact segment/boundary intersection and bounded crossed-chunk inspection;
  missing or uncertified geometry blocks travel. Scoped warning confirmation is
  consumed and cannot authorize another hazard. Gates also protect exterior exits.
- Outward regional-query and LOS callback contracts, conservative unknown-LOS
  exclusion, structured layers and six-item summary behavior. Regional candidate
  indexing and certified LOS providers remain future work.
- An additive ground-placement attachment table for objects/Spaces, preserving
  all existing absolute coordinates. Ground resolver and read-only audit adapters
  consume current terrain. Unknown rules/geometry require review; non-point
  footprints explicitly require a future object-specific evaluator.

The regional sampling adapter always marks chunks **incomplete for walking**.
Its input spacing for seed 103 is about 64.65 by 35.79 miles. Natural-feature
markers do not supply exact cliff lips, channel cross-sections or water depths.
The separate geometry milestone must supply those facts before any certification.
In injected HOG mode, outdoor LOOK returns unavailable empty layers, outdoor
resource/object interactions are gated, and unknown movement is blocked.
Legacy gameplay remains the default and is not HOG-backed gameplay.

### Initial development measurements

Windows 11, Python 3.12.14, seed 103, one deterministic inland location; existing
regional context setup 5.89 seconds. Materialization medians use five samples;
warm cache medians use 1,000 queries. Memory is recursive Python object size,
not process RSS, and excludes the shared regional context.

| 500-foot chunk sampling | Samples including shared edges | Materialize median | Warm cache + surface median | Estimated chunk memory | Nine chunks prewarmed |
| --- | ---: | ---: | ---: | ---: | ---: |
| 5 feet | 10,201 | 39.84 ms | 6.7 microseconds | 1,878,891 bytes | 369.27 ms |
| 10 feet | 2,601 | 10.11 ms | 7.0 microseconds | 480,363 bytes | 94.79 ms |
| 25 feet | 441 | 1.71 ms | 7.0 microseconds | 82,867 bytes | 17.83 ms |

All nine accesses hit after prewarm completed. This is not a travel workload or
proof of player-visible prewarm effectiveness. Local warm-OS-cache file read plus
JSON decode medians were 8.60/2.22/0.44 ms respectively (20 reads); these exclude
reconstructing typed chunks, database/network latency and truly cold disk I/O.
They justify further investigation, not adoption of a persistent derived cache.

The sampling adapter provisionally uses 500 feet/25 feet: denser interpolation
adds cost without new physical detail. **No gameplay-resolution default is selected.**
Runtime cache trial values are 64 chunks, 300 idle seconds and 16 pending requests;
they are configurable test values, not measured production capacity/lifetime choices.
No disk/database geography cache is enabled. Exact reproduction:
`python scripts/benchmark_hog_runtime.py --seed 103 --output work/hog-baseline.json`.

### Next bounded phases and acceptance

1. Approve and build deterministic walking geometry consistent with regional
   coast/water/features; establish exact boundaries and depth/ground behavior.
   Test cross-chunk continuity, narrow features, coast/lake/river entry and cliffs.
2. Benchmark that complete product at candidate sizes/resolutions across seeds
   and terrain types. Measure movement/LOOK end-to-end, concurrency, shared-context
   memory, p95 latency, travel prewarm and cache pressure before selecting defaults.
3. Supply regional indexes and conservative LOS/cover/elevation evaluation without
   walking materialization for distant views. Integrate database/live fact suppliers.
4. Adopt GROUND semantics in gameplay and object-specific footprint audits,
   including interior/portal elevations, with deliberate legacy-placement migration.
5. Add approved consequence consumers, choose seed and hand-place Origin, then
   enable HOG gameplay on DEV and verify it before any separately authorized PROD release.

No weather system, final prose generation, civilization, complete perception/skills,
additional zones or unrelated mechanics are introduced by these contracts.

## Step 10 continuation: continuous travel contracts

Thomas subsequently resolved the missing pace/stamina decisions. Their authoritative
home is the **2026-09-26 continuous travel and stamina addendum** near the beginning
of [CCMUD_Design.txt](/CCMUD_Design.txt), including R=150 pounds, persistent selected
pace, continuous recovery/drain, endurance targets and the 20% start threshold.

The inspected application previously had fixed 100-foot command strides, not a
continuous-travel engine. The new controller implements the documented C04/Q57
speed architecture through existing WorldService, signed-inch coordinate persistence,
Space restrictions and WebSocket connections. It does not replace reviewed geography.

### Implemented behavior

- Character selected pace and stamina persist through an additive migration.
  Active travel never survives disconnect/reconnect. Pace commands while stopped
  only select pace; while moving they change pace immediately, preserving direction.
- Monotonic elapsed-time integration carries fractional distance between ticks.
  Database position retains inch precision. Direction changes, STOP, thresholds,
  exhaustion, standing recovery and pace-specific rates follow the design addendum.
  Supplied environmental/wound/load factors affect speed, not stamina rate.
- The WebSocket loop services integration independently of observations (default
  0.1 real seconds). Each traversed segment checks certified terrain and precise
  boundaries, including boundaries between samples. Existing Space walls remain
  blockers. Entering a different chunk requests bounded neighbor prewarming.
- Observations default to 15 real seconds and are configurable independently.
  A structured perception callback supplies facts; unchanged updates carry status
  without repeating facts. No sophisticated prose or synchronous generation is added.
- Hazards stop at the last inch coordinate strictly before the intersection. Events
  carry kind, threat, direction, boundary identity and location. Confirmation
  revalidates the stopped position, direction, generation and exact boundary.
  It authorizes one crossing, consumed by a subsequent timed integration; issuing
  confirmation itself never teleports. Later hazards still stop movement.
- A pure game-side consequence callback accepts/rejects a transition and returns
  events and whether ordinary travel may continue. HOG invents no fall/swim/damage
  rules. Absent handlers keep crossing gated. Owners commit state before applying
  external effects. A second boundary within the short crossing is not authorized.

Bounded catch-up defaults to 5 real seconds. Longer scheduler stalls stop travel
instead of making unbounded jumps. Safety depends on the scheduler being serviced:
the normal detection bound is the integration interval, never the observation
interval. Exact intersection checks prevent thin hazards from being skipped.

### Activation limits and next boundary

The application factory still leaves continuous HOG travel disabled. Certified
walking geometry, environmental/wound-rule adapters and perception providers remain
activation gates. The regional sampler remains uncertified. The controller handles
planar cardinal outdoor travel; physical ground/vertical transitions, continuous
interior travel and consequence consumers remain for geometry/gameplay integration.
The supplied controller is tested using certified synthetic fixtures and isolated
databases, not passed off as completed natural walking terrain.

Terrain movement factors and offline recovery are now settled and implemented in
the continuation below. The remaining design boundary is the deterministic local
geometry model, including how real drops, water and steep slopes are classified.
Settled pace, stamina, terrain-factor and encumbrance rules should not be reopened
to address those independent geometry gaps.

### Verification and measurements

Tests cover elapsed-time position and fractional carry, chunk traversal, configurable
observations/unchanged facts, a hazard around second 7, sub-sample boundaries, scoped
confirmation followed by another hazard, changed geometry, absent handlers, exact
endurance, start thresholds, terrain-only speed changes, persistent pace/reconnect,
cabin walls, isolated migration, and WebSocket updates without new commands.

Synthetic warm benchmark, Windows 11/Python 3.12.14, 5,000 repeats:

| Operation | Median | p95 |
| --- | ---: | ---: |
| Safe segment query | 5.3 microseconds | 5.6 microseconds |
| Hazard segment query | 6.6 microseconds | 7.1 microseconds |
| One 100-ms controller integration | 13.3 microseconds | 14.5 microseconds |

These measure a synthetic flat prewarmed provider with an exact boundary, excluding
database transactions/network costs. They do not establish real geometry fidelity or
supported player count. Run `python scripts/benchmark_travel.py --output work/travel-benchmark.json`;
raw results live in `docs/travel-contract-benchmark.json`. Prior sampling benchmarks
remain unchanged. The continuous-travel baseline suite was 138 passed with 13 upstream
deprecation warnings (158.90 seconds); changed-file lint passes.

## Step 10 continuation: terrain policy, precise transitions and offline rest

Thomas's terrain/grade tables and offline policy are authoritative in the leading
**2026-09-26 terrain movement and offline rest** section of the
[design record](/CCMUD_Design.txt). The new implementation preserves the runtime,
controller, observations, consequence contracts and existing benchmarks above.

`TerrainMovementPolicy` centralizes configurable defaults. The game-rule adapter
selects one effective surface class and supplies signed east/north rise/run grades;
the controller resolves uphill grade against travel direction. Downhill has no
speed bonus. The separate altitude-condition input must not duplicate the slope
factor. A factor below 0.5 is now valid for on-foot travel; explicit passability
and hazard classification govern obstruction. Pace and stamina rates are unchanged.
Steep downhill still requires difficult/hazard classification by certified geometry;
the speed policy alone does not establish safety or invent a numerical threshold.

Precise `Boundary` segments remain independent of samples, now including safe
movement transitions as well as hazards. Safe significant transitions silently
split integration and refresh rules at the first integer-inch position across the
edge; their speed-change position error is at most one inch, not 25 feet. Hazards
are tested with the original precise geometry throughout, including a hazard in
that final inch or across a chunk seam. Safe boundaries never require confirmation
or generate routine travel-stop messages. Providers must publish every relevant
boundary into crossed chunks with stable identities. Approximate fuzzy transitions
need no artificial exact edge. No regional generator output changed in this phase.

Migration `20260926_03` adds nullable UTC accounting time and normal resting-rate
snapshot to characters. A reconnect applies real elapsed offline recovery, caps it
at 20, and advances the checkpoint in the same row-locked PostgreSQL transaction.
Clean disconnect checkpoints without catch-up movement; repeated reconnects cannot
reuse elapsed time. A backwards wall-clock change preserves the timestamp high-water
mark. Legacy rows with no timestamp receive no invented elapsed recovery. Connected
states checkpoint at least every configurable second under normal scheduler service,
even when stamina is unchanged; a crash recovers from the last committed checkpoint.
Offline characters have no recovery ticks. A geometry-independent athletics callback
supplies the normal resting rate, so unloaded terrain need not block resting recovery.
Future recovery-prevention conditions remain outside this phase.

### What remains before Thomas can walk in DEV

**HOG-backed movement cannot safely be activated yet.** The regional sampler still
returns `complete=False`; the application factory still uses legacy movement.
The current work resolves policy and accounting, not physical world generation.

The smallest next bounded phase is **one certified HOG-derived DEV walking area**:

1. Review the deterministic local-geometry model for that area: ground profile,
   effective surface classes/directional grades, exact banks/shores/cliff lips and
   hazard classification. Physical drop, water-depth and steep-slope thresholds
   are consequential design choices; do not silently select them. Preserve HOG
   regional geography rather than substituting a synthetic flat fixture.
2. Generate/certify a bounded group of seamless chunks and prewarm nearby travel.
   Keep uncertified territory and hazardous crossings without consequence handlers
   blocked. Full swimming/falling systems are not prerequisites to walking safe land.
3. Connect actual character athletics/load and applicable condition inputs, ground
   height, safe DEV spawn, and basic structured travel/LOOK facts. Enable through
   an explicit DEV configuration and render actionable movement/hazard messages in
   the existing client. Preserve legacy object/Space placement until audited.
4. Apply the additive migrations on DEV and verify real login, movement, terrain
   transitions, chunk seams, exhaustion, hazards, disconnect/reconnect and latency
   with PostgreSQL and the browser. This task had no authenticated server connection;
   no live DEV migration/deployment or PROD operation occurred.

Final prose, all nine zones, complete regional perception, vehicles and dangerous
crossing consequences are not required to prove this bounded safe-land milestone.

### Terrain/recovery measurements

Warm synthetic benchmark, Windows 11/Python 3.12.14, 5,000 repeats per path:

| Operation | Median | p95 |
| --- | ---: | ---: |
| Safe segment query | 5.3 microseconds | 5.6 microseconds |
| Hazard segment query | 6.7 microseconds | 7.8 microseconds |
| Ordinary 100-ms controller step | 15.7 microseconds | 23.4 microseconds |
| Terrain/grade speed calculation | 0.8 microseconds | 1.0 microseconds |
| 100-ms step crossing a safe precise edge | 37.3 microseconds | 39.5 microseconds |
| Offline recovery calculation | 1.5 microseconds | 1.8 microseconds |

Run `python scripts/benchmark_terrain_movement.py --output work/terrain-benchmark.json`.
Raw evidence is in `docs/terrain-movement-benchmark.json`. These are CPU contract
measurements excluding database, network and real geometry generation; they do not
certify player capacity or live latency. Existing benchmark files remain preserved.

Final full suite at implementation/test revision `f938385`: **173 passed**, 13
upstream deprecation warnings, 167.45 seconds. Changed-file lint passes. The eight
pre-existing full-repository lint findings remain in untouched generator code/tests.
Coverage includes all factors and grade bands, unchanged pace/stamina, precise safe
and hazardous transitions (including exact tick endpoints), chunk seams, persisted
offline elapsed time, rapid reconnects, caps, clock correction and SQLite migration.
