---
title: Game design
description: The MUD's accepted decisions, preserved in full and separated from tabletop rules.
reviewed: 2026-10-03
nav: design
permalink: /CCMud/design.html
---
**Object architecture review:** [Working Draft 5](objects.html) records the latest approved object decisions and supersedes conflicting historical alternatives. Thomas authorized Hunting Knife Slice 1 and the bounded Rabbit MOB / THROW / REMOVE interaction, subsequently extended to wounds, death, corpses, bounded SKIN, MAKE SPIT / GATHER FIREWOOD, the data-driven Craft Engine/web Builder, and compact Object Builder with linear real-time morphs on September 30. Other slices and deployment remain separate decisions; see current status for verification.


## Runtime configuration Phase 4: final existing-system conversion (2026-10-02)

The existing-system configuration conversion adds **22 restart-required controls**
with unchanged implemented defaults. This section supersedes earlier phase scope
statements that terrain factors, interaction, speech, local perception, backlog or
room-count policy remain outside Settings. Identity retains explicit activation;
all gameplay settings are read once into the immutable startup snapshot. Saving or
activating Identity cannot change running gameplay. No command, movement substep,
LOOK or SAY performs a configuration database query.

### Added controls and supported bounds

| Registry key | Current default | Allowed values / category | Shared consumers |
| --- | --- | --- | --- |
| `interaction.reach_ft` | 15 ft | 1–100 ft; Interaction; must not exceed local entity range | Outdoor GET/PICKUP, REMOVE, skin/craft inputs, door interaction and APPROACH stopping |
| `communication.say_range_ft` | 100 ft | 1–1,000 ft; Communication | Outdoor SAY and open-doorway hearing; same-ROOM speech remains local to the ROOM |
| `perception.object_range_ft` | 100 ft | 1–100 ft; Perception | Existing object/MOB selection, LOOK and persistent dwelling observations; no distant LOS/SCAN |
| `craft.work_backlog_limit_seconds` | 172,800 sec (48 hours) | 1–172,800 sec; Crafting | New craft admission is rejected at or above remaining backlog; existing deadlines and authored craft costs stay fixed |
| `dwelling.max_rooms` | 500 rooms | Whole numbers 1–500; Dwellings | New/modified live graphs, template drafts, usable validation, saves and text imports |

Internal inches and displayed backlog hours are derived. The former environment
variables `PICKUP_RANGE_INCHES`, `SAY_RANGE_INCHES` and
`OBJECT_LOOK_RANGE_INCHES` no longer override gameplay. The saved Settings values
are authoritative. Local sight certification retains its code-owned 100-foot
ceiling; graph/serialization support retains its code-owned 500-room ceiling.
Neither ceiling is editable or expanded by this phase.

Lowering room policy does not delete rooms, invalidate existing instances, or
prevent loading/exporting an already usable saved template. A subsequent authoring
save/import must satisfy the active policy, even for an existing graph. Template
and live ROOM editors obtain limits from server metadata, including the absolute
ceiling; they do not maintain an independent 500-room gameplay rule. Server
validation remains authoritative for all form/text/API paths.

### Terrain and uphill speed factors

Thomas explicitly approved these existing speed multipliers for Settings while
retaining terrain identities, classification, grade boundaries and all geometry
safety/passability rules in code. All factors accept finite numeric values from
**0.01 through 1.0**, rejecting booleans, strings, zero, negatives, NaN and infinity.
The supported tuning floor is 0.01; the existing no-speed-bonus
ceiling remains 1.0. Every factor is independent; no additional ordering constraint
is invented. Defaults and classifications are unchanged.

| Registry key suffix after `terrain.speed_factor.` | Default | Category |
| --- | --- | --- |
| `road`, `maintained_trail`, `grassland`, `firm_ground` | 1.00 each | Terrain |
| `light_woodland`, `brush` | 0.85 each | Terrain |
| `heavy_woodland` | 0.65 | Terrain |
| `rocky`, `rough` | 0.70 each | Terrain |
| `marsh`, `shallow_water` | 0.50 each | Terrain |
| `difficult` | 0.35 | Terrain |

| Registry key after `terrain.uphill_factor.` | Code-owned positive grade band | Default / category |
| --- | --- | --- |
| `gentle` | Above 0 through 5% | 1.00 / Uphill |
| `moderate` | Above 5 through 10% | 0.90 / Uphill |
| `steep` | Above 10 through 20% | 0.75 / Uphill |
| `very_steep` | Above 20 through 30% | 0.55 / Uphill |
| `extreme` | Above 30% | 0.35 / Uphill |

Directional grade projection remains unchanged. Level/downhill travel receives
factor 1, never a downhill bonus. Effective surface selection is still supplied by
HOG; a maintained road still overrides surrounding vegetation. The immutable
movement policy is derived from the active snapshot and reused in memory. Explicit
TravelController configurations and real HOG inputs consume the same authority.
A factor of 1 cannot permit a cliff crossing, deep-water entry, an uncertified
region or any other passage disallowed by geometry. Swimming effort and current
vectors retain their existing separate rules.

Migration `20261002_04`, after `20261002_03`, adds these 22 keys with frozen current
defaults. Existing saved keys, including Phase 1–3, already-present Phase 4 keys and
unknown/newer keys, win over seeds. It increments the configuration revision once
without changing characters, stamina, deadlines, rooms or content. Historical
migrations are unchanged; destructive downgrade is refused. Builder adds Interaction,
Communication, Perception, Crafting, Dwellings, Terrain and Uphill categories, with
server-supplied labels, units, numeric bounds, integer room validation and restart
status. Terrain/Uphill use the existing compact two-column layout.

### Final existing-system audit and ownership

This audit covers movement, stamina, water, interaction, communication, perception,
presentation, objects, crafts, dwellings/ROOMs, current combat/MOBs, character/account
policies, administration, persistence and HOG infrastructure. Classification records
semantic ownership, not a claim that every literal should become editable.

| Classification | Values / current owners | Reason and future boundary |
| --- | --- | --- |
| CONFIGURED | Identity (`game_settings`), pace/capacity/cadence, world and travel multipliers, real-time stamina and the 22 controls above | One typed registry and immutable runtime snapshot; earlier saved values/activation preserved |
| KEEP INTERNAL | Terrain identities/classification and 5/10/20/30% grade thresholds (`terrain_movement`, HOG providers) | Explicit approved boundary; speed factors alone are editable |
| KEEP INTERNAL | Character visibility 12,000 inches / 1,000 ft (`config`, `world`, `connections`) | Existing placeholder/presence query behavior, not completed distant-LOS policy; reconsider with LOS/SCAN |
| KEEP INTERNAL | Legacy 1,200-inch movement step (`config`, `world`) | Non-HOG compatibility path; continuous HOG movement uses configured paces, not this step |
| KEEP INTERNAL | Dwelling dimensions 5–100 ft (`dwellings`, `hog_structures`); placement maximum slope .05 (`hog_placement`, object/MOB placement) | Geometry certification and persisted placement contracts; no independent admin enlargement |
| KEEP INTERNAL | Carrying load curve .5/1/1.5 and ATHLX physical-speed divisor 20 (`travel`) | Adopted formula structure remains code-owned; capacity inputs and pace speeds are already configured |
| KEEP INTERNAL | Swim effort 1 mph; swimming/current onset 1 ft and wading limit 4 ft (`travel`, `water_travel`) | Coupled movement legality and boundary events; deliberate water redesign would need a coordinated contract |
| KEEP INTERNAL | Water geometry 3-ft certification (`hog_waterway`) | Separate geometry contract, not an interchangeable duplicate of the 4-ft player wading rule |
| CONFIGURED / DERIVED | Floating recovery (`travel`) | Uses configured Trudge gross recovery; no independent float-fraction setting |
| KEEP INTERNAL | Water current prose bands .25/1/2/3/4 mph and deep prose at 7 ft (`water_travel`, Cormac) | Descriptive vocabulary; does not determine permission or physical current |
| KEEP INTERNAL | Cormac nearby range 1,000 ft, distance/slope/tree/observation bands, verbosity 1/2/4, broad water 100 ft and wording (`cormac`, `world`) | Presentation policy, deliberately separate from reach and entity sight |
| KEEP INTERNAL | Eyes sampling distances, biome fractions/limits, rise/grade/retention/cover thresholds, feature ranges/limits (`cormac_eyes`, `hog_perception`) | Bounded engine observation and presentation contracts; not player entity perception |
| KEEP INTERNAL | THROW floor 10 ft, penalty per 10 ft, opposed d20, wound margin step 4, tiers and combination (`combat`, `world`) | Current tightly coupled interim combat rules; reconsider within approved future combat work, not this conversion |
| AUTHORED CONTENT | Rabbit Athletics/severity (`mobs`), armor +1/48-ounce profile (`combat`), wounds/death outputs (`mob_wounds`) | MOB-specific source-owned content/profile; future prototype authoring may relocate it, but no global rabbit controls |
| AUTHORED CONTENT | Object names/descriptions/weights/portability/tools/throwable/morph definitions; individual craft inputs/tools/outputs/quantities/work seconds | Existing object/Craft Builder and prototype authority; not duplicated in Settings |
| AUTHORED CONTENT | ROOM-link delay/stamina percentage/departure, room descriptions, dwelling geometry within certified bounds, doors/keys/bars | Existing graph/content authority; accepted ROOM costs remain fixed during a pending transition |
| KEEP INTERNAL | Craft cost ceiling 172,800 sec, link delay 3,600 sec, link cost 0–100%, graph links/text/name/description limits, morph transition cap 16 | Schema, serialization and resource ceilings; craft cost ceiling is distinct from backlog admission policy |
| KEEP INTERNAL | SHORE sector inference within 180 inches (`sectors`) and legacy 180-inch nearby wording (`world`) | Geographic classification / prose, not duplicate interaction reach |
| KEEP INTERNAL | Character name 3–40, account name 3–32, DEV ATHLX selector 0–20, initial walk/land/manual-water preferences (`world`, `auth`, `models`) | Identity/schema and explicit DEV testing/default-state contracts; no new account/chargen design |
| KEEP INTERNAL | Account roles, session lifetime/security, password constraints, login throttle 8/300 sec, credentials/origins/cookies (`auth`, `config`, `main`) | Security/deployment authority remains outside gameplay Settings |
| KEEP INTERNAL | LOOK/MOB entity caps 64; admin find/search pagination, mark IDs, jump timeout 30 sec, speech length 1,000 | Bounded queries, identifiers, asynchronous safety and protocol/resource limits |
| KEEP INTERNAL | Units, coordinate/odometer precision, tolerances, seeds/versions/world bounds, HOG size/spacing/cache/expiration/workers/prewarm, integration .1 sec/catch-up 5 sec/checkpoint 1 sec | Engine, deterministic geography, persistence and performance contracts; unchanged |
| KEEP INTERNAL | Reserved verbs, stable-ID syntax, request headers, session namespaces, DB retries and migration IDs | Protocol/schema compatibility; not admin game-design controls |
| DEFER WITH FUTURE FEATURE | New combat scheduler/phases/pins/tactical movement/FLEE; SEARCH/SCAN/EXPOSURE/HIDE/SNEAK, routes/chaining, new MOB AI/ranged systems, Telnet/ANSI/GMCP/maps | Unimplemented systems receive appropriate configuration when their own feature is designed; no placeholder keys |
| REQUIRES USER DECISION | None outstanding | Terrain-factor question resolved explicitly by Thomas; other candidates have the intentional ownership above |

**Audit conclusion:** there are no known existing global administrator-tunable
gameplay/design constants left improperly hardcoded within the implemented-system
scope. Internal rules/content listed above are deliberate boundaries, not forgotten
conversion work. Verification and final completion status are maintained in
[Current status](status.html). No generic Phase 5 is planned.

### Future-development configuration policy

Every future feature must classify its values during implementation. Existing or new
values that are global, administrator-tunable gameplay/design policy belong in the
typed Settings registry with validation, unchanged/approved defaults, migration,
activation policy, metadata/UI, consumer consolidation and behavior tests. Derived
values remain derived. Content belongs to its authoring system; units, engine,
geometry, security, protocol and safety invariants remain code-owned. Do not make
unimplemented features configurable in advance. Bring genuine ownership ambiguity
to Thomas rather than silently deciding it to claim completion.

## Runtime configuration Phase 3: movement and stamina (2026-10-02)

This implemented contract supersedes the historical Phase 2 acceleration and the
older 2x-only distance examples. World time and travel convenience are independent
restart-only settings. Movement is currently the only world-time consumer; no
accelerated calendar, combat clock or Phase 4 system is implemented.

`physical_mph = base_pace_mph * (1 + ATHLX/20) * existing_conditions`

`distance_inches = physical_mph * 63360/3600 * world.time_multiplier * travel.convenience_multiplier * real_seconds`

Defaults are **1.75 world time**, **2 travel convenience**, a combined **3.5x**.
The former `travel.geographic_multiplier=24` is removed, not multiplied in.
Displayed land mph includes Athletics and applicable conditions, but excludes both
acceleration factors. Default base paces are **0.85 / 3 / 5 / 8 / 11 mph**.
Capacity and terrain/grade/load rules are unchanged. Water retains its 1 mph swim
effort, currents, depth and legality rules; only geographic displacement uses the
same two multipliers. Water display remains actual physical vector speed.

| New setting | Default | Validation |
| --- | --- | --- |
| world.time_multiplier | 1.75 | finite 0.1-24 |
| travel.convenience_multiplier | 2 | finite 0.1-24 |
| stamina.maximum | 20 | finite 0.1-10,000 points |
| stamina.restart_fraction | 0.20 | finite 0.001-1 |
| stamina.rest_base_per_second | 1/15 | finite 0-100 points/real second |
| stamina.rest_athletics_factor | 0.1 | finite 0-10 |
| stamina.recovery_fraction.trudge/walk/jog/run/sprint | .9/.7/.3/.1/0 | each finite 0-1 |
| stamina.drain_fraction.trudge/walk | .35/.7 | each finite 0-10 |
| stamina.endurance_base_seconds | 5 | finite 0.1-3600 real seconds |
| stamina.endurance_athletics_seconds | 1 | finite 0-3600 seconds per Athletics |
| stamina.endurance_factor.sprint/run/jog | 1/2/4 | each finite .01-100 |

Standing recovery `R = rest_base_per_second * (1 + rest_athletics_factor * ATHLX)`.
Moving gross recovery is `R * recovery_fraction[pace]`. Trudge/Walk gross drain is
`R * drain_fraction[pace]`. Jog/Run/Sprint endurance is
`D = (endurance_base_seconds + endurance_athletics_seconds * ATHLX) * endurance_factor[pace]`;
gross drain is `gross_recovery + maximum/D`. Integration uses **real elapsed
seconds**, without world or travel acceleration. Swimming uses configured Jog
rates; floating keeps configured Trudge gross recovery with zero drain.
At defaults, ATHLX 10 still exhausts a full Sprint pool in 15 real seconds.

Restart requirement is `maximum * restart_fraction`, including messages.
ROOM links retain their authored percentage, applied to configured maximum when
a journey is accepted; its accepted cost is charged once on completion. Existing
pending journeys are not repriced. Disconnect/restart cancels them as before.
New characters receive configured maximum. Startup performs one bounded SQL
update of characters above a lowered maximum; increasing maximum preserves
absolute points. It does not rescale, grant points, or rewrite timestamps/rates.
Offline elapsed time continues to use its **persisted previous rate** until the
normal recovery checkpoint; the current maximum caps recovery. New intervals use
the new rate. Saved malformed settings fail before clamping or publishing an
active snapshot. Save and Activate Identity do not clamp characters.

Migration `20261002_03` removes the legacy key, explicitly sets 1.75/2 and Trudge
0.85, adds stamina defaults, and preserves identity, other saved travel settings,
characters and authored content. It intentionally does not reinterpret a saved
legacy multiplier. Registry defaults are authoritative; migrations freeze their
historical values. Pace ordering remains validated; booleans, numeric strings,
NaN and infinity are rejected.

Builder Settings provides compact Identity / Travel / World / Stamina categories.
Gameplay Save requires server restart; Activate Identity remains branding-only.
All gameplay consumers receive the same startup snapshot without settings SQL in
the movement loop. No derived speed, restart-point or net-drain setting is added.

Ideal distances: ATHLX 0 Walk travels 924 ft in 60 real seconds; ATHLX 20 Walk
travels 1,848 ft. ATHLX 10 Sprint travels 1,270.5 ft in its 15-second full-pool
endurance. ATHLX 0 Sprint's rate would cover 3,388 ft in 60 seconds, but normal
stamina exhausts after five seconds (about 282.33 ft); 60 seconds is a rate
example, not permission to sprint beyond exhaustion.

## Historical Phase 2: existing travel (2026-10-02)

Implemented in Crown-Call `3480055`; migration `20261002_02` adds the nine current
travel defaults to the singleton settings record, preserving saved identity and
existing values. This is configuration conversion, **not new movement design**.

| Key | Default | Units | Valid range |
| --- | --- | --- | --- |
| travel.pace_mph.trudge | 1 | mph | 0.1–50 |
| travel.pace_mph.walk | 3 | mph | 0.1–50 |
| travel.pace_mph.jog | 5 | mph | 0.1–50 |
| travel.pace_mph.run | 8 | mph | 0.1–50 |
| travel.pace_mph.sprint | 11 | mph | 0.1–50 |
| travel.geographic_multiplier | 24 | multiplier | 0.1–24 |
| travel.observation_seconds | 15 | real seconds | 1–300 |
| travel.capacity_base_lb | 150 | pounds | 1–10,000 |
| travel.capacity_athletics_factor | 0.05 | factor per Athletics | 0–1 |

All are finite numbers, not numeric strings or booleans. Server validation also
requires Trudge <= Walk <= Jog <= Run <= Sprint. Bounds limit accidental extreme
inputs; they do not certify successful movement through unprepared geography.
The multiplier ceiling deliberately does not exceed the existing 24× acceleration.

Admin-only Builder Settings now has Identity and Travel categories with labels,
units, bounds and activation information. Save remains revision-safe. Travel
changes are **restart-required**; Activate Identity never activates pending travel
values, even after a mixed save. Saved revision, Identity active revision, Travel
startup revision and restart-pending differences are separately visible. Changing
category discards unsaved form edits; save before switching. No gameplay reload or
new server-control button is added.

Startup supplies one immutable validated snapshot to the existing HOG travel
controller. Identity activation cannot mutate that controller's travel values.
Land speed, swimming/floating geographic displacement and displayed physical mph
share one configured multiplier/conversion. Pace membership is separate from
configured speed values. Capacity remains `base × (1 + factor × Athletics)` with
the existing load slowdown curve unchanged. Observation cadence applies to newly
scheduled observations after startup; no missed-observation replay or retrospective
movement reinterpretation is introduced. Normal disconnect/restart stops travel.

No settings database reads occur during movement. Test controllers can receive
explicit immutable configurations without loading a live database. Unit conversions,
reverse-display coefficients and other mathematically derived values are not knobs.
Registry defaults own runtime defaults; migration literals remain frozen history.

All seeded behavior remains unchanged: 24× geographic movement, Trudge 1 mph,
no direct Athletics speed scaling, no accelerated world clock. The proposed 2×,
0.85-mph Trudge, Athletics speed formula and 1.75× world clock remain future design.
Stamina (Phase 3), terrain/uphill factors, water thresholds/effort, combat, pins,
FLEE, perception, SEARCH/SCAN/routes, generation and authored content are deferred.
Only water's already-shared geographic multiplier is consolidated here.

## Runtime configuration Phase 1: identity (2026-10-02)

Phase 1 was implemented in Crown-Call `4c9187a`. **game.name** and **game.short_name** were the only settings
configurable in that phase; Phase 2 above adds restart-only Travel settings. Migration `20261002_01` creates the singleton
`game_configuration` row (fixed ID 1, revision, JSON values, updated_at, updated_by)
and seeds **Crown & Call** / **CCMUD**. DEV and PROD own independent database values;
no automatic cross-environment synchronization exists.

`Admin Settings → validated server API → database revision → explicit activation
or startup → immutable in-memory snapshot → runtime consumers`.

The code-owned typed registry supplies defaults, category, label, description,
constraints, activation policy and public-branding classification. Administrators
cannot edit validation metadata or add arbitrary keys. Names are nonempty plain
text, at most 80/16 characters respectively, with no markup, control characters
or edge whitespace. Runtime HTML titles escape text rather than injecting HTML.
Unknown keys cannot be submitted; unknown stored keys from a newer version remain
untouched during an older server's edits. Missing recognized keys use versioned
code defaults and are identified as defaulted. Invalid recognized values fail
validation; a missing row/table or database error prevents safe startup.

The compact `/builder` Settings tab is **admin-only**. Ordinary Builders retain
Crafts/Objects but have no Settings API access. Existing session authorization and
`X-Crown-Builder` write protection apply. GET/PUT `/api/builder/settings` reads/saves;
POST `/api/builder/settings/activate` activates the explicitly reviewed saved
revision. Optimistic revision checks reject stale saves/activation with conflict
responses. SQL compare-and-update protects concurrent saves. Save does not change
running branding; the UI shows saved versus active revision and offers Reload.

Startup loads/validates the saved record before serving. Explicit activation
validates a complete candidate before replacing the active immutable snapshot;
failure retains the previous active identity. Runtime title consumers perform
**no configuration database queries**. Client title, Builder title (`name · Builder`)
and FastAPI/OpenAPI title use game.name. OpenAPI cache regeneration is serialized
with activation. New page loads see activated identity; existing tabs refresh.
Settings activation also updates that Builder tab's title. game.short_name is
stored/validated for future use and has **no game display consumer yet**.

Activation is process-local; Phase 1 does not introduce distributed hot reload.
If multiple workers are introduced, restart all workers to load one saved revision.
Future gameplay configuration should initially require restart unless separately
approved. No gameplay settings are converted here.

**Cormac and Heart of Gold/H.O.G. remain engine identities.** Internal packages,
services, cookies, browser-storage namespaces, technical export identifiers and
request headers remain unchanged. Authored names/descriptions are content, not
branding templates; no global replacement occurs. Craft costs, object capabilities
and ROOM-link values remain owned by their content definitions. No Help Editor,
profiles, import/export or configuration-history application is added.

Phase 1 recorded the following policy, now implemented by Phase 3:
preserve absolute stamina, clamp above a lowered maximum and grant no free stamina
when increasing it. Thus 18/20 becomes 15/15 when lowered to 15, while 10 stays 10;
18/20 becomes 18/30 when raised to 30. Phase 3 now implements this policy.
Phase 3 above owns the deliberately changed movement formulas.

## Escape, Athletics movement and moving HIDE (2026-10-02)

This section owns approved combat/escape design direction; only its ordinary
Athletics movement is implemented by Phase 3 above.
It takes precedence over conflicting older combat pacing/escape assumptions in
C05/C06 of the [preserved detailed record](/CCMUD_Design.txt). That record remains
available as historical/alternative design; its detailed turn machinery is not
silently replaced by an invented new algorithm.

### Current implementation versus intended behavior

Phase 3 above implements Athletics physical speed, Trudge 0.85 mph, the separate
1.75x/2x movement factors and configurable real-time stamina. The remaining combat,
FLEE, pins, moving HIDE and SNEAK proposals below are not implemented. The bounded
rabbit THROW/wound interaction is not a full encounter system.

### Rapid combat clock: current intended design

**Design only, not implemented.** Combat is player-controlled with no automatic
fighting: typing HIT once never repeats attacks. Repeating **30-real-second rounds**
contain **15 seconds Action**, then **15 seconds Preparation**. All combatants share
these phases; there are no Phase 1/Phase 2 combatant assignments. Each combatant
gets **at most one combat action per round**, during Action. HIT, SHOOT/FIRE,
DEFEND and FLEE are examples of future actions, not implemented command claims.
Doing nothing forfeits that round's opportunity; no automatic attack substitutes.

Preparation allows assessment, communication, AIM/appropriate preparation and
choosing the next action. A combat action entered then queues for the next Action
phase, never executes immediately, and executes at phase opening if still valid.
Another valid action replaces the queue. **X cancels the queued combat action;
STOP does not.** Existing STOP behavior is unchanged by this documentation.

### Phase-start state and simultaneous consequences

Conceptually snapshot combatants at Action-phase start. Actions use their
phase-start combat state; wounds and other consequences accumulate and settle
for mechanical effect in the **next Action phase**. Prose may appear as actions
occur. Command arrival order and network latency must not become initiative.
For example, AA's queued blow gives A a MORT wound, but A's still-unused action
in that same phase can also give AA MORT. Neither action is canceled merely
because the other processed first: mutual mortal blows, and potentially both
deaths, are intentional. This does not establish a new universal instant-MORT
death rule. Exact snapshot scope and spatial revalidation are open below.

These rules supersede conflicting older C05/C06 initiative/individual turns,
long decision windows and bans on simultaneous phase resolution. Preserve those
older designs as historical/alternative material, not competing current clocks.

### Group friendly-fire confirmation

Do not infer teams, hostility graphs, enemy-of-my-enemy relationships or dynamic
combat factions. Established player grouping/follow relationships may supply a
simple safeguard: HIT A against one's group member first warns:

```text
A is a member of your group!
HIT A again to confirm.
```

Repeating HIT A deliberately confirms and may proceed under the normal clock.
No global betrayal announcement is required; ordinary perception/communication
reveals events. Confirmation lifetime and its interaction with queued commands
remain implementation details to settle, not an inferred faction system.

### Combat pins and tactical movement

Every genuine weapon attack reaching **opposed-roll resolution** creates or
refreshes a geographic combat pin, whether the attacker wins or loses. An
aborted attack, absent/invalid target, invalid reach/range before resolution or
other failure to reach an actual opposed weapon attack creates no pin.

Each pin has a world coordinate, **45-real-second lifetime** and **1,500-foot
radius**. Further resolved attacks may create more pins; a moving fight can leave
a temporary chain/cluster, and old pins expire. Pins belong to geography, not
combatants. Anyone within **any** active pin is subject to tactical movement:
participants, group members, reinforcements, bystanders and passing travelers alike.
This prevents accelerated travel across the final distance into an active fight.

Normal travel uses planned convenience acceleration; active pin areas instead
heavily reduce geographic distance to match tactical combat time. Approximately
**0.1× movement distance** is a **working design value**, not implemented or final
tuning. Its multiplication basis must be specified before implementation; do not
silently combine it with legacy 24×, planned 2× or world-time scaling.

### Permadeath and survival intent

Characters may represent months or years of investment. Starting a fight should
be relatively easy, winning harder, preventing escape harder still. Killing
someone actively trying to survive should take substantial effort, favorable
circumstances, persistence or serious mistakes by the victim. PvP and murder
remain possible: these are survival-biased mechanics, not consent or immunity.

### Athletics speed and real-time endurance

Implemented unobstructed physical/displayed land speed:

`speed_mph = base_pace_mph × (1 + effective_ATHLX / 20)`

Terrain, grade, load and other applicable movement constraints still matter.
The table is ideal-condition mph, rounded to two decimal places:

| ATHLX | Multiplier | Trudge | Walk | Jog | Run | Sprint |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | 1.00 | 0.85 | 3.00 | 5.00 | 8.00 | 11.00 |
| 1 | 1.05 | 0.89 | 3.15 | 5.25 | 8.40 | 11.55 |
| 5 | 1.25 | 1.06 | 3.75 | 6.25 | 10.00 | 13.75 |
| 10 | 1.50 | 1.28 | 4.50 | 7.50 | 12.00 | 16.50 |
| 15 | 1.75 | 1.49 | 5.25 | 8.75 | 14.00 | 19.25 |
| 20 | 2.00 | 1.70 | 6.00 | 10.00 | 16.00 | 22.00 |
| 25 | 2.25 | 1.91 | 6.75 | 11.25 | 18.00 | 24.75 |
| 30 | 2.50 | 2.13 | 7.50 | 12.50 | 20.00 | 27.50 |
| 35 | 2.75 | 2.34 | 8.25 | 13.75 | 22.00 | 30.25 |
| 40 | 3.00 | 2.55 | 9.00 | 15.00 | 24.00 | 33.00 |

With Phase 3 default settings, stamina maximum is 20. For ATHLX A, standing recovery is
`R = (1 + A/10)/15` points per **real second**. Recovery and expenditure are
integrated together: TRUDGE recovers 0.9R and spends 0.35R (net +0.55R); WALK
recovers and spends 0.7R (net zero). JOG recovers 0.3R and spends that plus
`20/[4(5+A)]`; RUN recovers 0.1R and spends that plus `20/[2(5+A)]`.
SPRINT has no recovery and spends `20/(5+A)` per real second. Thus full-pool
sprint duration is `5+A` real seconds, while A remains constant. Travel
acceleration does not multiply these stamina rates.

### Distance calculation convention

Phase 3 resolves the earlier clock conflict: use both separate factors, currently
1.75 world time and 2 travel convenience, with real-time endurance. The previous
2x-only examples are superseded.

`distance_feet = 11 * (1 + A/20) * (5280/3600) * 1.75 * 2 * (5+A)`

Ideal full-stamina sprint at default settings, without terrain/load slowdown:

| ATHLX | Sprint mph | Real seconds | Geographic feet (nearest foot) |
| --- | --- | --- | --- |
| 0 | 11 | 5 | 282 |
| 1 | 11.55 | 6 | 356 |
| 5 | 13.75 | 10 | 706 |
| 10 | 16.5 | 15 | 1,270 |
| 15 | 19.25 | 20 | 1,976 |
| 20 | 22 | 25 | 2,823 |
| 25 | 24.75 | 30 | 3,811 |
| 30 | 27.5 | 35 | 4,941 |
| 35 | 30.25 | 40 | 6,211 |
| 40 | 33 | 45 | 7,623 |

Athletics affects both physical speed and endurance. Combat-pin exemption remains
future design; these distances describe ordinary implemented travel.

### Successful FLEE: tactical-time exemption, not a stat burst

**Current design supersedes the earlier Adrenaline proposal.** Anyone inside an
active combat-pin area may attempt FLEE, including an uninvolved traveler. On
success, the character immediately becomes **exempt from combat-pin tactical
movement restrictions** and uses normal travel timing to physically escape.
There is **no +20 ATHLX, special speed bonus or special endurance bonus**. Actual
ATHLX, normal speed, stamina, terrain, wounds and other normal movement factors
apply. Fear/adrenaline may appear in prose only. FLEE is not teleportation.

Successful FLEE immediately sets effective **WEAPN = 0, RANGE = 0, AGGRESSION = −10**
during recovery. Other Caps are not automatically reduced; THIEV remains usable.
Zero WEAPN/RANGE is not automatic failure of every offensive command. These
penalties commit the character to escape rather than fast offensive repositioning.

FLEE grants no invulnerability. An opponent with an unused action in the current
Action phase may attack if the fleeing target remains visible, in valid weapon
range and otherwise legal. A resolved attack creates/refreshes a pin normally;
**that new pin does not by itself revoke the successful fleeing character's
exemption**. Pursuit remains possible, without perfect positional knowledge after
line of sight is broken. Geography, changed direction, SNEAK and HIDE support escape.

### Five continuous minutes outside all pins

FLEE recovery is **not** a five-minute wall-clock expiry after the command:

| Location/state | Recovery countdown | Effective combat values |
| --- | --- | --- |
| Inside any active pin | Reset/pinned at 5:00; no countdown | WEAPN 0, RANGE 0, AGGRESSION −10 |
| Outside all active pins | Count down in real time | Same penalties until complete |
| Re-enter any pin before completion | Immediately reset to 5:00 | Same penalties |
| Five uninterrupted real minutes outside all pins | Recovery complete | Restore normal values |

Returning with 0:30 remaining resets to 5:00, rather than pausing at 0:30.
Pin expiry/creation changes whether a location is inside any active area; all
active pins matter. Bystanders use the same rule: Miss Kitty caught in a nearby
fight's radius may FLEE, regain normal travel timing and receive the same penalties
and uninterrupted-outside recovery requirement.

There is **no FLEE-command cooldown**. A character caught/re-engaged 30 seconds
later can attempt FLEE again, subject to the normal action clock; recent FLEE is
not a reason to refuse. Penalties never stack numerically: they remain 0/0/−10.
Re-entry already resets/pins recovery; another success again releases tactical
movement. Exact re-engagement/exemption lifetime is an open boundary below.

### Superseded FLEE proposal and wilderness limits

The earlier **+20 effective ATHLX for 45 seconds** and **90-second WEAPN/RANGE
suppression** are historical, superseded proposals, not current mechanics or
implementation targets. The replacement is tactical-time exemption plus the
0/0/−10 values and continuous-outside recovery above. General proposed Athletics
speed scaling and moving HIDE below remain; they confer no special FLEE bonus.

Breaking combat is distinct from escaping a threat: a bear may still pursue.
Terrain, concealment, distance or loss of interest/motivation may end pursuit.
MOB pursuit and behavior toward incapacitated characters remain deferred. Animals
must not be described as always abandoning unconscious victims; wilderness
encounters retain a genuine possibility of death.

### HIDE while moving and SNEAK

This is the normal proposed moving-HIDE behavior, **not limited to combat**:

`MOVING → HIDE → SEARCHING FOR CONCEALMENT (still moving) → suitable cover → STOPPED/HIDDEN`

HIDE does not stop the character immediately. It acknowledges the search, for
example, “You begin looking for a suitable place to conceal yourself.” Once an
appropriate hiding place is found, the character settles into it, stops moving
and enters a concealed state (reduced exposure, not invisibility; see the unified
perception section). Example final prose: “You spot a thick tangle of
brush beneath the trees, slip into it, and settle out of sight.” Exact prose is
not fixed; this is not magical invisibility at the command's coordinate.

Dense woodland may offer cover quickly. Brush, broken ground, rocks, ravines and
structures may help depending on circumstances. Open prairie can require much
more travel or offer no suitable cover at all. RUN/SPRINT creates distance but is
conspicuous; SNEAK moves more slowly while attempting not to reveal position;
HIDE seeks a place to stop and conceal oneself. These complement each other:
FLEE → normal travel timing → SPRINT → break sight → change direction → SNEAK → HIDE/search → conceal.

### Deferred implementation decisions

Do not invent rules for these remaining boundaries:

- FLEE success/eligibility checks and how a bystander joins the shared action clock;
  clock synchronization for encounters that meet, merge or gain late arrivals.
- Which attack coordinate anchors a ranged pin (attacker, target or another point),
  create-versus-refresh identity, vertical distance and coordinate-free ROOM scope.
- Exact tactical factor/basis, boundary crossing, and stamina accounting during
  throttled movement; the existing world-clock/geographic coupling question remains.
- Snapshot state versus live reach/visibility/position revalidation, and the stated
  immediate FLEE exemption/penalties versus otherwise next-phase consequences.
- Exemption lifetime, re-entry/re-engagement and recovery completion boundaries:
  new incoming attacks do not alone revoke exemption, yet recapture permits another
  FLEE. The precise transition is not specified by that example.
- Disconnect/restart/offline recovery and pin persistence; confirmation expiry,
  queue revalidation/order, preparation limits and restoring normal underlying values.
- Concealment search, stationary HIDE, detection and cancellation algorithms.

These are implementation questions, not authorization to change runtime behavior.

## SEARCH, command chains and recorded routes (2026-10-02)

**Approved design direction for further refinement; not implemented.** This
section owns navigation/search behavior. Syntax below is proposed, not a claim
that these commands currently work. No new schema, pathfinding or runtime is
authorized by this documentation.

### Current implementation and precedence

The inspected Crown-Call implementation (`1188b58`) already provides continuous
travel, finite directional distances in feet, APPROACH, ordinary observations,
STOP and safe disconnect handling. These use shared server movement/navigation
and geography checks; APPROACH is not general obstacle-routing/pathfinding.
Admin MARK/MARKS/UNMARK/JUMP MARK persists per-character point bookmarks (XYZ or
exact ROOM identity) and uses validated admin placement. It is not a learned
route, player GPS, cartography or route recording. See [current commands](commands.html).

No spiral SEARCH, general `$` command queue, learned-route recording, geometric
route persistence/rejoining or route playback is implemented. Existing search
helpers for admin catalogs/FIND are not the proposed player SEARCH mechanic.
Hidden-person/object detection, tracking and other future perception systems
remain separately governed; listing them below does not claim they already run.

This moving SEARCH supersedes the older Q82 requirement to stand still. Q74's
suggestion of a substantial active-search advantage must not be interpreted as
an automatic perception bonus from this command or its spacing. Preserve those
older passages as historical context; normal perception/concealment owns notice.
Existing TRACK design is distinct and is not replaced by geometric route playback.

### SEARCH: movement determines where, perception determines what

`SEARCH <size> [spacing]` systematically moves through an approximately centered
square around the starting location. Both arguments are **feet**; default spacing
is **5 feet**, measured between passes/loops, not a SENSE/perception bonus.

| Example | Intended area | Spacing |
| --- | --- | --- |
| `SEARCH 50` | 50 × 50 ft | 5 ft |
| `SEARCH 50 2` | 50 × 50 ft | 2 ft |
| `SEARCH 1000 10` | 1,000 × 1,000 ft | 10 ft |

Use an **expanding square spiral starting at the character's current position**,
not a lawnmower grid approached from a distant corner. A conceptual 5-foot pattern
is `E 5 → N 5 → W 10 → S 10 → E 15 → N 15 → W 20 → S 20 → E 25 …`.
Nearby ground is searched first, coverage expands deterministically and searching
begins immediately. Exact generation, orientation and clipping to the approximate
square are later implementation details; this example does not lock an algorithm.

SEARCH uses ordinary movement/navigation. Terrain, slope, water, obstacles,
stamina, speed, observations, LOOK and interruptions apply. Other characters can
observe the searcher physically traveling. Applicable hidden-object/person/MOB
perception, environmental discovery and future tracking operate normally as the
character passes close enough. No teleport, independent simulated travel or
radius-wide reveal is permitted. **STOP cancels the search movement.**

Passing within five feet of something hidden is an opportunity to perceive it,
not a guarantee. SEARCH chooses **where the character looks**; normal perception
chooses **what the character notices**. Smaller spacing costs substantially more
walking without guaranteeing detection. A 1,000-foot square at ten-foot spacing
requires substantial physical travel and time; do not abstract that cost away or
promise an exact distance before the boundary algorithm is chosen.

### Temporary command chains

Proposed delimiter: **`$`**, with optional surrounding whitespace. For example,
`N 100 $ E 50 $ N 200` travels north 100 feet, then east 50, then north 200.
Each command starts only when the preceding command **completes**, not when it
merely acknowledges starting. The concept supports chaining ordinary commands;
the supported command set and completion contract still need definition.
SEARCH may generate an equivalent movement chain or use a cleaner shared segment
representation; it must not create a second movement engine.

Prefer parsing/validating the entire manually entered chain before starting.
`N 100 $ E 50 $ BANANA 20 $ S 10` should reject command 3 and execute nothing,
rather than travel 150 feet before detecting malformed input. This is preflight
syntax/eligibility validation where practical, not a promise that future terrain,
targets or other changing conditions cannot invalidate a later step. Normal
server validation must still apply at execution.

**STOP stops current travel and discards the remaining temporary chain.** Chains
are not persistent character knowledge. Do not use X for travel cancellation:
[X cancels a queued combat action](design.html#rapid-combat-clock-current-intended-design).
Literal delimiter escaping, asynchronous failures, indefinite movement commands,
queue replacement and limits remain unspecified rather than silently invented.

### Recorded routes: character knowledge of actual travel

A route is a **persistent, character-specific geometric path actually traveled
and learned**, not a text macro. Store meaningful waypoints/segments from START
to END rather than literal commands or microscopic samples. This supports forward
and reverse travel, simplification, interruption, rejoining and future map use.
Another character does not automatically learn it. It remains distinct from
admin marks, which identify destinations rather than learned paths.

No arbitrary mileage limit is required: a route may span hundreds of miles;
long straight sections can be compact. If necessary later, a generous segment/
waypoint bound is preferable to a mileage cap. **No numerical limit is selected.**

Proposed command concepts (exact syntax may be refined):

| Command | Purpose |
| --- | --- |
| `ROUTES` | List this character's known routes |
| `RECORD ROUTE <name>` | Begin recording actual travel |
| `RECORD STOP` | End recording; finalize/pause distinction remains to be settled |
| `RECORD ROUTE <name> CONTINUE` | Resume an incomplete/paused recording |
| `RECORD ROUTE <name> APPEND` | Deliberately extend a completed route |
| `ROUTE <name>` | Follow START toward END |
| `ROUTE <name> REVERSE` | Follow END toward START |
| `ROUTE CONTINUE` | Resume previously active route travel |
| `DELETE ROUTE <name>` | Delete a recorded route |

### Following, pausing and rejoining

Forward or reverse travel need not begin at the exact endpoint. From reasonably
near the route, approach an appropriate starting/rejoin point by **real movement**,
then follow the known geometry in the chosen direction. This is not magical GPS
across vast unknown territory. The practical rejoin-distance limit is **unsettled**;
no pathfinding capability is implied beyond existing or separately approved
navigation authority. Normal hazards, obstacles and movement restrictions apply.

During route playback, **STOP pauses physical movement and preserves progress**:
route identity, forward/reverse direction, progress/segment position and paused
state. It does not erase the learned route. ROUTE CONTINUE attempts to rejoin an
appropriate nearby point on the **remaining, uncompleted portion**, then proceeds
toward the original destination. It need not backtrack to the exact interruption
coordinate if a sensible forward rejoin point exists. Selection/rejoin algorithms
remain for implementation, particularly for loops or crossing segments.

Example: halfway through a route to a house 100 miles away, an angry moose forces
the character to FLEE off the path. Route geometry, direction and progress survive.
After escaping and orienting, ROUTE CONTINUE attempts a sensible rejoin ahead on
the remaining route. The character is not locked to rails. If the player instead
chooses to go home, stored geometry supports reverse travel; syntax for reversing
an active/paused route is not locked here.

Disconnect/logout **stops physical movement**, persists route progress and leaves
playback paused on reconnect. There is no offline route travel or automatic
login resumption; continuation is deliberate. Existing water disconnect behavior
still has its separate contract (persisted FLOATING, no offline drift); this
proposal does not silently rewrite it.

### Recording across sessions and extending routes

RECORD ROUTE begins capturing actual traveled geometry; the character then
travels normally. For example, record `tavern`, travel out/east/north 75 feet and
issue RECORD STOP. The interior/exterior example does not yet specify how ROOM
transitions are encoded alongside geographic segments.

Incomplete recordings survive disconnect/logout: physical movement stops and
recording pauses, preserving the partial route for another session. A long route
need not be recorded in one sitting. `RECORD ROUTE north_coast CONTINUE` resumes
an unfinished recording **from its recorded endpoint**. If elsewhere, return/
rejoin that endpoint appropriately first. Never silently connect the old endpoint
to an unrelated current position: that would fabricate unrecorded travel.
Endpoint-navigation conveniences are deferred.

`RECORD ROUTE north_coast APPEND` deliberately reopens a completed route at its
existing endpoint and extends it. CONTINUE resumes incomplete work; APPEND extends
completed work. Neither authorizes an invented connecting segment. PREPEND and
elaborate editing are not required for v1.

### Distinct interruption contracts and open boundaries

| System | Meaning | STOP |
| --- | --- | --- |
| Command chain | Temporary entered sequence | Cancel travel and remaining queue |
| SEARCH | Generated expanding spiral; ordinary perception | Cancel search travel |
| Recorded route playback | Persistent knowledge and traversal progress | Pause, retain route/progress |

Before implementation, settle RECORD STOP finalization versus pause (the handoff
uses both descriptions), route naming/replacement/deletion during use, restart
state, recording interruption/resumption details, geometry simplification tolerance,
ROOM/link transitions and non-traveled relocations such as admin JUMP. Never record
teleports or gaps as physically traveled connecting segments. Search input bounds,
pace selection, interruption/blocked-segment behavior, chain command eligibility
and nearby rejoin criteria also remain open. No database schema, general scheduler,
pathfinding algorithm or new numerical limits are prescribed by this design.

## Unified perception: SCAN, EXPOSURE and stealth (2026-10-02)

**Approved conceptual design, not implemented.** This section owns the common
perception model; no formula, schema, modifiers or new commands are implemented
by this documentation. It complements [SEARCH/navigation](design.html#search-command-chains-and-recorded-routes-2026-10-02)
and the [moving-HIDE transition](design.html#hide-while-moving-and-sneak).

### Shared roles and implementation boundary

| Concept | Role in the shared framework |
| --- | --- |
| SENSE | Observer capability |
| EXPOSURE | Situational target perceptibility; **not another CAP** |
| LOOK | Normal broad-area perception |
| SCAN | Deliberate directional attention, with peripheral cost |
| SEARCH | Actual systematic travel for close-range coverage |
| HIDE | Find/use real concealment to reduce exposure |
| SNEAK | Deliberate slower movement intended to keep exposure low |
| Posture | A contributor to exposure |
| Movement | Affects both target exposure and observer effectiveness |
| Terrain/cover/vegetation | Constrain visibility and provide actual concealment |
| Distance | Affects whether a particular target can be detected |

Use one coherent framework for environmental observation, hunting, scouting,
PvP/ambush/pursuit, stealth, MOB perception, tracking and eventual camouflage and
weather/night visibility. Do not create incompatible magical systems per command.

Current implementation has bounded LOOK/Cormac observations, supported local
object/MOB presentation and server target selection. Regional HOG discovery
contains visibility/concealment inputs but is **not proof of player visibility**.
Existing local perception is not the proposed observer-specific exposure model
or a claim of certified distant LOS. Directional SCAN state/concentration,
EXPOSURE, stealth/posture/movement modifiers, progressive identification and
clothing camouflage are not implemented. Existing water STAND is not evidence
of a general standing/sitting/prone perception system.

Earlier command-reference suggestions that SCAN might merge into LOOK/SEARCH
are superseded by its distinct directional-attention role, sharing perception
authority. Older opposed THIEV-versus-SENSE hiding material remains context;
the new model does not settle THIEV's exact contribution or a replacement roll.
The old “hidden” terminology must never imply universal invisibility. Older
self-recognition of one's own hidden objects does not grant sight through cover
or at arbitrary distance; its precise perception/knowledge interaction needs
reconciliation before implementation.

### Directional SCAN and attention cost

Proposed `SCAN NORTH`, `SCAN NORTHEAST`, etc. concentrate attention in a cone
centered on the heading; eventual `SCAN 17` or `SCAN 243` should be possible.
Compass directions are convenient aliases, not a reason to limit heading support.
This replaces old SoI's looking several ROOMs ahead with continuous geography.

Cone width is **not locked**. A discussed **45-degree total** cone centered north
spans **337.5° through 22.5°**. The earlier 335°–45° example spans 70°, not 45°.
These are geometry clarifications, not a selected final width.

SCAN may run during movement without stopping it. Travel north and scan northeast
are independent headings. This supports hunting, scouting, pursuit, watching a
flank, landmarks, animal observation and guarding while traveling.

Attention is redistributed, not freely added to normal perception everywhere.
Within the cone, useful distance, subtle movement detection, partial-concealment
recognition and detail may improve. Outside it, subtle/distant/hidden things and
objects near one's feet are easier to miss. Peripheral awareness is reduced,
**not blindness**: nearby screams, gunshots, physical contact, a very close charging
animal and other conspicuous stimuli can still be noticed. The cone governs
attention, not the existence of other senses.

SCAN is concerted effort. Sustained attention toward the same direction may
improve subtle/distant detection up to a reasonable cap. Rapidly cycling eight
directions must not grant an instantaneous full-strength 360° sweep. Exact
concentration times, reset/decay rules, caps, bonuses and peripheral penalties
remain unspecified. Example acknowledgment: “You turn your attention northward.”

### Visibility, distance and progressive information

Attention is neither telescopic nor supernatural. Use authoritative geography:
open prairie may offer long sightlines; dense forest, crests, buildings and walls
can block them; valleys may extend them directionally. Future fog/night/weather
can reduce effectiveness. SCAN must not invent visibility through obstacles.

Detection is not identification. Near/mid/far/very-far are illustrative concepts,
not locked bands: nearby details, recognizable figures/animals, distant movement
or silhouettes, and only conspicuous faraway features may be possible. Smoke and
large structures may be detected without precise identity. A sequence might be:

- “Something moves among the trees to the north.”
- “A lone figure appears to be moving south through the woodland.”
- “A man carrying a bow is approaching from the north.”
- Identification, only if circumstances permit.

No detection automatically grants exact identity, equipment, coordinates or all
details. Low-information signs such as something out of place may precede even
recognition of movement. Exact stages and wording remain open. A low-exposure
target at 20 feet is generally easier to notice than the same target at 500 feet.

### Dynamic EXPOSURE and observer-specific detection

EXPOSURE describes how easy a PC, MOB or object is to notice **under current
circumstances**. It is a derived situational concept, not a fixed character stat
or a new CAP. Observer SENSE, attention and circumstances interact with target
exposure/concealment; no arithmetic or roll direction is selected.

Potential contributors include movement, size, posture, vegetation, terrain,
physical cover, concealment, HIDE/SNEAK, distance, light/weather and future
clothing/equipment or noise. Their allocation and combination remain undecided;
a list of inputs is not permission to double-count them on both sides.

Illustratively, standing in open prairie is conspicuous; sitting lowers profile;
prone lowers it further; tall grass plus prone and deliberate concealment can
lower it much more. “High/low/very low” are examples, not fixed categories.

**Detection belongs to the observer relationship.** If Thomas notices concealed
Bob, Bob does not globally become unhidden: his physical exposure is unchanged.
Thomas acquired perceptual knowledge; another observer may still see nothing.
Different SENSE, direction, distance and observation circumstances matter.
Knowledge retention/reacquisition rules remain to be designed.

### HIDE, SNEAK and posture

HIDE uses actual local cover to lower exposure; it never sets a universal
`hidden = true` invisibility rule. Dense woodland, brush and fallen timber may
provide cover; short-grass prairie may provide little or none. Better SENSE,
proximity, concentration or favorable circumstances may still reveal a hidden
target. Do not conjure concealment absent from the world.

Retain moving HIDE: continue traveling while seeking suitable concealment, then
use it, stop and settle with exposure reduced by actual circumstances. SNEAK
moves slowly/deliberately to lower signature, without invisibility. RUN/SPRINT
creates distance conspicuously; WALK has normal signature. Escape can progress
FLEE → distance → break LOS → change heading → SNEAK → HIDE → settle.

Relevant posture concepts include **standing, sitting and prone**. Smaller
profile generally helps, especially with vegetation/terrain; prone in bare open
ground may remain obvious at short range. Posture alone is not magical stealth.

**HIDE does not inherently penalize SENSE or SCAN.** Target perceptibility and
outward attention are separate. Concealed, motionless observers can be excellent
watchers. Actual cover may still obstruct their sight; no generic hiding penalty
or automatic sitting/prone numerical bonus is selected.

### Movement cuts both ways

For targets, sprinting generally produces very high movement signature, running
high, walking moderate, sneaking reduced and stillness no **movement-generated**
exposure. Stillness does not remove all other exposure. This applies to MOBs as
well as PCs: a still deer in brush, wolf in grass, moose crossing prairie and
rabbit by a log should not all be equally noticeable at the same distance.

For observers, sprinting substantially impairs careful observation, running
impairs it, walking somewhat impairs it and deliberate sneaking may impair it
less. Stillness favors sustained observation whether standing, sitting or prone.
These relationships are qualitative, not numerical modifiers or guaranteed ranks
regardless of terrain. Movement makes a target easier to notice while making
that moving observer worse at noticing subtle things.

A concealed hunter under a fallen tree can remain low-exposure while scanning
north. A motionless deer 400 feet away in brush may escape notice; movement may
produce “A flicker of movement catches your attention among the trees to the
north.” The distance is illustrative, not guaranteed detection or forest LOS.
Patience and stillness matter.

Two concealed observers may scan for one another, neither invisible nor assured
of success. Shifting position, crossing an opening or moving equipment can expose
a cue, initially only “Something moved near the base of a pine to the northwest.”
This should emerge from shared perception, **not a separate sniper mode/system**.

### SEARCH tradeoffs and future extensions

SEARCH supplies close-range physical coverage; SCAN concentrates directional,
often distant attention. They may operate simultaneously: do not automatically
ban SCAN NORTH during a search spiral. Attention spent north can reduce notice
nearby or around one's feet. Normal perception determines the tradeoff; SEARCH
still grants no reveal radius or spacing bonus.

Future clothing/equipment effects depend on context: earth tones in woodland,
snow camouflage in snow versus summer prairie, bright colors, reflective metal
and noisy/bulky gear can affect exposure/sensory signature differently. Do not
implement them now or prescribe universal “hunter clothing +3 HIDE” bonuses.

Future admin RAW diagnostics might show qualitative exposure and contributors
(posture, movement, vegetation, active concealment). Format/categories are not
locked. Players should experience prose, detection and environmental feedback,
not “Exposure: 17.4%” or routine numerical diagnostics.

### Deliberately unresolved mechanics

No numeric SENSE/EXPOSURE formula, cone width, distance bands, concentration
schedule, movement/posture modifier, HIDE/SNEAK adjustment or clothing bonus is
approved. Later design/testing must settle representation, observer knowledge,
visibility/detection/identification boundaries, attention persistence/cancellation,
heading-change behavior, lighting/weather and sensory channels. Reuse existing
perception/geography authorities without treating diagnostic geography as sight.

## Bounded Rabbit MOB wounds and corpse (2026-09-30)

CCMUD calls NPCs **MOBs (mobiles)**. The original THROW/REMOVE hit test now uses
actual persisted wounds. Thomas explicitly adopted weapon severity modifiers
(Hunting Knife +0), the existing unarmored +1 adjustment, and a persisted **+2 MOB
severity modifier** for the rabbit after weapon/armor. Equal wounds combine under
W8 and C5 penalizes wounded dodge. There are no rabbit hit points or DEEP death rule.
A rabbit dies immediately on MORT (including combined wounds), an explicit bounded
animal rule; ordinary character MORT/bleed-out rules are unchanged. Death atomically
ends the active MOB, creates a generic persistent corpse with current identifying
details, and reparents all embedded items without changing their IDs. Existing
LOOK, APPROACH, GET, DROP, hands and REMOVE handle the corpse and knife.
See [the exact contract](objects.html#16-throw-wounds-death-and-corpse-2026-09-30).
No bleeding simulation, AI, encounter scheduler, butchering or food is added.

## Bounded rabbit corpse SKIN (2026-09-30)

`SKIN CORPSE` requires an accessible rabbit corpse (held or on the ground) and a
cutting tool in either hand. The Hunting Knife qualifies and remains in that hand.
Remove embedded items first. Atomically consume the corpse and create a rabbit
carcass, raw rabbit pelt and rabbit guts on the ground at the character's feet.
No automatic hand juggling, Craft roll, work timer or cooking is introduced.
Retries and concurrent attempts cannot duplicate outputs. Normal perception,
reach, ground/ROOM placement, LOOK and object handling apply. The cleaned carcass
is reserved for later whole roasting. See [the exact contract](objects.html#17-rabbit-corpse-skin-2026-09-30).

## Craft Engine v1 and web Builder (2026-09-30)

A CRAFT is data executed by one server engine. GATHER FIREWOOD and MAKE SPIT are
persisted definitions with exact command aliases, `TREES & !WATER`, ground outputs
and 300 real seconds of Work Timer cost. MAKE SPIT now requires a held cutting tool;
Hunting Knife qualifies. The old command-specific material handler is removed.
Definitions also support physical prototype/quantity inputs, held capability
requirements and multiple output rows, with immediate atomic processing.

Sector predicates support `&`, `|`, `!` and parentheses over authoritative HOG/ROOM
flags. Unknown facts cannot grant permission through negation. Work Timer and
physical-object rules remain authoritative. Persistent request receipts protect
retries across restart; new requests remain new charged work. SKIN retains its
existing bounded implementation for now, with no new corpse-processing semantics.

Builder/admin accounts author definitions at `/builder` through forms/dropdowns.
The browser is an authoring client, not game authority. Validated, permissioned
server operations own references, quantities, expressions, alias conflicts and
revision-safe writes. They form the future Object/MOB Builder and CCAI boundary;
CCAI never receives unrestricted shell/database access. No full Object/MOB builders,
script language, construction, fire/cooking, quality, discovery or progression are
introduced. See [the precise v1 contract](objects.html#19-craft-engine-v1-and-web-builder-2026-09-30).

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
| Combat clock, pins and FLEE | Current section above; C05/C06 preserved as historical alternatives where conflicting |
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


## Future Builder approval and PROD publishing lifecycle (2026-10-01)

**Approved future design; not yet implemented.** Authored Builder content such as OBJECTs,
ROOM/DWELLING templates, MOBs and CRAFTs should eventually use a controlled promotion
lifecycle between DEV and PROD:

**DRAFT → TESTABLE → APPROVED → PUBLISHED**

Builders may create, edit and test content on DEV. When ready, they submit a specific
revision for approval. Approval applies to that exact immutable revision, not merely to
the prototype ID. A higher-authority builder/admin reviews and approves it before it may
be published to PROD.

Once a revision is approved/published, it is frozen. Further editing creates a new
working revision which returns to DRAFT/TESTABLE status and must be reviewed and approved
again before replacing the currently published PROD revision. PROD continues using the
previous approved revision until the replacement is explicitly published.

This policy is intended to support future role separation such as Builder
(create/edit/test/submit), Senior Builder/Admin (approve/reject), and Administrator
(publish/manage exceptional cases). Exact role names and permissions remain future work.

The same revision/promotion model should apply consistently across authored content
types rather than creating separate approval systems for Objects, ROOM/DWELLING
templates, MOBs and Crafts. It should integrate with the future selective DEV→PROD
content-publishing/versioning workflow. No approval UI, PROD publishing pipeline, role
system or deployment behavior is implemented by this decision.


## Future persistent character transcript logging (2026-10-01)

**Approved future design; not yet implemented.** CCMUD should eventually maintain a
persistent server-side text transcript for each character. The purpose is both player
history and long-term debugging: when a difficult bug is reported, an authorized
administrator can inspect the actual commands and game output surrounding the event
rather than relying only on recollection or browser state.

Logging belongs to the server/game transcript path, not solely to the web client. This
preserves the client-agnostic architecture so supported web, traditional MUD and future
clients contribute to the same character history. Record actual game transcript content
such as player commands, server output, LOOK/travel/combat messages, SAY and errors; do
not log browser-only controls or UI chrome.

Logs should append across login sessions and be retained with a bounded rotation/archive
policy rather than one permanently growing file. A future web/admin convenience may
allow an authorized user to download/export a character transcript as plain text for
review or bug diagnosis. Exact retention periods, rotation size/cadence, privacy/access
permissions, redaction needs and download UI remain future decisions.

This feature must not make transcript files authoritative game state. Persistence and
gameplay remain database/server-model responsibilities; transcript logging is diagnostic
history and may fail without changing game outcomes.


## Shared MUD sessions and presentation v1 (2026-10-03)

The server remains authoritative for both first-class web and traditional MUD clients.
A shared session loop owns commands, continuous travel/ROOM ticks, presence, ordering and
STOP cleanup. Each character has one active owner across transports; second connections
are rejected. Ownership is released only after disconnect STOP finishes. Different owned
characters retain the existing account policy. There is no offline travel or takeover.

The optional Telnet listener runs in the same application process/lifecycle on a separate
port; one application worker is required. It defaults off. Numeric loopback plaintext is
for local development; remote binds require direct TLS with a supplied certificate/key.
Existing HTTP/HTTPS ports and web registration are unchanged. Terminal users log in with
existing credentials and select an owned character. Character creation and legacy-key
claiming remain web operations. Connection-local login tokens are revoked at terminal
exit; existing browser sessions and join-time authorization behavior are preserved.

Traditional connections receive asynchronous output without entering new commands. A
bounded queue and single writer isolate slow clients; failure wakes owner cleanup. Shared
presentation supplies travel fallback wording previously available only in JavaScript.
Semantic text runs accompany existing structured event fields: titles cyan, objects green,
PCs/MOBs magenta, hazards/errors red, prose default. Some specialized action prose remains
neutral. Adapters render safe CSS/text nodes, ANSI, or plain text; no renderer decides
mechanics, visibility, passability, ownership or administrative access.

Approved initial authored fields include object/appearance and ROOM descriptions and
ROOM departure text. They accept `#0`/`#8` reset, `#1`-`#7` red/green/yellow/blue/magenta/
cyan/white, `#9` bright red, and `#A`-`#F` bright green/yellow/blue/magenta/cyan/white.
Letter codes are case-insensitive and `##` is literal hash; unknown sequences remain
literal. Reset means client default and each field/message is scoped. SAY stays literal.
Raw controls are rejected on supported authoring paths and sanitized from legacy output.
Storage limits remain source-character limits; terminal wrapping uses Unicode display
cells and grapheme clusters. Browser wrapping remains responsive, and copy is plain text.
No new player-writing system, migration or global gameplay setting is introduced.

Terminal `ANSI ON`/`ANSI OFF` affects only that connection; default is plain. The adapter
supports bounded ECHO, SGA, NAWS, TTYPE, BINARY and CHARSET, with UTF-8/ASCII negotiation.
It does not advertise GMCP or compression. Structured events preserve a future GMCP seam;
protocol packages, maps and client-specific GUIs are deferred. Security/protocol limits
and deployment/TLS configuration stay outside immutable gameplay Settings.

The implementation README owns exact configuration and limit values. No firewall,
systemd, nginx or live deployment change accompanies source publication. Certificate
provisioning/renewal, hostname/port selection, remote reachability and acceptance in actual
Mudlet/MUSHclient/TinTin++ versions remain deployment verification. Existing game mechanics
and terrain safety are unchanged. The existing web client remains a supported peer.


### State snapshots versus transcript events

Unsolicited `travel_status` and `hud` messages are state snapshots, not terminal
transcript prose. Web clients continue receiving every snapshot for their existing HUD.
The terminal adapter does not print them, even when coordinates/stamina change during
travel: value-only deduplication would still produce a scrolling HUD. The shared session
marks travel-status messages returned by explicit STATUS with `requested: true`, without
changing their type/state fields or mutating engine results. Terminals render each such
reply, including repeated identical requests. Welcome odometers and explicit ODOMETER/
TRIP output remain; STOP acknowledgments, asynchronous observations and repeated hazards
are never removed by a global text deduplicator. Ticking, terminal width/coordinates and
ANSI/plain rendering are unchanged. GMCP remains deferred.


### Automatic entry after terrain preparation (2026-10-03)

A verified character whose terrain is still generating stays connected and authenticated.
The shared session sends one `join_wait` notice, reserves exclusive entry ownership, and
retries readiness once per second without blocking other sessions. Pending characters
are absent from online/presence lists and cannot execute or queue gameplay commands.
QUIT or disconnect releases the reservation. Once certification passes, the same session
enters automatically, clears stale travel, and receives the usual welcome and LOOK.
Web clients show the notice before joining; terminal clients receive readable prose.

Only a typed terrain-cache pending result is retried. Generator failure, unsafe geometry,
invalid ownership and unaudited structures still fail closed. Cold certified structure
attachments may wait during entry; ordinary structure interactions retain their existing
unavailable behavior. There is no entry wait deadline beyond the existing connection
capacity limits; QUIT remains available. No new listener settings, migration, public
ports, GMCP, terrain rules or gameplay tuning values are introduced.


### Traditional-client Alpha prompt and visibility polish (2026-10-03)

Web and terminal sessions default to COMPACT. `PROMPT COMPACT`, `PROMPT TEXT` and `PROMPT OFF`
change only the current connection; `PROMPT` reports the mode and usage. Prompt commands are handled by the shared session with no preference table. ANSI ON/OFF
remains terminal-specific. Web clients keep their existing HUD and add the same prompt
beside the command input. On narrow screens, TEXT mode can wrap above the input.

COMPACT is `<||||||/******>` with six fixed positions on each side; depleted positions
become spaces on the right. Stamina uses `ceil(6 * clamp(current / configured_max, 0, 1))`:
zero is empty, any positive amount has at least one pipe, and exactly full has six.
Health sums binary wound values FLSH=1, LITE=2, DEEP=4, SEVR=8, GRAV=16, MORT=32.
The stars are exactly: 0 total → 6; 1–5 → 5; 6–11 → 4; 12–17 → 3; 18–23 → 2;
24–31 → 1; 32 or greater → 0. Each bar's leftmost two positions are red, middle two
yellow, rightmost two green. These are safe presentation spans; game logic contains
no ANSI. ANSI OFF preserves the same symbols and spaces.

TEXT is `<Stamina 55% Health 43%>` for 22/40 stamina and total wounds 18.
Stamina percentage is `floor(100 * clamp(current / configured_max, 0, 1))`.
Health percentage is `floor(100 * max(0, 32 - total_wound_value) / 32)`.
This is a presentation scale, not a new death rule. The prompt is a summary; HEALTH
lists actual stored wound severity names. Existing INSPECT targets objects/MOBs;
INSPECT SELF is not implemented. STATUS remains the existing movement/status view.

Repository inspection found wounds persisted only for MOBs. Thomas approved minimal
PC storage: migration `20261003_01` adds an empty-by-default JSON wound list while
preserving characters. It adds no damage producer, combat scheduling, healing or death
mechanics. Existing PCs are unwounded. Prompt and HEALTH read this state; there is no
new player wound-editing command. Wound combination rules remain unchanged.

The serialized terminal output writer appends one unterminated prompt after a drained
batch of visible output, using current server state. Silent HUD/state ticks never dirty
or refresh the prompt. Async speech, observations and warnings start below an existing
prompt and finish with a new one; repeated meaningful events are retained. Telnet GA
is used where negotiation permits it. The server does not attempt to redraw characters
being edited locally by a client. Mudlet's separate input pane remains the intended
first acceptance target. OFF suppresses prompts only, not events or STATUS replies.

The outdoor HOG LOOK branch omitted online PCs while including objects and MOBs.
Character records have a name but no sdesc/ldesc/full-description fields; absent
character descriptions did not cause the omission. LOOK now includes online PCs using
the shared ready-terrain sight, range, cover and structure-barrier checks and character
semantic color. Different ROOMs, blocked sight and unavailable geometry remain hidden.
Both transports consume the same authoritative event; no transport visibility override
or new character authoring subsystem is involved.

Warning/hazard prose remains red, while the trailing `You stop.` uses default style.
Authored object descriptions still parse the historical #0–#F palette, lowercase letters,
#8 reset and ## literal hash into safe spans. ANSI/plain/WebSocket integration uses a
stored object description containing `#1DANGER#0 This is a #2green object#0.`; the web
renderer uses textContent and allowlisted CSS classes. SAY continues to treat # markup
literally. No GMCP, firewall, listener or deployment changes accompany this pass.


### Web prompt parity (2026-10-03)

The web prompt uses the same server-side COMPACT/TEXT/OFF mode handling, stamina and
wound calculations, positional colors and connection-local default as Telnet. WebSocket
messages carry changed prompt metadata alongside existing events; unchanged displays
are deduplicated, without extra transcript events or replacing existing HUD fields.
The browser uses allowlisted CSS spans and textContent, preserving compact blank slots.
It updates only the prompt element, preserving typed input, selection and focus.
Submitted commands echo the then-visible prompt plus literal command text into history;
the live prompt stays beside the command input ready for the next command. OFF hides
it and retains the web's ordinary command echo. Disconnect/re-entry clears stale display;
a new connection starts COMPACT. The compact widget has a percentage-based accessible
label and is not a live-announcement region. Telnet prompt delivery remains event-driven,
with no prompts from silent state ticks. No migration or configuration changes are needed.
