---
title: CCMUD Object Architecture — Working Draft 5
description: A bounded object architecture reconciled with approved gameplay decisions and the existing implementation.
reviewed: 2026-10-01
nav: design
permalink: /CCMud/objects.html
---

# CCMUD Object Architecture

**Working Draft 5 — Hunting Knife Slice 1 and the bounded Rabbit MOB / THROW / REMOVE increment are authorized. Other slices and game deployment remain separate decisions.**

This document condenses the historical object exploration into the smallest persistent object foundation needed for a playable Alpha. It strengthens the existing implementation and defines six bounded slices. It is not a mandate to build every system mentioned in the exploration.

**LOCKED** identifies approved decisions or preserved governing constraints. **PROVISIONAL** identifies recommendations and implementation details still subject to review. **DEFERRED** means outside these slices. **REJECTED** identifies excluded approaches. The latest explicit decisions recorded here supersede conflicting earlier draft policies and historical design alternatives. Architecture approval, implementation authorization, documentation publication, and deployment remain separate steps.

The September 30 Rabbit increments in sections 15-19 supersede earlier stop lines only for their explicit scope. MOB (mobile) is CCMUD terminology for an NPC, including wildlife.

## 1. Authority and evidence

Start future work with private Crown-Call's [WORK_START_HERE.md](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/WORK_START_HERE.md). Its instructions govern orientation and workflow. The [development guide](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/AGENTS.md), [design guide](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/DESIGN.md), and [preserved game design](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMUD_Design.txt) carry existing decisions. The preserved design's consolidation and supersession notes take precedence over its historical question queue.

| Evidence | Reviewed baseline |
| --- | --- |
| Private implementation and historical object exploration | `twpaige/Crown-Call`, `e3491580ab952874f46cf9716a33f1bd0934e6f3` |
| Public knowledge base | `twpaige/ForgottenWords`, `ea2c03d7a93f0458a9c8e4a28bb35fc135e742af` |
| Private pinned references | Seven references verified against that public revision |
| Local regression evidence | 78 tests passed across `test_world.py`, `test_hog_cabin.py`, and `test_cormac.py`; one dependency deprecation warning |
| Live DEV / PROD revisions | Not checked; no deployment or live migration performed |

The review used the exploration's complete numbered topic outline, substantive passages across its foundational and Alpha topics, and its late convergence material, especially points 1981–2080. This is a thematic architecture review, not a line-by-line certification of every speculative example. Relevant accepted game-design sections and implementation paths were read directly. Earlier Project chats supplied no authority.

The editable source is `ForgottenWords/CCMud/CC_Objects.md`. The September 30 instructions authorize Hunting Knife Slice 1 and the bounded Rabbit MOB / THROW / REMOVE / wounds / corpse / SKIN / survival-material interactions in sections 15-19, including commit/push. Other slices and game deployment remain separate decisions. Its explicitly approved decisions govern this design work. The original private `CC_Objects.txt` remains unchanged. Implementation links require private repository access and identify evidence without publishing implementation code.

## 2. Terminology and boundaries

**LOCKED (October 1 refinement):** persistent authored nonliving physical things are OBJECTs. ROOM, DWELLING and future STRUCTURE/VEHICLE behavior specialize OBJECTs; HOG geography remains HOG. The table below describes behavior, not separate fundamental identities. See section 22 for the implemented Pioneer Cabin POC.

| Category | Meaning |
| --- | --- |
| **PC** | Player-controlled character. |
| **MOB (mobile)** | All non-player characters, including humans and wildlife such as rabbits, deer, and horses. |
| **OBJECT** | Persistent authored nonliving physical things, including fixed infrastructure as well as portable possessions. |
| **DWELLING** | Enterable habitation: cabins, houses, pitched tents. |
| **VEHICLE** | Wagons, carriages, sleds, boats, ships. |
| **STRUCTURE** | Fences, walls, gates, bridges, docks, and similar built features. |
| **ROOM** | Fixed OBJECT providing coordinate-free occupancy and authored links. Space remains an exterior-geometry implementation record. |
| **HOG GEOGRAPHY** | Natural geography under HOG authority. |

ENTITY is removed as CCMUD's formal umbrella taxonomy. Existing implementation names such as `NearbyEntity` are evidence about code, not a requirement to retain that taxonomy. A category describes gameplay behavior; it does not require replacing working character, cabin, or object storage.

**TEMPLATE** means the existing `ObjectPrototype` definition. A **capability** is supported behavior/configuration, represented by `ObjectCapability` where appropriate. A **physical parent** determines a possession's current location. **Effective location** is the outdoor placement or ROOM reached by following that parent chain. A portal connects spatial contexts; a door controls access. Their conceptual distinction does not require splitting the current cabin's `Portal` record.

Preserve these governing boundaries:

- CCMUD owns gameplay and commands; the web interface is a client. Preserve client-agnostic text interaction. This does not claim a Telnet listener exists.
- HOG owns geography. Objects overlay it; forests do not require one OBJECT per tree or terrain cell. Extracted resources can become individual OBJECTS without transferring authority over the source landscape.
- Cormac and Eyes present supplied facts. Perception neither creates world truth nor grants movement permission. Preserve useful admin RAW diagnostics.
- Outdoors uses signed 64-bit integer inches: positive X east, positive Y north, Z elevation. Preserve PostgreSQL, deliberate Alembic migrations, travel accounting, and ground-placement authority.
- Adopted Crown's Calling rules remain above object storage. Future CCAI uses restricted, validated operations, not unrestricted database or shell authority.
- Slice 1 and the bounded sections 15-19 interactions are authorized for implementation and publication. Live cleanup and DEV/PROD deployment have not been performed. Later slices need a separate instruction.

## 3. Existing implementation and required changes

**LOCKED:** evolve `WorldObject`, `ObjectPrototype`, and `ObjectCapability`. Do not create a parallel object foundation.

| Existing evidence | Required integration |
| --- | --- |
| `WorldObject` has a UUID, prototype, positive quantity, coordinates or character holder, and optional interior space. | Preserve IDs; strengthen location exclusivity and add minimal handling state. Add containment only for the backpack slice. |
| `Character` stores coordinates, chunks, and `space_id`; runtime `TravelState` and `PositionedCharacter` serve existing movement/session contracts. | Resolve carried objects through this authority. No shared `Position` class was found or needs to be invented first. |
| GET/TAKE, DROP, and I/INVENTORY already dispatch to persistent handlers. Outdoor HOG interactions remain gated. | Document GET as canonical, retain TAKE, add INV, and integrate a bounded supported outdoor path. Existing dispatch is not proof that outdoor GET works. |
| `_get` checks ground candidates and portability, then changes holder and clears placement. Requests serialize per character, not per shared object. | Protect competing GETs across different PCs and validate destination hands atomically. |
| Holder/coordinate exclusivity does not fully exclude held `space_id`; there is no container parent yet. | Enforce the complete current-location invariant in storage and domain operations. |
| Ground queries filter by space and three-dimensional radius; the matcher returns the first match. | ROOM interaction must bypass intra-room distance. Resolve ordinary matches nearest-first, with contextual ordinals (section 21). |
| HOG observation supplies the audited cabin; generic live-object candidates are not integrated. | Add truthful object candidates to observation, with separate perception and interaction checks. |
| A cabin `Space` combines bounds and interior/exterior descriptions; a `Portal` combines endpoints and open state. | Keep these records while adapting interior interactions to ROOM semantics. |
| `GroundPlacement` supports absolute versus ground-relative placement and read-only audits. | Preserve those meanings and the cabin's narrow audited geometry. |
| Travel load currently sums directly held objects. | Add recursive load with containers; putting a knife in a backpack must not hide its weight. |
| Moving travel ticks can persist changed position and odometer state frequently. | Do not rewrite durability as a weaker periodic-checkpoint scheme during object work. |
| Existing APPROACH is an administrative geographic/structure diagnostic operation. | Integrate player `APPROACH <object>` without exposing admin targeting privileges. |

Implementation evidence: [models](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/models.py), [WorldService](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/world.py), [HOG adapter](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/hog_game.py), [server dispatch](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/main.py), and [migrations](https://github.com/twpaige/Crown-Call/tree/e3491580ab952874f46cf9716a33f1bd0934e6f3/migrations/versions). These are reviewed baseline facts, not claims that Draft 5 behavior has been implemented.

## 4. Stable identity and one current location

**LOCKED:** retain stable OBJECT identity and meaningful current state. Do not maintain object history/provenance: no former-owner lists, historical locations, or movement/transfer journals as object features. A current corpse description or fire's current fuel/time accounting is state, not a provenance system.

Ordinary handling preserves the same ID. Names, coordinates, prototype names, parser ordinals, and positions in a result list are not identity. A newly created knife receives a new ID; GET does not create another knife.

Each active OBJECT has exactly one authoritative current location path:

```text
direct outdoors:  Knife → outdoor SPACE + X/Y/Z
direct indoors:   Backpack → DWELLING ROOM
held:             Knife → PC's right hand → PC's current SPACE
contained:        Knife → Backpack → PC → DWELLING ROOM
left indoors:     Knife → Backpack → DWELLING ROOM
```

Direct placement and physical parentage are mutually exclusive. A parented item has no independent authoritative coordinates or ROOM placement. Its hand/wear role describes handling; it does not create a second location. Legal ownership and following behavior are not location parents.

The resolver follows the current parent chain to an outdoor position or ROOM. It must reject missing parents and cycles rather than invent an Origin placement. Space identity comes before outdoor distance. Effective location alone grants neither visibility nor access: the knife inside a closed backpack is not exposed because its coordinates can be derived.

LOOK and ordinary loading never spawn replacements, relocate objects, or repair corrupt state silently. Logout leaves the persistent PC and possessions intact. Deleting a parent cannot silently delete its contents. Inventory and equipment are views of these relationships, not duplicate stores.

Retain the existing character-holder case. Add only the handling state needed by Slice 1, and an object-parent case when Slice 2 needs containment. Exact columns, constraints, and subordinate records are implementation choices. Outdoor `space_id = NULL` may be adapted into an explicit logical outdoor context without creating a universal spatial database first. Persisted chunk coordinates remain derived lookup aids, never a second location authority.

Transformations follow their specific approved rule. The same tent persists through packing and pitching. MOB death and SKIN create the distinct results specified in section 10; stable identity does not mean every material transformation must retain an input's ID.

## 5. Outdoor placement, ROOM interiors, and barriers

**LOCKED outdoors:** coordinate-based location and movement remain authoritative. Preserve explicit ABSOLUTE Z and GROUND placement offsets/requirements; never reinterpret existing authored heights casually. A new dropped knife uses supported ground-relative placement with zero offset. Attachment metadata must agree with its new placement.

**LOCKED indoors:** inside a DWELLING, an accessible OBJECT in the same ROOM can be manipulated without intra-room coordinate movement, APPROACH, furniture collision, or pathfinding. An OBJECT in another ROOM requires moving there first. Room-wide interaction does not bypass a closed container or other applicable access rule.

The DWELLING boundary is real. An outside PC cannot GET an inside object, even through an open door or where coordinate numbers happen to be close. Normal exterior object discovery must not expose interior contents. Existing cross-door muffled speech is separate and remains intact.

The cabin remains `pioneer_cabin`. Its seeded exterior/interior portal coordinates are `(0,1200,0)` and `(0,1260,0)`, with actual HOG ground attachment resolving height. Section 22 now deliberately migrates interior occupancy to ROOM-object IDs with null interior coordinates. The legacy doorway geometry remains exterior access data; interior placement means ROOM membership, not terrain sampling.

Preserve the audited exterior doorway, wall footprint, terrain attachment, and rejection of unsupported configurations. ENTER crosses the real doorway and clears outdoor travel. Inside, reaching accessible contents or the exit does not require coordinate walking. EXIT still performs the authoritative transition to the exterior doorway, respecting door access. Existing interior radius/reach checks must be reconciled with this rule, not preserved as hidden restrictions.

Held possessions follow the PC through ENTER/EXIT. A backpack dropped indoors becomes directly placed in that ROOM; its children remain parented to it. Leaving the ROOM moves the PC, not the dropped backpack. The cabin is now a DWELLING OBJECT; `Space`/`Portal` retain its reviewed exterior geometry and door state. A separate door OBJECT is not required by this POC.

**LOCKED DROP:** outdoors, leave the item at the dropper's feet on the supported surface in the current SPACE, using authoritative position at the transfer boundary. Indoors, leave it in the current ROOM; flavor text may say “at your feet” without creating an intra-room positioning requirement. DROP itself does not stop continuous travel. Unsupported outdoor placement, including unsupported water-object contexts, fails with the item still held.

**LOCKED collision rule:** ordinary PCs, MOBs, OBJECTS, and VEHICLES are not tactical movement obstacles. A knife, pile of small items, chair, horse, crowd, or wagon creates no positioning puzzle, automatic stop, or avoidance route. Do not add a generic OBJECT collision toggle or a numeric small-item cutoff. Real barriers come from DWELLINGS, STRUCTURES, and HOG geography where appropriate: cabin walls and fences can block. The rule abstracts maneuvering around ordinary occupants and items; it does not bypass a wall.

VEHICLES are a distinct future access case. Exposed, reachable contents may eventually be manipulated from outside; do not automatically give them the DWELLING interaction boundary. Nested containers still enforce their own access rules. Vehicle implementation remains deferred.

## 6. GET, APPROACH, perception, and travel

**LOCKED:** GET is the canonical documented command; TAKE remains an alias. Outdoors GET never moves the PC. It requires a currently valid target, interaction range, portability, access, and an available hand. A previously observed target must be revalidated at action time.

**LOCKED player APPROACH:** `APPROACH <object|mob>` moves toward a perceptible OBJECT or MOB and stops at its appropriate interaction range. The outdoor target must already be perceptible and in direct line of sight. Move directly toward it and stop at interaction range. APPROACH does not pathfind around walls, fences, or buildings and does not automatically GET the target. PCs, MOBs, OBJECTS, and VEHICLES do not obstruct this movement; DWELLINGS, STRUCTURES, and HOG geography may block it. A known ID, coordinate, or admin reference is not player perception.

Use current movement and geometry authority for the supported path. Revalidate the target and path during movement; stop safely if the target is lost or the permitted direct path becomes blocked. Do not route around an obstruction or expand this into general navigation. Indoors APPROACH is unnecessary for an accessible object in the same ROOM. A target that moves stops this bounded APPROACH; no chasing is added. Ordinary arrival says "You approach the Hunting Knife and stop." or "You approach the rabbit and stop." Diagnostic geographic wording about represented targets, certification or inches is not player arrival prose.

**LOCKED:** GET issued during continuous travel first stops that travel, then attempts the interaction. It never automatically resumes, whether the object is obtained, out of range, inaccessible, missing, or rejected because hands are full. TAKE follows the same handler. Stopping is an intentional command effect even when the object transfer fails; therefore “failed transfer changes nothing” applies to object/possession state, not to the separately required travel stop. Preserve accepted movement and odometer accounting up to the stop boundary. The normal command loop must not advance the old travel state afterward.

**LOCKED:** visibility and interaction range are separate. Draft 4's fixed 15-foot visibility policy is superseded. The current pickup configuration of 180 inches is implementation evidence about interaction range, not an approved visibility ceiling. A knife may be visible 50 feet away on a road but invisible two feet away in tall grass. Exact object visibility tuning comes later; neither distance alone nor a known coordinate proves perception.

Object observation follows this flow:

```text
authoritative current facts → bounded candidates in the correct SPACE
                            → perception/access checks → player presentation
```

Keep dynamic candidates outside cached geographic prose. Cormac should receive facts through the observation seam, not query the database or generate objects during LOOK. Use nearest-first ordering, with absolute compass sector then namespaced stable ID breaking equal-distance ties. Namespace mixed candidate references because the current formatter deduplicates by ID. Bound dense result sets as well as spatial searches.

Indoors, retain authored ROOM descriptions and present actual accessible objects from that ROOM. Do not invent distance/bearing obstacles for ROOM contents. Outdoors, describe a zero-distance object as “here” rather than giving a fictitious meaningful bearing. A held item is not a second ground candidate; contained items appear through explicit container inspection, not as loose objects.

Resolve multiple matching names explicitly instead of taking the database's first row. Ordinary commands use perceived references; admin stable-ID inspection remains distinct. Existing outdoor interaction queries use three-dimensional distance while travel odometers use their established horizontal metric. Preserve each appropriate meaning; the new ROOM rule explicitly removes intra-room range checks.

## 7. Physical possessions, containers, and access

**LOCKED:** a PC carries things only in the right hand, left hand, configured worn locations, or physical containers being carried/worn. There is no invisible inventory space.

GET selects the right hand if free, otherwise the left, and fails if both are occupied. Never automatically stow, swap, or drop anything. Minimal hands arrive with the first knife; WIELD and WEAR arrive in their later slice. Neither hand gains a mechanical advantage. Confirmation identifies the actual hand.

**INV/INVENTORY lists only what the PC is holding and wearing.** Preserve I as an existing alias if desired. Show hand and worn roles, but do not enumerate container contents. Use `LOOK BACKPACK` or the corresponding accessible container target to inspect contents. An empty result must be distinguished from an unavailable/corrupt query. Existing LOOK dispatch must be extended deliberately because current HOG arguments select description verbosity rather than arbitrary object inspection.

The backpack introduces a single containment-parent relation. PUT replaces a held relation with a container parent; GET from the backpack replaces it with an available-hand relation. The backpack can itself be held, dropped, and later worn. Its contents follow it without copied coordinates.

Preserve approved maximum nesting of four container levels, no cycles, and recursive weight/capacity accounting. The depth limit counts containers, not links through a PC. Count each item's mass once using existing weight/quantity conventions where suitable. Dropping a backpack removes its complete recursive load from the PC once, leaves all children inside, and exposes only the backpack as a loose-object candidate.

Containers need explicit eligibility, capacity, and access behavior; an arbitrary ID cannot become a container merely because a reference can point to it. A simple always-open first backpack is sufficient. OPEN/CLOSE remains normal physical behavior wherever applicable. Nested containers enforce their access rules independently. Preserve the accepted restoration of temporary opening to its found state where an authorized convenience operation uses it; this does not permit automatic inventory rearrangement.

Equipment is current state of the same possessions. Two-handed use occupies both hands. HOLD versus WIELD expresses manner/readiness without creating another object or inventory. **THROW requires a throwable object to be in a hand; held or wielded both qualify.** WEAR starts from holding and uses a valid wear location/layer; REMOVE needs a free hand. Begin with the actual knife/backpack needs rather than a full garment catalog. Remove worn equipment through the supported handling transition before dropping it; do not invent implicit compound actions.

Property-door permissions retain the accepted virtual/identity-based system. OPEN/CLOSE does not imply physical keys. Physical key OBJECTS are not the default design, and advanced locking remains outside this work.

## 8. Durability, shared defaults, diagnostics, and DEV cleanup

**LOCKED durable transfer:** successful GET, DROP, PUT, and equipment transfers commit their authoritative result before success is reported. Protect both source and destination handling state. Competing GETs for one object have exactly one winner; per-PC serialization is insufficient.

Use a short transaction with object/parent locking or equivalent conditional protection. Recheck current location, access, and available hands within the protected operation. Database constraints backstop valid references, location exclusivity, required placement fields, and positive quantities. Domain validation handles reach, eligibility, nesting, and capacity. Concurrent container reparenting must not create a cycle through two individually plausible changes.

A failed object transfer leaves its original possession/location intact. GET's required travel stop is the explicit separate effect described above. If a commit succeeds but the confirmation is lost, retrying must recheck current state and cannot duplicate the object. Restart reconstructs committed IDs, placement, parents, and current state; it does not instantiate templates again. This is ordinary database durability, not a promise against arbitrary storage destruction.

**LOCKED shared defaults:** `ObjectPrototype` remains the template authority and `ObjectCapability` supplies existing supported behavior. Preserve the portable capability. Reviewed definition changes use the existing content workflow; no second live template loader is needed. Description corrections can propagate through shared defaults. Mechanical edits need explicit review of affected state, such as contents exceeding a reduced capacity. Defaults never reset identity, placement, children, or consumption state.

Persist only authoritative current facts: IDs, definition references, direct placement or physical parent, handling role, meaningful quantities/state, and necessary time accounting. Effective location, Nearby ordering, inventory text, and recursive load are derived. Full template versioning, inheritance frameworks, per-instance copies of every default, and provenance stores are unnecessary.

**LOCKED diagnostics:** read-only admin inspection should explain object ID/prototype, current quantity/state, direct placement or parent, hand/wear role, effective outdoor coordinates or ROOM, height interpretation, and the parent chain. Explain access/perception exclusions or transfer failures where useful. Diagnostics must agree with existing RAW geography and character coordinates. Inspection must not move, repair, or delete objects.

**LOCKED legacy DEV policy:** before enabling hands, identify incompatible obsolete held objects in DEV and clean/remove them as a documented DEV cleanup. Do not invent invisible slots, transitional inventory states, or containers to preserve obsolete test possessions. This supersedes Draft 4's requirement to preserve every legacy holding. Audit the actual affected records and keep cleanup narrowly scoped to incompatible DEV data; no production reset follows from this decision. The revision records the future cleanup policy and performs no live audit or deletion. Valid current objects retain identity through normal operation.

## 9. Tents and the separate Alpha milestone

**LOCKED:** a packed tent is an OBJECT; the pitched form is a DWELLING with a ROOM interior. It remains the same particular tent throughout. Preserve stable identity across pitching/packing without requiring every gameplay category to share one table.

Dismantling is allowed only when the interior contains no PCs, MOBs, or loose OBJECTS. An occupied tent or one with a backpack left inside cannot be packed; do not eject occupants, sweep contents into magical storage, or silently delete anything.

Tent pitching remains part of the separately accepted navigation Alpha exercise: travel at least two miles, place a tent, leave sight, and return to that same persistent tent using the intended navigation tools. It adds no seventh object-system slice and no general construction system. Storage details for the tent's two forms belong to that milestone.

## 10. Rabbit, corpses, Craft, and the first meal

**LOCKED death model:** MOB death ends the active MOB and loads/creates a generic CORPSE OBJECT populated with relevant current details: race/species, weight, short description, and appropriate identification for named characters. The corpse is a resulting OBJECT, not a living MOB retained under a new label. Populating its description does not require storing former owners, location history, or a provenance graph.

**LOCKED processing:** SKIN consumes that CORPSE and creates separate RAW PELT/SKIN, CARCASS, and GUTS OBJECTS. The consumed corpse cannot be skinned again. Consume/create results atomically so retries or simultaneous actions cannot duplicate yield. These outputs have their own identities and valid physical destinations; no output is placed in invisible inventory.

The CARCASS can be cooked whole through Craft or optionally butchered through Craft. BUTCHER is not mandatory for the rabbit meal. RAW PELT may later be tanned through a simple Craft; this does not authorize a broad crafting catalog.

Preserve the accepted survival entry: normal starting equipment does not grant a free hunting knife. Primitive stone/cutting-edge acquisition and a suitable weapon support the ordinary route; a deliberately placed knife is the first architecture proof. FLUSH creates the adopted rabbit encounter in suitable habitat. Combat supplies attacks, wounds, turns, and actual death. A fixture creating a corpse can test persistence but cannot stand in for working player hunting.

The accepted meal uses a simple spit and whole-rabbit roasting. Cooked food is a physical object with remaining edible quantity; progressive EAT reduces it and leaves appropriate remains. Do not manufacture serving objects merely to represent bites. Food quantity, resource yield, and fire fuel must not replenish on reload or become negative.

**LOCKED Craft:** after validation, consume inputs and create/transform results immediately while adding Work Timer debt atomically. Work Timer is real-time per-character labor debt, with the accepted below-48-hours entry threshold. It is not delayed delivery of results, and travel acceleration does not accelerate it.

Fire's continuing burn is a separate process. Store sufficient fuel/time facts to recover correctly after absence or restart, and prevent repeated consumption on retries. No global per-second object tick, weather simulation, or fire spread is required. Before Slice 6, select the burn clock explicitly: the accepted future world-time rate is 1.75× real time, but this does not establish that the clock is implemented. Do not borrow travel acceleration by accident.

The object slices do not replace combat, Craft, or perception. Accepted rabbit lifetime, bleeding, concealment, and trail behavior remain owning-system requirements where applicable. Identify missing prerequisites honestly rather than claiming a test-only shortcut completes the hunt.

## 11. Six slices and their acceptance proofs

**The six-slice plan remains; authorized implementation is Slice 1 plus the explicit bounded sections 15-19 exceptions.** Each slice closes the relevant integrity and restart tests before expanding scope. Slice 3 remains even if its final change is small.

| Slice | Bounded deliverable | Acceptance proof |
| --- | --- | --- |
| **1. Hunting Knife** | Existing foundation, reviewed outdoor placement, object perception, player APPROACH, canonical GET/TAKE, minimum hands, INV/INVENTORY, DROP, diagnostics. | Same knife through ground → hand → travel → ground → restart; GET never moves the PC; APPROACH requires perception/direct LOS and respects barriers; GET stops travel on success and failure; concurrent GET has one winner. |
| **2. Backpack** | One container type, PUT/GET, LOOK contents, parent validation, capacity and recursive load. | Knife follows backpack without copied coordinates; INV omits contents; LOOK respects access; cycle/depth/capacity failures are atomic; restart preserves parent chain. |
| **3. DWELLING/cabin integration** | ROOM interaction and presentation, possession transitions through ENTER/EXIT, leave/retrieve contents. | Accessible same-ROOM objects need no APPROACH; another ROOM requires moving there; outside cannot GET inside; restart preserves cabin contents and door state. |
| **4. Equipment/use** | HOLD/WIELD and WEAR/REMOVE using actual hand/wear state; knife/backpack first. | Occupied hands reject invalid changes; readiness changes preserve identity; worn backpack retains contents; restart restores state. A rifle can test two hands without implying working firearms. |
| **5. Rabbit/resources** | Adopted encounter/combat boundary, death to generic corpse, SKIN to raw pelt/carcass/guts. | MOB ends; corpse description reflects the MOB; repeated death/skin processing cannot duplicate results; outputs persist. Whole cooking and optional butchering remain valid routes. |
| **6. Fire/first meal** | Minimal material acquisition, tinder/fuel/ignition, fire, spit/roasting, progressive EAT, required Craft/Work Timer integration. | Fuel/time survives restart; processing is atomic; retries cannot duplicate food or fuel; edible quantity decreases correctly. |

Slice 1 does not include containment, wear layers, combat, food, property administration, or CCAI. Existing valid ENTER/EXIT with held possessions must continue working before Slice 3; slice order is not permission to lose possessions or make the cabin unusable. Later slices must name missing gameplay prerequisites without adding a parallel substitute system.

**Canonical knife proof:** record knife ID K at a valid outdoor placement. A perceptible but out-of-range K can be approached; GET alone fails without moving closer. GET into the right free hand, otherwise left. Travel, then DROP at the action-boundary position and continue past K. Another PC can cross that point. Restart while K is held and while K is dropped; identity and current location survive. Repeating GET under contention yields one winner. A failed GET during travel still stops travel and never resumes it.

**Canonical backpack/cabin proof:** put K in backpack B, carry B through the cabin door, and DROP B into the ROOM. K still references B. EXIT moves the PC alone. Restart; B and K remain inside. From outside they cannot be retrieved. Re-enter and retrieve B without intra-room walking; LOOK B reveals accessible K while INV lists only held/worn items. The persisted door remains independently open or closed.

The deferred horse example only stress-tests the location contract: K → B → PC → horse → outdoors could resolve through one root without moving every descendant. It does not claim present character storage supports riding and does not add mounts to these slices.

## 12. Stop line and excluded expansion

Stop expanding the object foundation when players can reliably obtain, carry, store, equip, leave, and recover possessions, including inside the cabin, and complete the supported rabbit-to-meal interaction without identity loss or duplication. Multiple-player safety, restart persistence, and explanatory diagnostics are part of completion. Then prioritize content, playability, and the separate remaining Alpha commitments.

**DEFERRED:** mounts and vehicle implementation, mobile spaces, broad construction, ecology/agriculture, weather interactions, detailed physics, large crafting/clothing catalogs, complex locks, advanced property administration, economy, general decay frameworks, live CCAI building, and large-scale terrain modification. Direct LOS and barrier checks required for approved APPROACH remain in scope; a comprehensive perception simulation does not.

**REJECTED:** a universal category table requirement, parallel object engine, copied possession coordinates, invisible inventory, generic OBJECT collision toggles, ordinary occupants/items as tactical obstacles, object provenance histories, world creation through LOOK, mandatory rabbit butchering, delayed Craft results, and default physical property-door keys. No framework-completion slice follows the six slices.

## 13. Architecture gate and remaining decisions

Draft 5 closes the previously open policy questions: named gameplay categories replace the formal umbrella taxonomy; current identity has no provenance history; DWELLINGS use ROOM interaction; visibility is separate from reach; GET and APPROACH have distinct movement behavior; inventory shows only held/worn objects; tents preserve particular identity; collision follows the approved category rule; death/SKIN have explicit outputs; obsolete incompatible DEV possessions can be removed before hands are enabled.

**No further foundational decision is identified as necessary for Slice 1.** Thomas explicitly authorized that implementation on September 30; other slices remain outside the task except for the bounded sections 15-19 authorization. The approved policies above are locked; exact schema/locking choices, parser wording, supported initial perception parameters, and targeted DEV cleanup records are implementation preparation rather than new architecture questions.

Later content choices belong before their owning slices: actual container capacities, resource/food quantities, Craft output destinations within the physical handling rules, and the fire burn clock. None requires reopening the location model or adding a future system. If implementation evidence exposes a real conflict, present that specific conflict rather than expanding the architecture speculatively.

The September 30 instruction is the separate authorization to implement Hunting Knife Slice 1. Completion does not authorize starting Slice 2 or deploying the game.

## 14. Slice 1 implementation (2026-09-30)

Implementation commit [e4dcd3c](https://github.com/twpaige/Crown-Call/commit/e4dcd3cdd5d85a8bd04183e1d6b4d20ffb2d29da) supports LOOK → APPROACH KNIFE → GET/TAKE KNIFE → INV/INVENTORY → DROP KNIFE through the existing object and travel services. This is source-level implementation; no DEV or PROD deployment is claimed. [Current status](status.html) records verification results.

Migration `20260930_01` adds physical hand state and a unique holder/hand constraint. It seeds exactly one Hunting Knife, ID `00000000-0000-0000-0000-000000000010`, at outdoor X=600, Y=0 inches: 50 feet east of Origin. Its ground attachment supplies current HOG elevation. It is never spawned by LOOK, login, or restart. On the supported DEV seed, approach it from Origin with ordinary player commands. This is a placed world object, not free starting equipment.

GET selects right then left and uses an atomic conditional update, with a character lock and hand uniqueness protecting the destination. Success is reported after commit. Held placement has no coordinates, chunks, SPACE, or ground attachment; DROP commits the current supported surface and frees the hand. Persistent identity survives both changes. Normal ground objects do not obstruct movement.

Object visibility is deliberately conservative: query at most 64 loose candidates within the configured look range, capped at 100 feet for this slice. Read only ready certified geometry; apply cover, a direct terrain sight check, persistent walls, and unresolved natural-barrier exclusions. This limit is independent of the configured interaction range (currently 15 feet). It does not implement a comprehensive vegetation/LOS simulation or claim unknown geometry is clear. LOOK performs no surrounding walking generation for objects. Unreviewed absolute placements that do not meet certified ground remain hidden rather than being relocated.

Player APPROACH revalidates the same object's identity, placement and perception during ordinary travel integration. It stops within interaction range or safely stops when the object disappears, moves, or loses its clear path. It never pathfinds or retrieves automatically. Admin feature-ID APPROACH remains a distinct restricted path. Admin `OBJECT <id>` explains current identity, prototype, hand/parent and effective placement without changing state.

GET stops voluntary travel even when it fails, cancels pending jumps, and never resumes the old direction. Existing STOP semantics are preserved in water: swimming becomes floating, and natural current is not an invented anchor. Unsupported water-object pickup/drop remains gated. DROP accounts elapsed travel before placing the object at the character's feet and does not itself stop walking. Carrying through the existing cabin doorway preserves hand state; outside cannot GET cabin contents. No new cabin navigation or door system was added.

Before applying the migration, run `scripts/legacy_hand_cleanup.py` against the intended DEV database to list legacy held IDs. If present, explicitly remove only reviewed obsolete DEV IDs using `--confirm-dev-database --remove-dev-object ID` (repeat the latter for each ID). The migration refuses incompatible holdings rather than deleting them or inventing invisible slots. The cleanup command refuses non-development environments and post-migration use. No live cleanup was performed in this task. Do not run these operations against PROD as part of this authorization.

No WIELD, THROW, containers, rabbits, Craft, fire, food, or later-slice infrastructure was added. Existing historical exploration remains unchanged. The public source, command reference, status, and private onboarding/reference snapshot carry continuity; publication and game deployment are separate.

## 15. Rabbit MOB THROW and REMOVE hit test

The initial hit-test increment added `THROW KNIFE RABBIT` and
`REMOVE KNIFE RABBIT`. Section 16 now extends it with actual wounds, death and corpses;
the command, identity and movement rules below remain in force.

- A migration authors one persistent rabbit MOB at X=840, Y=0 inches (70 feet east
  of Origin), on supported HOG ground. Its test ATHLX is 0. LOOK and restart never
  spawn replacements. Movement AI, concealment behavior, bleeding simulation,
  fire, cooking and food remain deferred. Section 17 adds bounded corpse SKIN.
- `THROW <object> <target>` requires a single throwable object in either physical
  hand. Holding is sufficient; WIELD is not required. A wielded object remaining
  in a hand qualifies by the same predicate. No WIELD command or equipment system
  is added. The Hunting Knife's existing prototype gains a throwable capability.
- Use adopted C05/R1/R2/R4/W1a/W11 hit resolution: attacker d20 + RANGE + the
  knife's one-hand modifier (+2), minus one per complete ten feet, opposed by
  d20 + the MOB's ATHLX dodge. Ranged attacks require at least ten feet. Higher
  total wins; a tie does not hit. No Aggression or armor modifies hit probability.
  RANGE is persisted and defaults to ordinary competence, 0, for this test increment.
- The wound resolution adopted explicitly in section 16 supersedes the original
  hit/miss-only stop line. No full encounter, initiative, turn scheduler or repeated
  attack loop is added; each explicit THROW resolves one opposed attempt.
- Targets must pass the existing bounded, ready HOG ground/visibility/barrier checks.
  Cabin walls, interior boundaries, unavailable geometry and unsupported water remain
  barriers. Both THROW and REMOVE stop voluntary travel through existing STOP behavior;
  they never close distance automatically or resume movement afterward.
- A miss leaves the same knife on supported ground at the target's feet. This bounded
  landing rule adds no scatter or flight simulation. A hit transfers the same knife
  to an embedded-MOB location with no independent coordinates or hand. LOOK describes
  the surviving rabbit and its wounds with the embedded knife; section 16 governs death.
- `REMOVE <object> <target>` requires the visible MOB or ground corpse within the existing 15-foot
  interaction range. Move closer with ordinary travel if needed. It uses the first
  free hand (right, then left); full hands fail without dropping or moving anything.
  Removal is a conditional transfer of the original object, not a replacement knife.
  GET does not retrieve embedded objects. Restart preserves both embedded and removed state.
- The object's exclusive location constraint now covers ground, character hand, or
  embedded MOB or corpse OBJECT. Competing transfers cannot duplicate the knife. Deleting a referenced
  MOB cannot cascade-delete its embedded possessions.

The previous Slice 1 bug fix, `3af197f`, shares controller attachment between DEV
startup and construction. Its travel-tick regression preserves HOG generation checks.
It also supplies immediate connection feedback and protects character entry against
competing sockets and stale callbacks. No game deployment is implied by these commits.

## Source map for deeper review

The historical exploration remains [CC_Objects.txt](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/CC_Objects.txt). Its useful clusters are: identity/location and boundaries (19–25, 221–239, 601–640); storage and spaces (721–780, 1561–1640); inventory alternatives and handling (781–860); durability (961–1000, 2051–2054); concrete slices (1121–1260); templates (1941–1980); convergence and the first-slice gate (1981–2080). These are evidence and alternatives, not 2,080 approved requirements.

For governing gameplay, consult the [preserved design](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMUD_Design.txt): C01–C02, C09–C11, Q86–92, Q114–130, and Q146–148. Its earlier world-time values are superseded by its dated 1.75× decision; implementation remains separately evidenced.

For current boundaries, consult [HOG](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/HOG.md), [commands](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/COMMANDS.md), and [publishing](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/PUBLISHING.md). The current command reference records GET/TAKE and INV/INVENTORY/I as implemented, with outdoor object support bounded by Slice 1.

For implementation evidence, also consult [cabin attachment](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/hog_structures.py), [ground placement](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/hog_placement.py), [Nearby formatting](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/cormac.py), [world tests](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/tests/test_world.py), and [HOG cabin tests](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/tests/test_hog_cabin.py).


## 16. THROW wounds, death and corpse (2026-09-30)

Thomas explicitly authorized this bounded extension and resolved the wound-rule
conflict: adopt current weapon severity modifiers instead of old weapon ceilings.
Use the existing W3 wound ladder (MoV 1–4 FLSH, 5–8 LITE, 9–12 DEEP, 13–16 SEVR,
17–20 GRAV, 21+ MORT), then weapon SM, armor, and finally a MOB severity modifier.
Clamp each stage to the existing ladder; no rank exceeds MORT. Hunting Knife SM is
+0; an unarmored rabbit receives the existing +1 armor adjustment. The rabbit's
persisted MOB severity modifier is **+2**. This is a target property, not extra hit
points, an attack bonus, a special knife/rabbit kill rule, or a DEEP death threshold.

After weapon/armor, FLSH becomes DEEP, LITE becomes SEVR, DEEP becomes GRAV and
SEVR or worse becomes MORT. For this unarmored knife/rabbit matchup, MoV 1–4 yields
SEVR, 5–8 yields GRAV and 9+ yields MORT. Poor hits can leave a surviving wound;
solid hits are lethal. W8 combines equal wounds up the ladder (two GRAV become
MORT); separate MORT boxes do not combine upward. Persist normalized wounds on
surviving MOBs. C5's worst wound level penalizes the rabbit's ATHLX dodge on its
next opposed throw defense, without changing its stored base ATHLX.

**LOCKED bounded animal death:** a rabbit reaching MORT dies immediately, including
through combined wounds. Thomas explicitly selected this while excluding bleeding
simulation. Ordinary character Scale-1 MORT/unconsciousness/bleed-out rules remain
unchanged and are not implemented by this increment. Other MOB species do not
silently inherit rabbit mortality rules.

Death removes the active MOB row and creates one generic CORPSE OBJECT at its
current supported ground location. Its current details retain name, species and
weight; the bounded authored rabbit profile weighs 48 ounces. No provenance log,
respawn or replacement animal is created. A corpse has its own object ID. Every
embedded item's original ID moves from the MOB to the corpse parent in the same
transaction, with no independent coordinates. Surviving wounds, knife placement,
corpse creation and ending the MOB commit together or roll back together. A
conditional wound revision update ensures retries and simultaneous deaths cannot
create duplicate corpses or apply a stale wound twice.

LOOK describes the corpse and its embedded items; inventory does likewise when
held. `APPROACH CORPSE`, `GET CORPSE`, `DROP CORPSE` and `REMOVE KNIFE CORPSE` use
ordinary movement, perception, hand and portability rules. `REMOVE KNIFE RABBIT`
can also match the rabbit corpse through the same nearest-first/ordinal selection. REMOVE works from a visible
ground corpse or one's own held corpse, needs a free hand, and cannot take a knife
from a corpse that another player has picked up. A held corpse and its embedded
knife follow the holder through outdoor travel and ROOM transitions. Parent deletion
cannot silently cascade-delete an embedded knife. A generic corpse is not a carcass.

Migration `20260930_03` preserves existing rabbit/knife identity and location,
initializes existing rabbits without fabricated wounds, sets rabbit severity +2,
and adds the generic corpse prototype/portability plus exclusive embedded-object
placement. It does not spawn another rabbit or corpse. Section 17 adds SKIN and
carcass/pelt/guts; butchering, decay, fire, cooking, food, bleeding simulation, MOB AI, other species'
wound/death profiles and broader combat remain outside this increment.


## 17. Rabbit corpse SKIN (2026-09-30)

Thomas authorized the next bounded interaction: `SKIN CORPSE` consumes an accessible
rabbit corpse and creates exactly three persistent OBJECTS: **rabbit carcass**,
**raw rabbit pelt**, and **rabbit guts**. All outputs go on the ground at the
character's feet, never into an invisible inventory or automatically into a hand.
The carcass is cleaned and will later be roasted whole; cooking is not implemented.

The corpse may be on the ground within ordinary interaction range (currently
15 feet outdoors) or held by the acting character. Existing perception, barriers,
ROOM access and supported ground rules apply. Another character's held corpse is
not accessible. A suitable cutting tool must be held in either physical hand;
the Hunting Knife qualifies without WIELD. It keeps its original identity and hand.
A ground corpse can be processed with both hands occupied if one holds the tool.
Remove any embedded objects first, preserving their identities before consuming
the parent. SKIN stops voluntary travel through the existing movement authority.

Consumption and creation form one transaction. A conditional corpse claim checks
its unchanged physical location, absence of embedded items and a held cutting tool.
Concurrent attempts or retries cannot consume the corpse twice or produce duplicate
outputs. Failure rolls back the input and outputs together. Each output has its own
persistent object ID, ordinary portable capability and ground/ROOM location. Outdoor
outputs use existing HOG ground attachments; indoor outputs use the current ROOM.
LOOK, GET, DROP and restart preserve normal physical-object behavior.

Migration `20260930_04` adds only three output definitions/portability and the
Hunting Knife's cutting capability; it does not spawn or relocate instances.
The bounded 48-ounce rabbit yields a 36-ounce carcass, 6-ounce raw pelt and
6-ounce guts. This is a fixed test profile, not a general resource/yield system.
No tanning, butchering, Craft, work timer, spit-making, fire, cooking, EAT, decay,
bleeding simulation, MOB AI or broader resource/combat system is added.


## 18. MAKE SPIT and GATHER FIREWOOD (2026-09-30)

**Execution update:** section 19 supersedes the original bespoke handlers with
Craft Engine definitions, adds a held cutting-tool requirement to MAKE SPIT, and
adds persistent retry receipts. The weights, ground outputs and five-minute costs remain.


The next authorized increment adds exactly two basic survival actions, without a
learned recipe, CAP, cutting tool or empty-hand prerequisite:

- `GATHER FIREWOOD`: one persistent firewood OBJECT, a 5 lb bundle of ordinary
  dead wood and brush (quantity one, 80 ounces).
- `MAKE SPIT`: one persistent simple wooden spit OBJECT (quantity one, 8 ounces),
  fashioned from a suitable fallen stick in the local environment.

All outputs go on the ground at the character's feet. Hands and held objects are
unchanged. The objects use ordinary IDs, portable definitions, HOG ground attachments,
LOOK, APPROACH, GET and DROP, and survive restart. The spit is intended for later
whole-rabbit roasting; neither cooking nor a fire is implemented by these actions.

**Bounded availability:** read the existing HOG light/heavy woodland classification
at the current position: wooded grassland, woodland, mixed forest or wet forest.
This permits ordinary woody material gathering as a gameplay abstraction, not a
claim of a surveyed stick at walking resolution. Supported dry ground is also
required. Open grassland, interiors, water, unsupported/unavailable geography and
unreviewed biomes fail without material or labor changes. No individual landscape
objects, resource nodes, resource counters or general depletion system are created.

**Minimal Craft integration:** results are immediate and deterministic. Each action
adds five real minutes of labor in the same transaction as its output. Persist one
per-character `work_until`; compute `max(now, work_until) + duration`. Entry requires
remaining debt strictly below 48 real hours; an accepted action may cross that
threshold. Debt elapses online/offline without travel or world-time acceleration.
The response states remaining whole minutes (rounded up). Costs and bundle weights
are bounded test tuning, not a new general recipe/catalog system.

A conditional labor/position update rejects stale concurrent attempts. Failed
validation or a failed transaction leaves no object and no debt. Every deliberate
new command is new work and may produce another output with another labor charge;
C02 explicitly allows repeated Crafts. There is no per-recipe cooldown. The existing
command protocol has no retry identifier or automatic command replay; this does not
promise deduplication of two separately submitted commands. Restart never replays
creation. Commands stop travel through the existing accepted-movement boundary.

Migration `20260930_05` adds only the timestamp and two portable definitions. It
neither spawns outputs nor changes existing object identity, hand or location.
Ignition, burning/fuel consumption, roasting, EAT, tanning, butchering, broad gathering,
learning, and a general crafting catalog remain outside this increment.


## 19. Craft Engine v1 and web Builder (2026-09-30)

Thomas authorized one server-side Craft Engine and a small web authoring foundation.
A CRAFT is a persistent data definition, not a command-specific Python handler.
The original GATHER FIREWOOD / MAKE SPIT handler is removed. Exact normalized command
aliases resolve to definitions and all validation/execution follows the same path.
The two seeded definitions are:

| Craft | Sector expression | Inputs | Held tools | Outputs at feet | Work Timer |
| --- | --- | --- | --- | --- | --- |
| GATHER FIREWOOD | `TREES & !WATER` | None; local ordinary dead wood/brush is abstracted | None | One 5 lb firewood bundle | 300 real seconds |
| MAKE SPIT | `TREES & !WATER` | None; suitable local fallen stick is abstracted | `tool` capability with `purpose: cutting`; Hunting Knife qualifies | One 8 oz simple wooden spit | 300 real seconds |

### Definition and execution contract

Definitions contain stable identity, name, exact player command aliases, required
input prototype IDs/quantities, required held tool capabilities/properties, output
prototype IDs/quantities, optional sector expression, real-second labor cost, and
placement. The only v1 placement/effect is `ground_at_feet`. There is no script,
eval, user-defined callback, construction effect, or recipe-specific executor.

The server validates strict fields, registered flags, existing ordinary prototypes,
known capability/property combinations, positive material quantities and bounded
lists/costs. Current limits are eight aliases, eight rows per material/tool list,
100 total input units and 100 total output units, and 0-48 hours per action.
Aliases cannot replace an existing game verb or collide with another craft.
A revision check prevents lost Builder edits. Definition edits affect future work;
existing physical objects and completed request receipts never reset.

Inputs must be held or visible within ordinary interaction reach, using existing
HOG perception/barriers or same-ROOM access. Another character's held possessions
are unavailable. A partial stack retains its identity and remaining quantity; full
consumption removes the input and ground attachment. Required tools are held in an
actual hand, not on the ground or embedded, and are never consumed as inputs.
Objects with embedded children and specialized corpse prototypes are excluded from
v1 generic processing. Existing SKIN remains under its section 17 handler/rules;
future migration needs explicit corpse targeting/state support, not a shortcut.

All outputs have new persistent IDs with ordinary quantities and direct ground/ROOM
locations at the accepted character position. Hands are unchanged. Existing outdoor
dry-ground checks and HOG attachments apply; sector eligibility never grants ground
placement. Non-portable outputs are permitted on the ground; inputs remain portable.
Craft never grants hand eligibility to non-portable objects. No invisible inventory
or automatic hand juggling.

Consumption, creation, ground attachments, Work Timer, character execution revision
and optional request receipt commit atomically. Work Timer remains
`max(now, work_until) + cost`, immediate results, strictly below 48-hour entry,
real time online/offline and no accelerated clock. Stable input ordering and
conditional position/tool/input/revision checks prevent competing attempts from
consuming an input twice or losing debt. Failed transactions restore all state.
Zero-cost crafts still advance the execution revision for concurrency protection.

The browser supplies a random request ID per submitted command. Same character,
same request ID and same normalized craft command returns the original result,
even after restart or alias edits, without another output, input consumption,
debt charge or travel stop. Reusing that ID for a different craft command fails.
Fresh IDs or commands without IDs represent new deliberate work; repeated work is
allowed under C02. Receipts are durable current retry records, not object histories.

### Sector expressions

The grammar supports registered uppercase flags, `!` NOT, `&` AND, `|` OR and
parentheses, with NOT then AND then OR precedence. No expression means no sector
restriction. `TREES` only requires TREES, regardless of other flags. Multiple flags
may be true. `(TREES & MOUNTAIN) | ROCKY` works without a second geography map.

Initial names are TREES, GRASS, MOUNTAIN, ROCKY, WETLAND, WATER, SHORE, ROAD and ROOM.
[HOG sector facts](hog.html#craft-sector-facts-2026-09-30) defines their sources and
limits. Unavailable facts remain unknown, including under NOT; a restriction must
resolve to true. Add future names such as PRISON through the registry and an
authoritative fact adapter, not by editing the parser or storing craft-owned sectors.

### Web Builder and future clients

The flow is **web Builder -> validated server Builder API -> definitions -> Craft
Engine**. Builder/admin accounts access `/builder` from the account screen.
Forms provide prototype/quantity rows, capability dropdowns, aliases, flag/operator
controls and labor cost. Browser code serializes the form and renders server results;
it owns no crafting, geography, input-consumption or permission rules.

`GET /api/builder/crafts` supplies authorized definitions and form choices.
`PUT /api/builder/crafts/{id}` accepts `{revision, definition}`: 0 creates, the loaded
revision edits. Authentication and builder/admin authorization apply server-side.
Writes also require the non-simple `X-Crown-Builder: 1` header, with existing strict
same-site cookies and no cross-origin CORS grant. Definitions record/log the editor
and revision. Stale saves or conflicting aliases fail atomically.

The shared validated server operations are the boundary for future Object/MOB
Builders and restricted CCAI clients. CCAI does not receive shell/database authority;
section 20 adds the bounded Object Builder, while MOB Builder and CCAI integration
remain deferred. Content authoring belongs
primarily in this web interface, not a growing in-game builder-command language.

Migration `20260930_06` adds definitions, unique aliases, execution receipts and a
character execution revision, and seeds the two crafts. It preserves existing
Work Timer timestamps, objects, quantities, identities and locations. It creates
no physical objects. No fire, ignition, roasting, EAT, tanning, butchering, cabin
construction, resource depletion, quality, discovery or progression is added.


## 20. Object Builder and timed morph v1 (2026-09-30)

Thomas authorized a compact desktop `/builder` with **Crafts | Objects**. Both
editors use short rows and three columns, fitting ordinary creation/editing on a
1280×720 desktop viewport without page scrolling. Longer material lists scroll
within their panels. The browser only authors definitions; the authenticated,
revision-checked server operations remain the authority and future CCAI boundary.

Object authoring exposes stable identity (immutable after creation), name, keywords,
ground and held/inventory descriptions, weight in ounces per unit, portable,
existing cutting/woodcutting tool purpose, existing throwable attack/severity
modifiers, and timed morph. Capabilities have strict server schemas; no arbitrary
JSON, scripts or unrestricted database access. Saves record editor/revision/time.
Saving never spawns an instance. There is no prototype deletion,
container, armor, durability or MOB Builder in v1. Mechanical edits that affect
live instances or pending morph destinations are rejected; use a new prototype.
Description corrections remain shared. Saves revalidate affected definition rules
and Craft compatibility; ground outputs may be non-portable, but inputs remain
portable. Morphable input/output quantities must be one.

### Timed morph definition and persisted state

An optional `timed_morph` capability contains `after_seconds` (positive integer real
seconds), `action` (`morph` or `disappear`), and `target_prototype_id` (required for
morph, absent/null for disappear). No world-time, Work Timer or travel multiplier
applies. Chains must be acyclic and have at most **16 transitions**: enough for
short staged lifecycles, while bounding validation and unattended catch-up. Each
duration is at most 315360000 seconds (ten 365-day years), preventing unreasonable
arithmetic/input sizes. Targets must exist; corpses and specialized-state morphs
are excluded. Stages preserve portability so a held object remains legally held.

Ordinary object creation captures the complete small pending chain as absolute UTC
deadlines and destination prototype IDs, with null destination meaning disappearance.
The instance stores this pending list plus an indexed `morph_at` equal to the first
remaining deadline. Each later deadline is computed from the preceding deadline,
never the observation time. Applied entries are removed; there is no history or
copy of all prototype defaults. Timing/target edits affect new lifecycles only.
Existing instances without timers do not acquire one on a definition edit.

Reconciliation catches up all elapsed stages in one transaction. Morphs retain the
same object ID, quantity one, and valid hand, embedded-parent, ROOM or outdoor
placement, including existing ground attachment semantics. Descriptions, weight
and capabilities then come from the new prototype. Terminal disappearance removes
the object and its ground attachment, freeing its hand if held.

**Explicit embedded-object rule:** Thomas directed that a morph proceeds even when
items are embedded in the morphing object. Every embedded descendant disappears
in the same transaction on each morph or terminal disappearance. This is an
explicit lifecycle exception to the ordinary parent-deletion protection, not a
general container cascade policy. An embedded object may itself morph while
preserving its own parent; its terminal disappearance leaves that parent intact.
Corpse morphs remain excluded, so ordinary rabbit death/SKIN/knife recovery rules
are unchanged. Wearable stages must retain the same anchor/layer throughout the chain; a worn
instance retains its role or disappears, never silently relocates. General container
morph behavior remains deferred.

### Relevance, concurrency and restart

One scoped reconciliation service runs before local LOOK/ROOM presentation,
inventory, object interactions, Craft input/tool checks, APPROACH selection and
travel-target revalidation, and carried-load snapshots. It includes relevant
embedded objects. Expired rows are reconciled before visibility result limits,
name matching or capability checks. A changed/vanished APPROACH target stops travel
through the existing navigation authority. Unrelated distant objects are untouched.

Short database transactions reconcile elapsed time, then lock relevant lifecycle
objects through interaction validation and mutation. Deterministic object-lock
ordering and the same service arbitrate simultaneous readers/transfers/morphs;
SQLite tests use conditional/write locking, PostgreSQL uses transaction row locks.
Elapsed reconciliation is committed before ordinary action validation so rejection
of a request does not reset the timer. Embedded deletion, stage changes and pending
deadlines commit or roll back together. A small authoring transaction lock serializes
morph graph edits, Craft definition validation and new schedule capture, including
concurrent edge changes that would otherwise introduce a cycle. No object mutation
history or general scheduler is added.

Restart reads committed deadlines and catches up lazily. Craft retry receipts may
report the original creation, but never recreate expired outputs. Read-only admin
diagnostics do not reconcile state. There is no global object polling, background
cleanup worker, ignition, fuel consumption, cooking or new campfire recipe in this
increment. The deadline index permits bounded cleanup later if actually needed.
Migration `20260930_07` adds authoring metadata and timer state without starting
old timers, spawning objects or changing existing identity/location.


## 21. Builder cloning and ordinary target usability (2026-09-30)

Both Craft and Object Builder provide **Clone** for an existing definition. Clone
copies editable form data into an unsaved draft named `Clone of <original name>`,
prepares a new editable stable ID, and clears the source revision/editor metadata.
Nothing is persisted until Save. Save uses the existing revision-zero create path;
selecting an existing ID cannot overwrite it. Craft aliases are copied and still
must be made unique before saving. The source definition is never modified.

Ordinary object/MOB target resolution and Nearby share nearest-to-farthest ordering.
Equal distances use absolute eight-point compass sector, then namespaced stable ID.
No ordinal selects the first eligible match; `2.knife`, `3.rabbit`, etc. select the
second/third match within the command's eligible candidates. This applies to GET,
LOOK inspection, APPROACH, DROP, THROW, REMOVE and SKIN. GET counts only reachable
portable ground/ROOM objects, never held objects. REMOVE counts reachable parents
with the requested embedded object; its object ordinal is local to that parent.
Physical reach, perception, hands, combat range and atomic transfer rules remain
in force. Ordinals are contextual selectors, never persistent identities. LOOK
BRIEF/NORMAL/MAXIMUM retain their existing geographic meanings.

Nearby uses separate lines, without stacking, for objects and MOBs alike:

```text
Nearby:
A Hunting Knife. (here)
A simple wooden spit. (2 ft E)
A rabbit carcass. (15 ft E)
```

Capitalize the description's beginning and place its period before the parenthesized
location, with no punctuation after the closing parenthesis. Distances below half
a foot display `(here)`; other distances use rounded feet and an absolute compass
abbreviation. This changes Nearby, not the clockwise geographic survey.


## 22. Pioneer Cabin ROOM-object POC (2026-10-01)

### Locked architectural constraint: no nested dwellings

**A dwelling may NEVER exist inside a ROOM or inside another dwelling.**
This is an intentional architectural prohibition, not deferred functionality.
Dwellings remain anchored to the continuous HOG world. Dwelling-within-dwelling,
legacy-ROOM-hosted instances, ROOM-anchored dwellings and recursive dwelling
containment are prohibited through every creation and placement mechanism.

A large authored area such as Origin is one dwelling. Shops, inns, houses,
churches and other building interiors represented with legacy ROOMs are additional
ROOMs owned by that same Origin dwelling, not separate dwelling instances.
Local keys organize them for builders/admins: `MAIN_01`, `MAIN_02`, `SSS_MAIN`,
`SSS_STORAGE`, `SSS_WORKSHOP`, `BB_DINING`, `BB_KITCHEN`. Normal room names remain
player-facing. Existing legacy-room doors, locks and links connect street rooms
to building interiors; they do not require additional dwelling instances.

A dwelling **owns** ROOM objects; PCs, MOBs and ordinary objects **occupy** ROOMs.
The cabin is a WorldObject linked one-to-one to its exterior Space. Owned ROOM
WorldObjects reference that dwelling object and have typed ROOM data. Character,
MOB and object `room_id` references point to ROOM objects, never exterior Spaces.
The Python `space_id` compatibility spelling aliases `room_id` for occupants; it
is not a second stored location. Portal/GroundPlacement Space references still
mean exterior geometry.

Placement is exclusive: outdoors has coordinates; indoor membership and owned
ROOM placement have no X/Y/Z or chunks. Held/embedded items inherit their physical
parent's location. Resolve immediate ROOM context separately from the dwelling's
outdoor anchor. Common exterior coordinates never grant cross-ROOM access.

ROOM infrastructure is quantity one, non-portable and excluded from ordinary
handling, Craft inputs/outputs, timed morphs and ordinary creation. Existing fixed
ROOM/dwelling objects cannot be moved, reparented, morphed, consumed or deleted
through ordinary lifecycle operations. The subsequent ROOM authoring slice below
exposes these instances through Object Builder without enabling ordinary manipulation.
BUILD CABIN remains deferred.

LOOK, GET, DROP, inspection and ordinary physical outputs use current ROOM
membership, with no 15-foot radius. Accessible ROOM candidates display `(here)`;
stable identity breaks their equal-distance ties. Multiple PCs and MOBs can occupy
the same ROOM. SAY is room-wide; different ROOMs do not hear each other merely
because they share a dwelling. The existing open-door speech path is limited to
the entry ROOM and real exterior doorway. Indoor MOB scope is occupancy, LOOK,
admin LOAD/FIND; THROW/combat remains outdoors-only.

The Pioneer Cabin owns Main Room, Bedroom and Loft. Main Room `north` leads to
Bedroom in one real second; Bedroom `south` returns in one second. Main Room `up`
leads to Loft in two seconds; Loft `down` returns in two seconds. All four cost
zero stamina. Exterior ENTER reaches Main Room. Only Main Room has EXIT through
the existing doorway; door state and HOG exterior safety remain authoritative.

ROOM links are directed instance records with exact normalized trigger text,
destination, integer real-time delay (0–3600 seconds), integer stamina percentage
(0–100), and optional departure text. Reverse links are independently authored.
There is no link-count/directional-column limit. Direction abbreviations normalize
to full direction triggers. Other phrases are exact, bounded text, not scripts.
Core verbs are protected; Craft and ROOM-link authoring reject alias collisions.
Unmatched indoor movement never falls through to HOG coordinate travel.

A small session-local pending transition echoes departure, waits without blocking
input, revalidates the link/source and character ROOM revision, then atomically
changes membership and deducts the configured percentage of maximum stamina.
Concurrent completions cannot double-charge. Arrival calls ordinary LOOK directly.
LOOK/SAY leave pending travel intact. A conflicting physical/movement command,
including STOP, automatically cancels it before executing. Disconnect/restart
cancels without cost, leaving the PC in the source ROOM. No HOG movement, distance
credit, persistent scheduler or world-time multiplier participates.

FIND extends its existing SQL parent resolution through occupants, ROOM ownership
and dwelling anchors. Results include ROOM and dwelling context. ROOM infrastructure
is omitted from ordinary OLIST/FIND results. JUMP FIND re-resolves the displayed
instance and enters its actual containing ROOM, including through held/embedded
parents, without extracting anything. The owning cabin must retain certified
exterior attachment. Searches remain cold/read-only and retain existing paging.

Migration `20261001_01` preserves the reviewed footprint, doorway, open state,
existing PC/object identities and held/embedded relationships. Existing cabin
occupants/contents move to Main Room and lose obsolete interior coordinates.
Unknown Space layouts or conflicting direction Craft aliases fail preflight for
review rather than being guessed. The legacy `pioneer_cabin` ID also identifies
Main Room for compatibility; the dwelling and other ROOMs get distinct IDs.
A system-only transactional POC instantiation operation allocates independent
ROOM/link graphs for another dwelling; it does not grant that exterior HOG approval.
Existing layouts are not automatically rewritten by prototype edits. Vehicles,
construction, property systems and indoor combat remain deferred. The bounded
layout authoring interface is specified below.


## 23. ROOM/DWELLING Builder and ROOM text (2026-10-01)

The existing **Crafts | Objects** Builder now offers **Normal | Rooms | Dwellings**
filters within Objects. Normal prototypes retain their existing workflow. ROOMs
and dwellings are authored **instances**, not spawnable ordinary prototypes.
Choose a dwelling/exterior association, then its ROOM. Under Objects → Rooms,
the existing room selector displays local keys, sorted alphabetically ignoring
case. Its adjacent live filter matches local-key substrings only; clearing restores
the complete sorted dropdown. While filtering, the existing selector expands to
show up to six choices with scrolling for additional matches. Exactly one match
automatically opens that ROOM through the normal room-switch handler; multiple
matches require selection. Switching retains unsaved edits in the draft and never
automatically saves. No matches leave the current editor intact. Room names remain player-facing. Compact forms expose name,
keywords, ground/short descriptions, ROOM LOOK description, and directed links.
Each link has an exact trigger, destination ROOM dropdown, real seconds, percentage
of maximum stamina, and optional departure text. Add/remove link rows explicitly;
reverse links are never inferred and there is no six-exit limit.

Dwellings expose their owned ROOMs and entry ROOM. Their existing Space/Portal
records still own exterior footprint, anchor and door state; the Builder displays
that association read-only and does not author another geography map. New dwelling
infrastructure may be attached only to an existing unassociated Space with one
existing door. This does not certify a new HOG placement. There is no exterior
construction, dwelling cloning or BUILD CABIN command. The migrated Pioneer Cabin
is immediately available with Main Room, Bedroom and Loft.

Saving a new ROOM explicitly creates its fixed owned object. Ordinary prototype
Save still never spawns an object. Existing ROOM keys and ownership are fixed;
this slice offers **no ROOM/dwelling deletion**, including empty ROOMs. It cannot
consume, move, morph, take or reparent infrastructure. Existing infrastructure
weight is read-only, consistent with the Object Builder live-mechanics restriction.
Normal handling, occupancy, delayed travel, FIND/JUMP FIND and exterior safety
remain the proven POC authority. Description edits appear in the next normal LOOK.
Entry ROOM edits change which ROOM uses the existing doorway, not its geometry.

### Shared authoring boundary

GUI and ROOM text use the same authorized, typed server service. Future CCAI and
construction clients can use that service without database access. The existing
Builder/admin session boundary and request-header protection apply. Unknown fields,
capabilities, malformed keys, protected command/Craft collisions, duplicate triggers,
invalid destinations, delay/stamina and blank descriptions are rejected server-side.
Direction abbreviations normalize using the existing runtime convention.

A dwelling-wide author revision covers its metadata, owned ROOM content, entry
and links. Saves compare the revision under the shared definition-edit lock and
commit atomically. Stale saves/import retries require reload; concurrent imports
cannot both create the same graph. Unchanged links retain identity; changed or
removed links are revalidated by pending travel before arrival. ROOM object IDs,
occupants and nested contents are preserved on edits. If old POC instances share a
prototype, authoring isolates the edited instance's descriptive prototype rather
than renaming another dwelling's ROOM. No gameplay morph or placement change occurs.

Persistent additions are one local authored key per ROOM, a dwelling author revision
and last editor. Migration `20261001_02` backfills `main_room`, `bedroom`, `loft`
for the existing cabin and preserves instance/occupant/link identities and geometry.
Keys are unique within their owning dwelling under the validated transaction path.

### Human-readable ROOM format

Objects → Rooms/Dwellings → **Import / Export** supports paste or UTF-8 text upload,
**Validate & review**, then **Import reviewed text**. Validation creates nothing;
review reports ROOM/link counts, new versus updated keys and entry. Editing text
invalidates review. Import revalidates the saved dwelling revision and all rules
inside the transaction. Either the complete change commits or none does.

```text
ENTRY: entrance

ROOM: entrance
NAME: Mine Entrance
DESC:
A rough-hewn passage descends into darkness.
ENDDESC

LINK: north -> main_tunnel
DELAY: 1
STAMINA: 0
ENDLINK

ROOM: main_tunnel
NAME: Main Tunnel
DESC:
Heavy timbers brace the walls of this broad mining tunnel.
ENDDESC

LINK: south -> entrance
DELAY: 1
STAMINA: 0
TEXT: You head toward the entrance.
ENDLINK
```

- `ROOM` keys use lowercase letters, digits and underscores, begin with a letter,
  and contain at most 40 characters. References use keys, never UUIDs/foreign keys.
- `NAME` and multiline `DESC` are required. A new `ROOM:` starts the next section;
  each `LINK` must end with `ENDLINK`, and each description with `ENDDESC`.
- Optional `KEYWORDS`, `GROUND`, `HELD`, `WEIGHT` preserve all authored object fields.
  Omitted values default from NAME, with weight zero. Export emits them explicitly.
- Optional `ENTRY` appears before ROOMs. If absent, preserve the existing entry,
  or use the first imported ROOM for a new dwelling.
- `DELAY` defaults to 0 and accepts integers 0–3600; `STAMINA` defaults to 0 and
  accepts integers 0–100. Optional `TEXT` is one line of up to 250 characters.
  ROOM descriptions are limited to 1000 characters. Other fields retain Builder
  limits. Unknown/repeated fields and malformed sections fail plainly.
- Blank lines and `#` comments outside DESC are ignored. Within descriptions,
  prefix a literal directive-like line (`ROOM:`, `NAME:`, etc.), `ENDDESC`, or a
  leading backslash with a backslash; export escapes these automatically.
- Imports **merge by key** into the selected dwelling. Matching ROOMs retain IDs;
  new keys create new ROOMs. A supplied ROOM replaces its description/fields and
  complete outgoing link list. Omitted ROOMs and their links remain unchanged.
  Renaming a key means adding a different ROOM, not moving or deleting the old one.
- Destinations must resolve within the resulting dwelling graph, including retained
  ROOMs. This local graph authoring format does not represent cross-dwelling links;
  it refuses an existing external link rather than silently losing it on export.
- Text is limited to one million characters and a resulting dwelling to 500 ROOMs
  as authoring request bounds, not a six-direction rule. No scripting or conditional
  exits are supported. Ordinary explicitly authored return links remain valid.

**Export saved** emits the persisted complete graph at a named revision into the
same textarea; **Download text** saves it for editing/reimport. Unsaved form edits
are not exported or included in text import. This slice supplies ROOM templates
only, not ordinary Object or MOB template/import systems, or DEV→PROD publishing.


## 24. Reusable DWELLING templates and installed doors (2026-10-01)

This extends the instance authoring in section 23. Objects → **Dwelling prototypes**
creates reusable definitions; Rooms/Dwellings still edit selected live instances.
The compact template editor provides identity, descriptions, weight, width/depth,
one exterior entrance, side controls, and an **Interior Rooms** GUI.
The scrolling room lists in dwelling instance/template editors sort alphabetically
by local key and filter live by case-insensitive local-key substring. Clearing
the filter restores all rooms. Filtering is client-side presentation only; no
folders, categories, pagination, graph edits or hierarchy are introduced. Add Room or
Add Rooms → Create adds unsaved template entries; Edit opens normal ROOM fields and
compact link controls with named destination dropdowns. Choose the entry ROOM in
the main form. Text import/export remains an optional power tool. **Save creates no
instance.** Template text replaces the draft graph; instance imports retain the
merge behavior in section 23. Server authorization, strict validation and revision
comparison govern both APIs, including future CCAI callers.

The [no-nested-dwellings constraint](#locked-architectural-constraint-no-nested-dwellings)
is permanent: no dwelling can be hosted by any ROOM or another dwelling.

`LOAD O <dwelling_prototype_id>` requires an admin outdoors on dry land. Each LOAD
is a new creation request. One transaction creates the dwelling root, fresh owned
ROOM objects, directed links, physical door states, entry reference, Space/Portal
geometry and virtual key grants. Failure rolls back everything. Template-local
ROOM/door keys resolve only within that instance; no reverse links are inferred.
Existing instances never acquire later template edits. Their descriptive prototypes
are isolated snapshots, while Space records the reusable source prototype identity.
These internal `auth_<uuid>` ObjectPrototype rows are referenced by live dwelling/
ROOM objects; they are not extra draft or usable template identities. Validate for
Use writes nothing, and Save updates the existing stable prototype ID. OLIST hides
ROOM definitions and DWELLING definitions lacking a reusable template, including
internal snapshots and legacy non-loadable dwelling definitions. Draft and usable
reusable templates retain their human-controlled catalog IDs. Snapshot LOAD remains
forbidden; no deletion/merge is needed to correct catalog visibility.

### Bounded exterior placement

V1 uses a rectangular footprint, 5–100 feet per side, with a single south entrance
at the loading character's feet. The footprint starts 30 inches north of the
entrance and extends north. This is fixed orientation, not a rotation/placement UI.
The complete footprint and entrance apron require ready, dry, boundary-free HOG
geometry, slope at most 0.05 and no more than one inch of ground-height variation.
Grid-cell vertices and footprint extents are checked; corners alone are insufficient.
Existing structures, entrances and physical occupants/objects cannot be obstructed.
There is no foundation, terrain levelling or fallback invented geometry.

The persisted generation identity, ground attachment and geometry are revalidated
for use. Space/Portal still own exterior collision and doorway coordinates; ROOMs
still own indoor occupancy. The original Pioneer Cabin retains its existing audited
attachment and door state. APPROACH uses continuous player travel toward the single
entrance and stops within the normal 15-foot interaction range. OPEN/ENTER and other
exterior operations require reach and a clear certified approach. ENTER uses that
instance's entry ROOM; only that ROOM can EXIT to the same exterior entrance.

### Shared installed door state and side access

A physical Door row belongs to one dwelling and has `OPEN`, `CLOSED` or `LOCKED`
state plus independent `barred`. OPEN+barred is invalid, including at the database
boundary. A directed link or exterior side supplies its own description, optional
virtual KEY identity, and BAR access. Explicitly paired links share only physical
state; they may expose different controls. Missing KEY means no keyed mechanism
access on that side, not that the other side cannot lock the door.

The **loading admin's character receives the configured virtual keys**, persisted
and scoped to each physical door instance and key identity. They do not authorize
another dwelling instance using the same template key. No loose keys, key transfer,
property rights or general security system are introduced. BAR access is installed
hardware and needs no loose bar object.

`OPEN` requires unlocked/unbarred state; `CLOSE` changes OPEN to CLOSED. `LOCK`
requires CLOSED plus accessible KEY and authority; `UNLOCK` changes LOCKED to CLOSED.
`BAR` requires a non-open door and side access; `UNBAR` requires side access.
Unlocking from outside does not remove an inside bar. Commands identify an indoor
door by link trigger or description; ambiguous generic “door” requests ask which
one. Prose uses the configured door description. Commands reserve LOCK/UNLOCK/BAR/
UNBAR against Craft/link collisions. Migration rejects existing conflicting authored
triggers rather than silently repurposing them.

ROOM travel checks passability at departure and again under transaction locks at
arrival. Closing/barring/locking during the delay cancels arrival without stamina
cost. Exterior transfers and door operations also lock shared state. The existing
pending-travel cancellation/restart rules remain unchanged.

### ROOM text additions

```text
LINK: north -> bedroom
DELAY: 1
STAMINA: 0
DOOR: yes
DOOR_ID: bedroom_door
DOOR_DESC: a simple wooden door
KEY: crooked_key
BAR: yes
STATE: CLOSED
BARRED: no
ENDLINK
```

DOOR defaults to no. With DOOR yes, missing DOOR_ID gets a unique local per-link key
and missing description defaults to “a simple wooden door”. Supply the same explicit
DOOR_ID on independently authored opposite sides to share state. Keys follow ROOM
key syntax; `entrance` is reserved. Shared references must connect the same ROOM
pair and agree on initial STATE/BARRED. KEY defaults to none, BAR/BARRED to no and
STATE to CLOSED. Export emits explicit door fields and round-trips them. Initial
state initializes a new physical door; editing an existing graph does not reset its
runtime state. Changing side KEY definitions later does not automatically grant new
keys. No unrelated Object/MOB import system is added.

Migration `20261001_03` adds reusable template JSON, Space source/ground identity,
shared Door rows, side references and virtual key grants. Legacy portals keep their
existing open/closed state and do not acquire unrequested locks/bars. ROOM objects
remain fixed infrastructure: no ordinary taking, moving, consuming, morphing,
deleting or reparenting. BUILD CABIN, multiple entrances, indoor combat, windows,
door damage, vehicles, template upgrades and deployment/publishing systems remain
out of scope.


### GUI template drafts and validation

The Interior Rooms list supports Add, Edit and Remove. Bulk creation makes unused
local keys `room_1`, `room_2`, etc. with names `Room 1`, `Room 2`, etc.; descriptions
start blank. ROOM forms include local key, name, keywords, ground/short descriptions,
weight and full description. Link rows expose trigger, named destination, delay,
stamina, departure and existing shared door/side controls. Applying a ROOM edits
only the unsaved template. Changing its key updates local entry/link references.
Removing a template ROOM unsets entry/incoming destinations that referred to it;
this never deletes a live ROOM. Existing live-instance deletion restrictions remain.

**Save draft** persists unfinished work for later sessions. A draft may have zero
ROOMs, no entry, blank descriptions or incomplete links. Shape, field-size/numeric
bounds, unique valid local ROOM keys and revision protection still apply. It cannot
be instantiated. No parallel graph format exists: GUI and text tools use the same
stored definition, with draft validation followed by the existing complete validator.

**Validate for Use** runs the complete server validator without saving. On success,
**Save usable prototype** persists the definition and usable status together, after
revalidating on the server. Subsequent GUI edits mark the draft unvalidated again;
save it as draft or validate it again. Merely validating without saving does not
change what LOAD may use. Usable retains the existing validator's rules; this does
not introduce a new reachability or mandatory-link rule.

**Import Rooms** opens the optional text review dialog, then applies reviewed ROOMs
to the unsaved draft. **Export Rooms** downloads the current complete ROOM graph,
including unsaved GUI edits; incomplete graphs must be completed before text export.
Use Save draft to retain unfinished work. Complete GUI-authored graphs round-trip
through the existing text format, including local door identities. No UUID entry
or text import is needed for ordinary GUI creation.

The template catalog/API returns `usable`. Saves default to draft unless usable is
explicitly requested and complete validation succeeds. Migration `20261001_04`
preserves previously validated templates as usable and defaults new rows to draft.
Changing a prototype back to draft blocks new LOADs but never alters previously
instantiated dwellings or their ROOMs, links, doors and occupants.

## Configured authoring room limit (2026-10-02)

`dwelling.max_rooms` defaults to 500 and accepts whole numbers 1–500. It takes effect
on server restart. Live graph saves/imports and template draft/save/validate/text
paths use the same server policy. Both editors read limit metadata from the server.
The absolute graph ceiling remains code-owned at 500. Lowering the policy does not
delete existing ROOMs or prevent loading/exporting an already usable saved template;
new or modified authoring must satisfy it. Placement dimensions/certification,
content values and authored link costs remain under their existing authorities.
See the [Phase 4 configuration contract](design.html#runtime-configuration-phase-4-final-existing-system-conversion-2026-10-02).


## Portable Builder blueprints v1 (2026-10-05)

Builder's compact Download menu exports saved **object prototypes**, **craft
definitions**, **dwelling templates**, or **All** as readable UTF-8 JSON. The
format is `ccmud-authored-content`, version `1`, with `exported_at` and arrays
`objects`, `crafts`, and `dwelling_templates`. Category files populate only their
category. Each record has a stable authored `id` and `definition`; templates also
carry `usable`. Optional/default fields are omitted. Property order is irrelevant.
The existing ROOM text tools remain available and unchanged.

This protects reusable blueprints, not deployed structures. A dwelling template
includes its appearance, footprint, entrance controls, entry ROOM, ROOM descriptions,
directed links, costs and authored initial door settings. Edits made solely to a
placed dwelling are not automatically copied back into its template. No live
ROOM graph, exterior identity, coordinates, occupants, possessions, running timer,
virtual key ownership, account, character or Settings data travels in these files.
Saving/importing creates no WorldObjects. Draft templates remain drafts and cannot
LOAD until validated through the ordinary Builder workflow.

Import is Upload → Validate/Preview → explicit approval → one transaction.
Records are NEW, UNCHANGED or CONFLICT, with separate warnings/errors and a field
comparison. Conflicts default to **Keep Existing**. A checked replacement requires
another preview before approval; there is no automatic overwrite. The server
rechecks destination revisions/content and the approved choices. A stale preview
must be refreshed. Source revisions are informational, never destination tokens.
All approved categories succeed together or roll back together. Validation uses
the same Builder rules, including mechanical-edit restrictions on prototypes used
by live objects, morph chains, craft references and complete usable-template checks.

References must resolve in the destination or approved import. A referenced
conflicting object retained with different data blocks the dependent import.
Category exports do not collect dependency packages; use Export All or supply the
required definitions at the destination. Runtime-only and legacy engine prototypes
whose capabilities cannot be faithfully authored through Object Builder are
excluded and explicitly named in file `_notes`, never flattened into incomplete
ordinary objects. Such dependencies must already exist compatibly at the destination.

Normalization is centralized in the portability service, separate from database
schema. Missing optional fields take current model defaults; missing required
fields and future format versions fail. Unknown fields generate warnings and are
ignored. Source revision/actor metadata is ignored with a notice. `_comment` and
`_notes` are accepted as human annotations but are not persisted in gameplay
records; the preview makes this limitation explicit. No historical schema
conversion is invented for this first format version. Future known renames belong
in that normalization boundary rather than in gameplay services.

V1 accepts at most 2 MB and 1,000 records per category. Files are authorized through
the existing Builder boundary. Export reads a consistent saved snapshot. Preview
is read-only, including SQLite; it does not simulate saves by writing and rolling
back. No database migration, destination mapping, live instance import, MOB format,
package manager or synchronization mechanism is introduced.


## Wear physical foundation (2026-10-07; DEV-certified)

Wear uses existing Character-owned WorldObjects, with exactly one hand or worn role.
The nullable `wear_location`, `wear_layer` and `wear_slot` fields are persisted together;
wear_slot is a capacity position, not a second object inventory. Worn quantity is one.
The database preserves exclusive physical placement and unique occupied capacity.
WEAR transfers a held object in place; REMOVE transfers a worn object to the first free
hand. Both-full refusal leaves the worn object unchanged. Embedded REMOVE retains its
existing two-target syntax and precedence. INV lists right/left hands, then worn items
in registry location/layer/capacity order. Worn weight remains carried weight.

The code-owned registry contains head, face, eyes, throat, neck, torso, arms, hands,
waist, legs, feet, back, lfinger, rfinger, lwrist, rwrist, lear, rear, lankle, rankle,
lshoulder, rshoulder, larmband, rarmband, hair, belt1 and belt2. Torso/arms/legs allow
UNDER, BASE, ARMOR and OVER; head allows BASE/ARMOR/OVER; hands BASE/ARMOR; feet
UNDER/BASE/ARMOR; neck BASE/OVER. Remaining locations allow BASE only. Each legal pair
holds one object except neck BASE, which holds three.

The portable prototype's optional `wearable` capability authors `location`, `layer`,
`coverage` (unique registered locations) and `concealable` (boolean). The normal Builder
uses controlled registry choices and the existing save/reload, clone, mechanical-edit
protection and Content Portability paths. The following passes activate visibility
and armor independently. Hand-only mechanics exclude worn objects; containers remain
outside this foundation.

Additive migration `20261007_03` preserves existing placements and rejects existing
Craft/link/template WEAR command conflicts before schema changes. New authoring reserves
WEAR. DEV recovery is fix-forward. Prompt #4 is DEV-certified.


## Equipment visibility (2026-10-08; DEV-certified)

Prompt #5 activates the authored visual coverage and concealability above. For each
worn object, concealment requires both its `concealable` flag and another worn object
at a strictly higher layer whose coverage includes its anchor location. Empty coverage
conceals nothing; occupying an outer anchor alone adds no implicit coverage. Same-layer
items do not hide each other. A concealed garment continues providing authored coverage;
non-concealable objects remain visible. Broader coverage creates no extra physical roles.

Shared Character presentation receives only visible worn descriptions, preserving
existing held descriptions, FDESC, PMOTE and safe conditions. LOOK ME has exactly the
same visibility as another observer's Character LOOK; scene LOOK shares that projection.
INV remains private physical truth, marking hidden worn entries `(concealed)` without
removing them or changing anatomical/layer/capacity order. Held objects are unaffected
by clothing coverage, including worn HANDS equipment.

Current worn state/prototypes are read afresh. Observer-side timed-morph reconciliation
uses the existing lifecycle authority before projection, so coverage/concealability
changes and disappearance become visible even when the wearer has issued no command.
Visibility is neither persisted nor cached. No schema or Builder format change is needed.
Web and Telnet render equivalent server semantic output. Armor, hoods/disguises, identity
obscuration and containers remain outside this visibility pass. Thomas DEV-certified
and accepted Prompt #5. The hat concealment incident was incorrect authored visual
coverage, not a visibility defect.


## Armor foundation (2026-10-08; pending DEV certification)

An optional `armor` ObjectCapability extends the existing portable wearable prototype.
Object Builder provides controlled `armor_class` choices: `NO_ARMOR`, `LEATHER`,
`CHAINMAIL`, `PLATE`. Its `regions` list requires one or more unique values from
`HEAD`, `TORSO`, `ARMS`, `LEGS`. Invalid classes/regions and armor without wearable or
portable configuration are rejected. Save/reload, clone, revision protection and
Content Portability use existing shared validation. Armor changes are mechanical
changes: live instances and pending morph destinations retain their existing protection
against such edits. No separate definition storage or migration is introduced.

These four regions are abstract combat coverage, not wear locations. Armor coverage,
visual coverage, anchor, layer and concealability remain independent. Only currently
worn WorldObjects contribute; held, ground, ROOM, embedded and other Characters'
objects provide no protection to the Character. Concealed armor still protects.

Thomas's overlap clarification is strongest single class per region, without stacking
bonuses, then weakest of all four regions overall. Missing coverage is No Armor.
Plate on HEAD/TORSO/ARMS plus Chainmail on LEGS yields Chainmail overall; three stronger
regions never compensate for the fourth. No Armor gives +1 wound severity, Leather 0,
Chainmail -1 and Plate -2. Preserve MoV -> Base Wound -> Severity Modifier -> Armor Modifier
-> Final Wound, including the existing caps and combination rules. Armor never modifies
hit probability. No averaging, percentages, hit-location rolls, durability, penalties,
shields-as-armor or additional combat mechanics are added.

Regional and overall protection are immutable, disposable runtime snapshots derived
from current worn objects/capabilities. Warm reads avoid equipment queries. Existing
object transactions invalidate cached results after changes, including WEAR/REMOVE and
JUNK. Worn morph deadlines expire cached results; refresh uses existing lifecycle
reconciliation so compatible transitions and disappearance remain authoritative.
Login rebuilds, and server/service restart begins without cached state. Explicit
refresh is available following an external database repair. No derived armor column
or independently authored Character armor value exists.

Existing Character LOOK, LOOK ME, INV and Web/Telnet presentation remain unchanged.
The established wound function accepts the derived modifier; Character combat is not
implemented to demonstrate armor. Automated Stage 1 Wear & Equipment certification
covers authoring/portability, physical roles/capacity, visibility/presentation, armor
and persistence. Final DEV certification and Prompt #6 completion await Thomas.
