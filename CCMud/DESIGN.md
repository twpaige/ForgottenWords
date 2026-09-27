---
title: Game design
description: The MUD's accepted decisions, preserved in full and separated from tabletop rules.
reviewed: 2026-09-27
nav: design
permalink: /CCMud/design.html
---

## Authoritative design record

The existing **[CCMUD_Design.txt](/CCMUD_Design.txt)** remains the detailed game-design source. It is preserved in full, including its consolidation and numbered decisions. This installation does not migrate, shorten, or reorganize that record.

For the existing illustrated reading edition, see [CCMUD_Design.html](/CCMUD_Design.html). That older HTML is a separate legacy presentation, not an automatically generated version of the text. When wording differs, consult the text and current explicit decisions. The new Markdown-to-HTML publishing system applies to this `/CCMud/` knowledge base; it does not silently convert old pages.

The record begins with consolidated decisions C01–C11, followed by preserved numbered decisions. Read its reconciliation instructions before interpreting older entries. Old questions are historical records and do not automatically reopen a completed design interview.

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
certified distant LOS/player discovery remains separate. Inferred local channel and
lake footprints may bound certified dry exterior ground, but unknown water depth,
bed/bank geometry and crossing consequences remain blocked. No complete swimming or
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
