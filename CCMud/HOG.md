---
title: Heart of Gold
description: Deterministic world-generation architecture, physical constraints, and preview limits.
reviewed: 2026-09-26
nav: hog
permalink: /CCMud/hog.html
---

This is the canonical HOG architecture reference, carried forward from Crown-Call's existing `docs/heart-of-gold.md` at revision `a6b2fc76dfa27f38eaf623034c98dd6f67c3975e`. Its detailed engineering content is preserved. Current progress and live-environment evidence belong in [Status](status.html). HOG applies only to the MUD.

* Contents
{:toc}

## Generator overview

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

Offline stamina recovery was not decided. This gated adapter accounts connected
time only and invents no offline recovery grant; resolve that policy before live
activation. The immediate next geometry decisions are Q60's Terrain Movement Factor
calculation and exact cliff/channel/shore geometry. Settled pace, stamina and
encumbrance rules should not be reopened to address these independent gaps.

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
remain unchanged. The complete regression suite is 138 passed with 13 upstream
deprecation warnings (158.90 seconds); changed-file lint passes.
