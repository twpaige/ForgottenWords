---
title: Current status
description: Admin persistent instance FIND, paging, and identity-based JUMP FIND.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Admin `FIND O <search>` and `FIND M <search>` search actual persistent instances,
including cold/unloaded areas. Database queries resolve implemented physical
parents, sort by horizontal map distance and identity, and return bounded pages
of 20 results. ENTER continues; Q retains displayed identities. New FIND replaces
the set; disconnect clears it. This is separate from definition catalogs.

`JUMP FIND <number>` re-resolves the exact displayed identity and its current
physical location, including held and embedded items. It leaves the target intact.
Outdoor destinations retain existing JUMP safety checks; cold preparation also
re-resolves the target before placement. Supported ROOM destinations use the
existing certified interior anchor, currently the audited Pioneer Cabin. Unknown
interiors remain refused. See the [command contract](commands.html#find-instances-and-jump-to-results).

The existing Builder cloning, shared player ordinals, Crafts, object morphs,
physical hands and gameplay remain in place. No migration, vnums, spawning system,
HOG redesign or content-publishing mechanism was added. Root AGENTS.md now points
to CC_Work_Optimization.txt for future sessions.

## Verification

Implementation: [25883db](https://github.com/twpaige/Crown-Call/commit/25883dbe9c064d0d732b90bbe0f2e01aa3446dea), committed and pushed to main.

- Directly affected regression run: **124 passed**, one existing dependency warning,
  127.89 seconds. Covered FIND, admin navigation, field commands, cabin, targeting,
  WebSocket and Craft Builder tests.
- Final FIND/Builder run: **35 passed**, one existing warning, 59.33 seconds.
  Includes 1,005-instance bounded paging without ORM instance loads or geography
  access, displayed result 27 after Q, cold search, held/ROOM/nested/embedded MOB
  locations, deterministic ordering, moved/deleted targets, queued re-resolution,
  permissions, cancellation, replacement, and reservation of FIND against Craft aliases.
- Final ROOM/pager checks after the last small state fixes: **2 passed**, one existing
  warning, 23.75 seconds. These runs overlap; their counts are not additive.
- Headless Edge browser command/pager, navigation, role, HUD and layout checks passed;
  client login/arrival regressions passed. Empty ENTER is sent only during paging.
- Changed-file Ruff and whitespace checks passed. No full suite was run.

Tests use isolated SQLite fixtures and real/synthetic HOG providers. PostgreSQL
runtime execution and live game behavior have not been verified in this task.
Public command documentation and the private reference pin accompany the change.

## Deployment state and next action

No DEV/PROD deployment, migration or restart was performed. Current live revisions
were not inspected. Thomas controls FAST DEV playtesting and full DEV checkpoints.
Next: deploy the final pushed documentation-pin revision when desired, then try
FIND O knife, ENTER, Q, JUMP FIND 27, and FIND M rabbit, including a held/ROOM target.

Search pages are live queries rather than a frozen world snapshot; objects moving
across the cursor may require a fresh FIND. Only displayed identities are retained,
and jumps always re-resolve them. Unsupported/cyclic physical paths do not receive
invented locations. Existing deferred object/content systems remain deferred.
