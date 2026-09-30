---
title: Current status
description: Builder cloning, shared nearest-first ordinal targeting and clearer Nearby prose.
reviewed: 2026-09-30
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

The small Builder/targeting usability increment is implemented:

- Craft and Object Builder offer Clone as an unsaved editable draft. The copied
  name becomes `Clone of <name>`, identity is new/editable and revision is zero.
  Save still validates on the server; no source definition is overwritten.
- Nearby and ordinary object/MOB selection share nearest-first ordering with
  compass sector and stable ID ties. Contextual `2.knife`, `3.rabbit`, etc. work
  through shared selection in GET, LOOK inspection, APPROACH, DROP, THROW, REMOVE
  and SKIN. GET excludes held objects and selects eligible ground/ROOM candidates.
- Nearby renders description first: `A Hunting Knife. (here)` or
  `A simple wooden spit. (2 ft E)`, without grouping or a final extra period.

Existing compact Crafts | Objects forms, validated authoring APIs, Craft execution,
physical transfers and lazy linear timed morphs remain in place. No schema change,
new gameplay system or deployment machinery is introduced. See the
[usability contract](objects.html#21-builder-cloning-and-ordinary-target-usability-2026-09-30)
and [Object Builder/morph contract](objects.html#20-object-builder-and-timed-morph-v1-2026-09-30).

## Verification

Implementation revision: [6e82fb8](https://github.com/twpaige/Crown-Call/commit/6e82fb80b30d1e592855bd5d0d35e48939ead332), committed and pushed to main.

- Focused gameplay run: **22 passed** in 60.80 seconds, covering shared ordering,
  ordinals, Nearby prose, ground/ROOM GET and inspection, object/MOB APPROACH through
  travel ticks, THROW/REMOVE ordinals, knife identity/restart and competing GET.
- Final directly affected checks: **3 passed** in 52.32 seconds: SKIN ordinal selection and both Builder APIs
  rejecting revision-zero attempts to overwrite existing definitions (25 total).
- Headless Edge/Playwright checks passed for unsaved Craft/Object clones, copied
  fields, source preservation, new ID collision avoidance, revision-zero saves,
  existing form validation/reload, compact desktop layout and mobile overflow.
- Changed-file Ruff and whitespace checks passed. No full suite was run. Tests use
  isolated databases; the existing Starlette/httpx deprecation warning remains.

## Deployment state and next action

No DEV/PROD deployment, migration or restart was run. Thomas's last pasted
server evidence was FAST DEV `575b87bb4d4ba3fce66e3ae41e49089078198a1c`; no later
live revision is claimed. Documentation publication is separate.

After the documentation pin is pushed, Thomas can deploy that committed revision
for live checks: Clone without Save, save a distinct clone, inspect Nearby order,
and try GET/APPROACH/THROW/REMOVE with numbered targets. The
[deployment guide](development.html#cc-update-authority-and-installation) owns
`sudo cc-update dev --fast [ref]` for iteration and `sudo cc-update dev [ref]` for
full verification. No fast PROD. Development checks remain focused tests and
directly affected regressions unless scope or Thomas requires the full suite.

## Deferred

Wearables, containers, armor, durability, MOB Builder/CCAI clients, repeating/circular
morphs, specialized-state morphs, general scheduling/background cleanup, ignition,
fuel consumption, roasting, EAT, tanning, butchering, cabin construction, broad
resources/depletion, quality, recipe discovery and skill progression remain deferred.
