---
title: Current status
description: Admin prototype discovery and object/MOB loading.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Admin `OLIST <search>` and `MLIST <search>` now search definitions by stable
prototype ID, name and keywords. Bare commands show usage. Results identify what
can be created, independently of existing instances; no vnums are introduced.

`LOAD O <prototype_id>` / `LOAD OBJECT <prototype_id>` create one new ordinary
persistent OBJECT at the admin's current feet or in the current ROOM. Existing
ground validation, ground attachments, physical invariants and object lifecycle
creation hooks remain authoritative. LOAD does not put the object in a hand.
Specialized corpses still require the death process and cannot be loaded directly.

`LOAD M <prototype_id>` / `LOAD MOB <prototype_id>` use shared MOB/world creation.
The small read-only authored MOB catalog currently contains `rabbit`, preserving
athletics 0, severity modifier +2 and empty wounds. It does not depend on surviving
rabbit instances. Current MOB persistence only supports outdoor placement, so
ROOM MOB loading is refused. There is no new MOB Builder or persistence redesign.

See the [command contract](commands.html#prototype-discovery-and-loading).
The web Builder remains the authoring interface. Existing FIND/JUMP FIND,
player targeting, hands, Crafts and morphs remain unchanged.

## Verification

Implementation: [bf6001b](https://github.com/twpaige/Crown-Call/commit/bf6001b4dcb488adec4aab4e3f3599d3f91a2b31), committed and pushed to main.

- Directly affected tests: **150 passed**, one existing dependency warning,
  166.06 seconds: admin loading, rabbit THROW, MOB wounds, object Builder/morphs,
  Hunting Knife and Craft Builder.
- Final feature tests: **15 passed**, one existing warning, 5.95 seconds. Includes
  permissions, bare usage, aliases, catalog discovery without instances, bounded
  catalog results, literal search, distinct persistent identities, held restart
  state, current feet during travel, ROOM objects, unsupported ROOM MOBs, unknown
  IDs, specialized corpse refusal, ground/structure/cold-placement checks,
  creation-time morph scheduling and rollback on attachment failure.
- Changed-file Ruff and whitespace checks passed. These runs overlap and their
  counts are not additive. No full suite or unrelated browser checks were run.

Tests use isolated SQLite fixtures and real/synthetic HOG providers. PostgreSQL
runtime and live game behavior were not verified in this task. No schema migration
is required. Public command documentation and the private reference pin accompany
this change.

## Deployment state and next action

No DEV/PROD deployment, migration or restart was performed. Live revisions were
not inspected. Thomas controls FAST DEV playtesting and full DEV checkpoints.
After deploying the final pinned revision when desired, try OLIST knife, MLIST
rabbit, LOAD O hunting_knife and LOAD M rabbit, then LOOK and ordinary interactions.
Also verify bare catalog usage and object loading in the cabin ROOM.

Earlier admin FIND/JUMP FIND remains available for actual instances; this task
adds prototype discovery/loading without changing FIND. Parked content publishing,
MOB Builder, ROOM MOB persistence and other deferred gameplay remain deferred.
