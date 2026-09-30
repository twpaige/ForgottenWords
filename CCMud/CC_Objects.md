---
title: CCMUD Object Architecture — Working Draft 5
description: A bounded object architecture reconciled with approved gameplay decisions and the existing implementation.
reviewed: 2026-09-30
nav: design
permalink: /CCMud/objects.html
---

# CCMUD Object Architecture

**Working Draft 5 — Hunting Knife Slice 1 and the bounded Rabbit MOB / THROW / REMOVE increment are authorized. Other slices and game deployment remain separate decisions.**

This document condenses the historical object exploration into the smallest persistent object foundation needed for a playable Alpha. It strengthens the existing implementation and defines six bounded slices. It is not a mandate to build every system mentioned in the exploration.

**LOCKED** identifies approved decisions or preserved governing constraints. **PROVISIONAL** identifies recommendations and implementation details still subject to review. **DEFERRED** means outside these slices. **REJECTED** identifies excluded approaches. The latest explicit decisions recorded here supersede conflicting earlier draft policies and historical design alternatives. Architecture approval, implementation authorization, documentation publication, and deployment remain separate steps.

The September 30 Rabbit increment in section 15 supersedes earlier stop lines only for its explicit scope. MOB (mobile) is CCMUD terminology for an NPC, including wildlife.

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

The editable source is `ForgottenWords/CCMud/CC_Objects.md`. The September 30 instructions authorize Hunting Knife Slice 1 and the bounded Rabbit MOB / THROW / REMOVE hit test in section 15, including commit/push. Other slices and game deployment remain separate decisions. Its explicitly approved decisions govern this design work. The original private `CC_Objects.txt` remains unchanged. Implementation links require private repository access and identify evidence without publishing implementation code.

## 2. Terminology and boundaries

**LOCKED:** use these gameplay categories; they do not require one universal database table or base class.

| Category | Meaning |
| --- | --- |
| **PC** | Player-controlled character. |
| **MOB (mobile)** | All non-player characters, including humans and wildlife such as rabbits, deer, and horses. |
| **OBJECT** | Manipulable physical things: knives, rifles, backpacks, furniture, food, packed tents. |
| **DWELLING** | Enterable habitation: cabins, houses, pitched tents. |
| **VEHICLE** | Wagons, carriages, sleds, boats, ships. |
| **STRUCTURE** | Fences, walls, gates, bridges, docks, and similar built features. |
| **ROOM/SPACE** | Spatial or interior context; a DWELLING interior uses traditional MUD ROOM semantics. |
| **HOG GEOGRAPHY** | Natural geography under HOG authority. |

ENTITY is removed as CCMUD's formal umbrella taxonomy. Existing implementation names such as `NearbyEntity` are evidence about code, not a requirement to retain that taxonomy. A category describes gameplay behavior; it does not require replacing working character, cabin, or object storage.

**TEMPLATE** means the existing `ObjectPrototype` definition. A **capability** is supported behavior/configuration, represented by `ObjectCapability` where appropriate. A **physical parent** determines a possession's current location. **Effective location** is the outdoor placement or ROOM reached by following that parent chain. A portal connects spatial contexts; a door controls access. Their conceptual distinction does not require splitting the current cabin's `Portal` record.

Preserve these governing boundaries:

- CCMUD owns gameplay and commands; the web interface is a client. Preserve client-agnostic text interaction. This does not claim a Telnet listener exists.
- HOG owns geography. Objects overlay it; forests do not require one OBJECT per tree or terrain cell. Extracted resources can become individual OBJECTS without transferring authority over the source landscape.
- Cormac and Eyes present supplied facts. Perception neither creates world truth nor grants movement permission. Preserve useful admin RAW diagnostics.
- Outdoors uses signed 64-bit integer inches: positive X east, positive Y north, Z elevation. Preserve PostgreSQL, deliberate Alembic migrations, travel accounting, and ground-placement authority.
- Adopted Crown's Calling rules remain above object storage. Future CCAI uses restricted, validated operations, not unrestricted database or shell authority.
- Slice 1 and the bounded section 15 hit test are authorized for implementation and publication. Live cleanup and DEV/PROD deployment have not been performed. Later slices need a separate instruction.

## 3. Existing implementation and required changes

**LOCKED:** evolve `WorldObject`, `ObjectPrototype`, and `ObjectCapability`. Do not create a parallel object foundation.

| Existing evidence | Required integration |
| --- | --- |
| `WorldObject` has a UUID, prototype, positive quantity, coordinates or character holder, and optional interior space. | Preserve IDs; strengthen location exclusivity and add minimal handling state. Add containment only for the backpack slice. |
| `Character` stores coordinates, chunks, and `space_id`; runtime `TravelState` and `PositionedCharacter` serve existing movement/session contracts. | Resolve carried objects through this authority. No shared `Position` class was found or needs to be invented first. |
| GET/TAKE, DROP, and I/INVENTORY already dispatch to persistent handlers. Outdoor HOG interactions remain gated. | Document GET as canonical, retain TAKE, add INV, and integrate a bounded supported outdoor path. Existing dispatch is not proof that outdoor GET works. |
| `_get` checks ground candidates and portability, then changes holder and clears placement. Requests serialize per character, not per shared object. | Protect competing GETs across different PCs and validate destination hands atomically. |
| Holder/coordinate exclusivity does not fully exclude held `space_id`; there is no container parent yet. | Enforce the complete current-location invariant in storage and domain operations. |
| Ground queries filter by space and three-dimensional radius; the matcher returns the first match. | ROOM interaction must bypass intra-room distance. Resolve ambiguous targets explicitly. |
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

The cabin remains `pioneer_cabin`. Its seeded exterior/interior portal coordinates are `(0,1200,0)` and `(0,1260,0)`, with actual HOG ground attachment resolving height. These existing storage conventions can remain for compatibility; they must not impose intra-room object reach or walking. Interior placement means ROOM membership, not outdoor terrain sampling. Do not require a coordinate migration merely to provide the approved gameplay semantics.

Preserve the audited exterior doorway, wall footprint, terrain attachment, and rejection of unsupported configurations. ENTER crosses the real doorway and clears outdoor travel. Inside, reaching accessible contents or the exit does not require coordinate walking. EXIT still performs the authoritative transition to the exterior doorway, respecting door access. Existing interior radius/reach checks must be reconciled with this rule, not preserved as hidden restrictions.

Held possessions follow the PC through ENTER/EXIT. A backpack dropped indoors becomes directly placed in that ROOM; its children remain parented to it. Leaving the ROOM moves the PC, not the dropped backpack. Existing `Space`/`Portal` storage can support this integration without creating separate cabin and door OBJECTS.

**LOCKED DROP:** outdoors, leave the item at the dropper's feet on the supported surface in the current SPACE, using authoritative position at the transfer boundary. Indoors, leave it in the current ROOM; flavor text may say “at your feet” without creating an intra-room positioning requirement. DROP itself does not stop continuous travel. Unsupported outdoor placement, including unsupported water-object contexts, fails with the item still held.

**LOCKED collision rule:** ordinary PCs, MOBs, OBJECTS, and VEHICLES are not tactical movement obstacles. A knife, pile of small items, chair, horse, crowd, or wagon creates no positioning puzzle, automatic stop, or avoidance route. Do not add a generic OBJECT collision toggle or a numeric small-item cutoff. Real barriers come from DWELLINGS, STRUCTURES, and HOG geography where appropriate: cabin walls and fences can block. The rule abstracts maneuvering around ordinary occupants and items; it does not bypass a wall.

VEHICLES are a distinct future access case. Exposed, reachable contents may eventually be manipulated from outside; do not automatically give them the DWELLING interaction boundary. Nested containers still enforce their own access rules. Vehicle implementation remains deferred.

## 6. GET, APPROACH, perception, and travel

**LOCKED:** GET is the canonical documented command; TAKE remains an alias. Outdoors GET never moves the PC. It requires a currently valid target, interaction range, portability, access, and an available hand. A previously observed target must be revalidated at action time.

**LOCKED player APPROACH:** `APPROACH <object|mob>` moves toward a perceptible OBJECT or MOB and stops at its appropriate interaction range. The outdoor target must already be perceptible and in direct line of sight. Move directly toward it and stop at interaction range. APPROACH does not pathfind around walls, fences, or buildings and does not automatically GET the target. PCs, MOBs, OBJECTS, and VEHICLES do not obstruct this movement; DWELLINGS, STRUCTURES, and HOG geography may block it. A known ID, coordinate, or admin reference is not player perception.

Use current movement and geometry authority for the supported path. Revalidate the target and path during movement; stop safely if the target is lost or the permitted direct path becomes blocked. Do not route around an obstruction or expand this into general navigation. Indoors APPROACH is unnecessary for an accessible object in the same ROOM. A target that moves stops this bounded APPROACH; no chasing is added. Ordinary arrival says "You approach the Hunting Knife and stop." or "You approach the rabbit and stop." Diagnostic geographic wording about represented targets, certification or inches is not player arrival prose.

**LOCKED:** GET issued during continuous travel first stops that travel, then attempts the interaction. It never automatically resumes, whether the object is obtained, out of range, inaccessible, ambiguous, missing, or rejected because hands are full. TAKE follows the same handler. Stopping is an intentional command effect even when the object transfer fails; therefore “failed transfer changes nothing” applies to object/possession state, not to the separately required travel stop. Preserve accepted movement and odometer accounting up to the stop boundary. The normal command loop must not advance the old travel state afterward.

**LOCKED:** visibility and interaction range are separate. Draft 4's fixed 15-foot visibility policy is superseded. The current pickup configuration of 180 inches is implementation evidence about interaction range, not an approved visibility ceiling. A knife may be visible 50 feet away on a road but invisible two feet away in tall grass. Exact object visibility tuning comes later; neither distance alone nor a known coordinate proves perception.

Object observation follows this flow:

```text
authoritative current facts → bounded candidates in the correct SPACE
                            → perception/access checks → player presentation
```

Keep dynamic candidates outside cached geographic prose. Cormac should receive facts through the observation seam, not query the database or generate objects during LOOK. Reuse outdoor Nearby's compass ordering, nearest-first ordering, and deterministic ties. Namespace mixed candidate references because the current formatter deduplicates by ID. Bound dense result sets as well as spatial searches.

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

**The six-slice plan remains; authorized implementation is Slice 1 plus the explicit bounded section 15 exception.** Each slice closes the relevant integrity and restart tests before expanding scope. Slice 3 remains even if its final change is small.

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

**No further foundational decision is identified as necessary for Slice 1.** Thomas explicitly authorized that implementation on September 30; other slices remain outside the task except for the bounded section 15 authorization. The approved policies above are locked; exact schema/locking choices, parser wording, supported initial perception parameters, and targeted DEV cleanup records are implementation preparation rather than new architecture questions.

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

Thomas authorized a small playable increment after Slice 1, then explicitly deferred
wounds and killing: resolve whether the throw hits. He subsequently authorized
`REMOVE KNIFE RABBIT`. This is not the complete hunt or a new combat system.

- A migration authors one persistent rabbit MOB at X=840, Y=0 inches (70 feet east
  of Origin), on supported HOG ground. Its test ATHLX is 0. LOOK and restart never
  spawn replacements. Movement AI, concealment behavior, wounds, bleeding, death,
  corpse processing, SKIN, fire, cooking and food remain deferred.
- `THROW <object> <target>` requires a single throwable object in either physical
  hand. Holding is sufficient; WIELD is not required. A wielded object remaining
  in a hand qualifies by the same predicate. No WIELD command or equipment system
  is added. The Hunting Knife's existing prototype gains a throwable capability.
- Use adopted C05/R1/R2/R4/W1a/W11 hit resolution: attacker d20 + RANGE + the
  knife's one-hand modifier (+2), minus one per complete ten feet, opposed by
  d20 + the MOB's ATHLX dodge. Ranged attacks require at least ten feet. Higher
  total wins; a tie does not hit. No Aggression or armor modifies hit probability.
  RANGE is persisted and defaults to ordinary competence, 0, for this test increment.
- Wound ceilings versus newer tabletop severity modifiers are deliberately not
  resolved or applied here: this increment records hit/miss only. No full encounter,
  initiative, turn scheduler, repeated attack loop, rabbit behavior or damage result
  is claimed. Each explicit THROW resolves one opposed attempt.
- Targets must pass the existing bounded, ready HOG ground/visibility/barrier checks.
  Cabin walls, interior boundaries, unavailable geometry and unsupported water remain
  barriers. Both THROW and REMOVE stop voluntary travel through existing STOP behavior;
  they never close distance automatically or resume movement afterward.
- A miss leaves the same knife on supported ground at the target's feet. This bounded
  landing rule adds no scatter or flight simulation. A hit transfers the same knife
  to an embedded-MOB location with no independent coordinates or hand. LOOK describes
  the rabbit with the embedded knife, without claiming injury severity or death.
- `REMOVE <object> <target>` requires the visible MOB within the existing 15-foot
  interaction range. Move closer with ordinary travel if needed. It uses the first
  free hand (right, then left); full hands fail without dropping or moving anything.
  Removal is a conditional transfer of the original object, not a replacement knife.
  GET does not retrieve embedded objects. Restart preserves both embedded and removed state.
- The object's exclusive location constraint now covers ground, character hand, or
  embedded MOB. Competing transfers cannot duplicate the knife. Deleting a referenced
  MOB cannot cascade-delete its embedded possessions.

The previous Slice 1 bug fix, `3af197f`, shares controller attachment between DEV
startup and construction. Its travel-tick regression preserves HOG generation checks.
It also supplies immediate connection feedback and protects character entry against
competing sockets and stale callbacks. No game deployment is implied by these commits.

## 16. Source map for deeper review

The historical exploration remains [CC_Objects.txt](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/CC_Objects.txt). Its useful clusters are: identity/location and boundaries (19–25, 221–239, 601–640); storage and spaces (721–780, 1561–1640); inventory alternatives and handling (781–860); durability (961–1000, 2051–2054); concrete slices (1121–1260); templates (1941–1980); convergence and the first-slice gate (1981–2080). These are evidence and alternatives, not 2,080 approved requirements.

For governing gameplay, consult the [preserved design](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMUD_Design.txt): C01–C02, C09–C11, Q86–92, Q114–130, and Q146–148. Its earlier world-time values are superseded by its dated 1.75× decision; implementation remains separately evidenced.

For current boundaries, consult [HOG](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/HOG.md), [commands](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/COMMANDS.md), and [publishing](https://github.com/twpaige/ForgottenWords/blob/ea2c03d7a93f0458a9c8e4a28bb35fc135e742af/CCMud/PUBLISHING.md). The current command reference records GET/TAKE and INV/INVENTORY/I as implemented, with outdoor object support bounded by Slice 1.

For implementation evidence, also consult [cabin attachment](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/hog_structures.py), [ground placement](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/hog_placement.py), [Nearby formatting](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/src/crown_call/cormac.py), [world tests](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/tests/test_world.py), and [HOG cabin tests](https://github.com/twpaige/Crown-Call/blob/e3491580ab952874f46cf9716a33f1bd0934e6f3/tests/test_hog_cabin.py).
