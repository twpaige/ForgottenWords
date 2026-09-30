---
title: Current status
description: Data-driven Craft Engine v1 and a validated web Craft Builder.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

One server-side Craft Engine now executes persisted definitions. GATHER FIREWOOD
and MAKE SPIT resolve exact aliases through the same generic path; their bespoke
material handler is removed. Both require `TREES & !WATER`, output at the player's
feet and add five real minutes of Work Timer debt. Firewood remains one 5 lb bundle;
the spit remains one 8 oz object. MAKE SPIT now requires a held cutting tool, including
the Hunting Knife. No invisible inventory or automatic hand juggling.

Definitions support names/identities, command aliases, physical prototype/quantity
inputs and outputs, held capability requirements, sector expressions, labor cost,
and the v1 ground-at-feet effect. Existing HOG/ROOM perception, reach, placement and
Work Timer authority are reused. Full/partial input consumption, output creation,
attachments, debt, execution revision and optional retry receipt commit atomically.
The same request ID returns the original craft result after restart without another
output, charge, consumption or travel stop; fresh IDs are new work. The web client
supplies IDs. SKIN retains its current bounded behavior and handler.

Builder/admin accounts access `/builder` from the account screen. The form offers
prototype quantities, tool capabilities, aliases, flag/operator controls and labor
cost; all validation and execution remain server-side. Authenticated Builder APIs
and shared validated operations reject invalid references/expressions/quantities,
reserved or conflicting aliases and stale revisions. Saves record/log the editor.
This is the future Object/MOB/CCAI client boundary, not full Object/MOB builders or
CCAI implementation. CCAI receives no shell/database authority.

Flags are derived from HOG/ROOM facts without a second geography map. The parser
supports `!`, `&`, `|`, parentheses and the nine requested names. Multiple flags can
apply. Missing facts remain unknown under NOT, and restrictions must resolve true.
ROAD is registered but unknown until authoritative road geometry exists. Mountain
and rocky labels use the explicitly documented regional biome interpretation;
SHORE only certifies proximity to represented ready water edges. See
[Craft Engine contract](objects.html#19-craft-engine-v1-and-web-builder-2026-09-30)
and [HOG sector sources](hog.html#craft-sector-facts-2026-09-30).

Migration `20260930_06` seeds the two definitions and adds aliases, receipts and the
character craft revision. It preserves existing Work Timer debt, object identities,
quantities and locations, and creates no physical outputs.

## Verification

Implementation: [656b1ba](https://github.com/twpaige/Crown-Call/commit/656b1ba5fdfee7a64aa3f26c1cacc97dbe985163), committed and pushed to main.

- Focused Craft Engine, Builder API and migrated-material run: **119 passed** in
  71.88 seconds. Tests exercise data-driven edits/aliases, all grammar operators,
  simultaneous/unknown flags, real HOG and ROOM facts, capabilities, physical inputs,
  stack quantities, tool identity, outputs, Work Timer, restart, retry receipts,
  rollback, zero-cost revision safety, competing requests/inputs and migration.
- Directly affected gameplay/authentication regressions: **178 passed** in
  145.52 seconds: rabbit SKIN/wounds/THROW, Hunting Knife, player APPROACH, world,
  auth, cabin, odometer and stamina recovery. Each run reports one existing
  dependency deprecation warning. Isolated SQLite only; no live database.
- Final Builder API/shared-operation run: **18 passed** in 22.15 seconds, including
  three additional strict revision-validation cases. Together the named runs cover
  **300 distinct pytest cases**; the final run also checks the last validation/logging
  changes. This is focused coverage, not the full suite.
- Client login/arrival, browser navigation/request-ID/role visibility, and Builder
  forms regressions pass. A real local test API/browser smoke also verified invalid
  expression rejection, create/save/reload persistence, tool display and desktop/
  mobile layouts. Its temporary test server used an isolated database and was stopped.
- Changed-file Ruff, whitespace and single Alembic head checks pass. No full suite
  was run; focused tests and directly affected regressions follow Thomas's request.

## Deployment state and next action

Thomas's last pasted server evidence verified FAST DEV revision
`575b87bb4d4ba3fce66e3ae41e49089078198a1c`: release `575b87bb4d4b`, service active,
health ready, database up, HOG walking certified_origin. Later server revisions
have not been verified by this task. No DEV/PROD command, migration, restart or
deployment was run for Craft Engine v1. Local test-server activity is separate.

After the final documentation pin is pushed, Thomas can deploy that committed
revision and playtest GATHER FIREWOOD, GET KNIFE, MAKE SPIT and LOOK in woodland.
Builder/admin accounts can open Craft Builder from their account screen, edit a
definition, save and execute its alias; no game objects are created just by saving.
The [deployment guide](development.html#cc-update-authority-and-installation) owns
`sudo cc-update dev --fast [ref]` for iteration and `sudo cc-update dev [ref]` for
full verification. No fast PROD. Documentation publication is separate from game
deployment. Future code work should default to focused and directly affected tests;
full-suite testing requires sufficiently broad changes or an explicit request.

## Deferred

Ignition, burning/fuel consumption, roasting, EAT, tanning, butchering, cabin
construction, resource depletion, quality, recipe discovery, skill progression,
a general scripting engine, full Object/MOB Builders and CCAI integration remain
outside v1. No new rabbit/reset or broader combat behavior is added.
