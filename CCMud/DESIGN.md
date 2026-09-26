---
title: Game design
description: The MUD's accepted decisions, preserved in full and separated from tabletop rules.
reviewed: 2026-09-26
nav: design
permalink: /CCMud/design.html
---

## Authoritative design record

The existing **[CCMUD_Design.txt](/CCMUD_Design.txt)** remains the detailed game-design source. It is preserved in full, including its consolidation and numbered decisions. This installation does not migrate, shorten, or reorganize that record.

For the existing illustrated reading edition, see [CCMUD_Design.html](/CCMUD_Design.html). That older HTML is a separate legacy presentation, not an automatically generated version of the text. When wording differs, consult the text and current explicit decisions. The new Markdown-to-HTML publishing system applies to this `/CCMud/` knowledge base; it does not silently convert old pages.

The record begins with consolidated decisions C01–C11, followed by preserved numbered decisions. Read its reconciliation instructions before interpreting older entries. Old questions are historical records and do not automatically reopen a completed design interview.

## Finding a decision

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

Use [HOG](hog.html) for detailed generator architecture and [Status](status.html) for current progress. Historical progress notes in the design text do not establish the current implementation or live environment state.

The existing [world-design page](/CCMud.html) and [design-gap notes](/CCMUD_Holes.txt) remain available for context. Treat older gaps as historical until reconciled with accepted decisions and current code; they do not override the consolidated design record.

## Updating design

Make focused edits to the authoritative record when an approved decision changes. Keep durable rationale beside the decision. Update dependent architecture only where affected, and update STATUS when development state changes. A major restructuring of the legacy design record is a separate task.
