---
title: Current status
description: Admin navigation, precision movement and reusable command shortcuts implemented; deployment remains manual.
reviewed: 2026-09-28
nav: status
permalink: /CCMud/status.html
---

## Current bounded phase

Admin Navigation & Testing QoL is implemented on top of the Pioneer Cabin fix
`055d2ae6de34ac2f35d7600e03a797ee86996dde`. Concurrent upstream ruleset-only commits
through `f6b4d8a` were incorporated without conflict.
Implementation commit: `979d0a6b607d5c743aef1ff9494b726ead961b65`.
Fixture correction: `158e51051d71472fe631aca7f0353d97d3104878`. These and the upstream
ruleset changes are pushed at `775affbafdd6fe0fee86bb9f7d8ec052aa7e64dd`.

APPROACH resolves the audited cabin's exterior south entrance through normal travel.
Optional cardinal feet use the existing integrator; bare directions remain continuous.
JUMP retains relative miles and adds signed absolute XY inches with authoritative
ground Z. Ready geography places immediately; cold generation completes automatically
without a repeat command. Cancellation, authorization, certified placement, water and
structure restrictions remain enforced. Prose is not a placement requirement.

Admins receive authoritative coordinates and compact PROSE/RAW/precision controls.
All players have LK/STAT plus four editable Run/Enter shortcuts that retain text
and save per account in the browser. The [field utility contract](hog.html) and
[command reference](commands.html) define units, permissions and cancellation details.

## Verification and next action

Broader focused command, movement, HOG, structure and web checks: **336 passed**,
one dependency warning, **148.50s**. After the final swimming-completion and invalid
confirmed-alias edge fixes, movement/admin-navigation/travel checks: **137 passed**,
one dependency warning, **35.02s**. Full suite: **545 passed, one fixture failure**,
13 dependency warnings, **621.28s**. The old disconnect test stub lacked the new
pending-JUMP state. Its fixture was corrected without changing runtime code; all
**7 prewarm/lifecycle tests then passed in 0.45s**. The full suite was not repeated
after that fixture-only correction.

Headless Edge checks pass against actual client HTML with mocked account/WebSocket
responses: typed/button command parity, roles, authoritative HUD, revocation,
disconnect, reusable CLEAR, shortcut Enter/retention/refresh/account isolation, and
1000px/375px responsive layout. These are isolated tests, not live gameplay verification.
Changed-file Ruff and whitespace checks pass. Repository-wide Ruff has **55 existing
findings**, an exact normalized match to the earlier untouched baseline.

No DEV or PROD deployment, cc-update invocation, or live database migration occurred.
After review, Thomas can manually update DEV to the final pushed private commit, which
also pins this documentation. PROD remains unauthorized. Live revisions were not
inspected. Public website publication is separate from game deployment.

## Preserved baseline and limits

The cabin's reviewed ground attachment, coordinates, collision footprint, door reach,
OPEN/ENTER/EXIT and interior reconnect behavior remain intact. Other structures are
unaudited; this does not add continuous indoor movement or route pathfinding.

Cormac v1.1 prose, verbosity, stable feature identity and RAW diagnostics are unchanged.
Water Movement v1 retains LAND/WADING/SWIMMING/FLOATING, AUTOWADE/AUTOSWIM, ordinary
currents, certification and hazard restrictions. Completing a swimming distance uses
normal floating behavior. Disconnect still stops travel with no offline drift.
Drowning, knockdowns, unsupported natural features and future nine-zone expansion
remain deferred. No generator version, world-unit architecture or migration changed.
