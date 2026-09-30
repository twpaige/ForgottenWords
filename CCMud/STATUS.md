---
title: Current status
description: Hunting Knife Slice 1 implementation and verification; no game deployment.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Thomas authorized **Slice 1 — Hunting Knife** on September 30, based on the current
[object architecture](objects.html). The existing WorldObject/prototype/capability
foundation now supports LOOK, player APPROACH, GET/TAKE, INV/INVENTORY/I, and DROP
with persistent right/left hands. No later slice is authorized or implemented.

The migration seeds one knife, ID `00000000-0000-0000-0000-000000000010`, at X=600,
Y=0 inches (50 feet east of Origin), attached to HOG ground. LOOK never spawns it.
GET stops voluntary travel even on failure; APPROACH uses current perception and
normal movement/barriers. Conditional pickup and unique hand occupancy protect
against contested transfers. DROP preserves identity and leaves the knife at the
current feet position without stopping ordinary travel. Held state survives restart.

## Verification

Runtime implementation: [e4dcd3c](https://github.com/twpaige/Crown-Call/commit/e4dcd3cdd5d85a8bd04183e1d6b4d20ffb2d29da).
Verification used that runtime with the accompanying documentation-sync changes
and updated permission assertions. No outstanding test failure remains.

| Check | Result |
| --- | --- |
| `python -m pytest -q` | 663 passed, one stale permission assertion failed, 13 dependency warnings; 736.92 seconds. The assertion still assumed all APPROACH commands were admin-only. |
| Corrected `tests/test_field_commands.py` | All 37 passed; 11.56 seconds. Player object targeting and restricted feature-ID targeting are checked separately. |
| Final `python -m pytest --lf -q` | The sole recorded failure passed after that correction; 1.58 seconds. Together with the full run, all 664 collected cases are verified. |
| Focused knife/navigation/cabin/water/odometer/database suite | 173 passed; 132.91 seconds. Includes independent-connection pickup contention, restart, both hands/full hands, changing barriers, cabin boundaries and refused water placement. |
| Reference synchronization tests | 5 passed, including explicit eight-file adoption and protection of locally modified references. |
| Changed-file Ruff and whitespace | Passed. Repository-wide Ruff retains 55 findings; every affected file is unchanged from the pre-task baseline. |

The new migration and targeted DEV cleanup were exercised only in isolated fixtures.
The original object brainstorm is unchanged; Markdown/source links and review-copy
consistency were checked. Public publication and private reference pinning accompany
this report; no game deployment is included.

Tests use isolated SQLite databases, independent connections, and the real HOG DEV
seed where applicable. No live database or deployed game has been tested. PostgreSQL
runtime contention has not been exercised on this workstation; compare-and-swap
and hand constraints are tested with overlapping independent SQLite transactions.

## Migration and next action

Before any separately authorized DEV migration, audit incompatible legacy held
objects with `python scripts/legacy_hand_cleanup.py`. Explicitly remove only reviewed
obsolete DEV IDs using `--confirm-dev-database --remove-dev-object ID` (repeat for
each ID). The migration refuses remaining legacy holders instead of silently deleting
them or creating invisible inventory. No live audit, cleanup, migration, or deployment
was performed here. The helper is restricted to development environments and refuses
post-migration use. Detailed behavior and syntax are in the object document and README.

**Next action:** Thomas reviews the completed Slice 1 report. Deployment and Slice 2
remain separate decisions. After an authorized future DEV migration, verify LOOK →
APPROACH KNIFE → GET KNIFE → INV → DROP KNIFE, then restart and confirm the same ID.
No `cc-update` or other DEV/PROD deployment is part of this task.

## Limits and preserved behavior

Visibility reads ready certified geometry only, with a 100-foot maximum query radius,
cover, direct terrain sight checks, walls, and unresolved natural-barrier exclusions.
It is separate from the current 15-foot interaction range and is deliberately
conservative, not a comprehensive vegetation/LOS simulation. Unsupported water-object
placements fail without losing the held object. GET uses existing water STOP semantics:
swimming becomes floating; current is not canceled. Unreviewed absolute objects are
not silently moved onto terrain. Admin object inspection is read-only.

HOG generation, Cormac/Eyes geography, cabin doorway and wall authority, water rules,
travel integration, and odometer accounting remain the existing systems. No WIELD,
THROW, containers, rabbits, crafting, fire, food, or universal object infrastructure
was added. The original `CC_Objects.txt` remains unchanged.

The prior regional-lake implementation is `210163fe725861f8e230fcdcd223e07647bc886f`;
its preserved contract and historical verification remain in [HOG](hog.html#regional-lake-resolution-2026-09-29).
Read Crown-Call's WORK_START_HERE and current public CC_Objects.md to continue;
Project chat context is not needed. Public documentation publication, private reference
synchronization, and game deployment are reported separately.
