---
title: Start here
description: A durable home for Crown & Call MUD design, architecture, and development continuity.
reviewed: 2026-10-04
nav: home
permalink: /CCMud/
---

**Crown & Call MUD (CCMUD)** is a persistent, coordinate-based multiplayer world. **Heart of Gold (HOG)** generates its physical geography. This knowledge base carries project decisions between development sessions; previous conversations are not required to resume work.

## Two projects, a clear boundary

**C&C TTRPG** is the tabletop game. Its definitive rules live in the public ForgottenWords repository. **C&C MUD** is a separate software project that draws inspiration and selected rules from the TTRPG. Its code remains in private Crown-Call.

The `/CCMud/` section belongs exclusively to the MUD. HOG and MUD operating rules do not govern the tabletop project. A tabletop rule change becomes a MUD change only after deliberate adoption; record MUD-specific adaptations in the MUD design documentation.

## Before beginning work

1. Open private Crown-Call and read its root `AGENTS.md` and `WORK_START_HERE.md`.
2. Read the [development guide](development.html) and [current status](status.html).
3. Inspect repository identity, branch, remote, uncommitted changes, and relevant recent commits.
4. Read the [game-design reference](design.html), [HOG architecture](hog.html), and implementation/tests relevant to the task.
5. Establish what exists before changing anything. If work appears interrupted, follow the recovery procedure in the development guide.

## Where information belongs

| Reference | Responsibility |
| --- | --- |
| [Current status](status.html) | Present development state, blockers, next actions, and verification |
| [Game design](design.html) | Detailed MUD decisions and the preserved design record |
| [Object architecture — Working Draft 5](objects.html) | Approved object decisions, Hunting Knife and bounded rabbit wounds/corpse/SKIN and Craft/Object Builders with timed morphs; later slices and deployment require authorization |
| [Commands](commands.html) | Current commands and reviewed Shadows/SOI command adoption decisions |
| [HOG investigation evidence](evidence.html) | Full Step 10 reports, measurements and private reproduction artifacts |
| [Heart of Gold](hog.html) | World-generation architecture and engineering constraints |
| [Development](development.html) | Operating instructions, testing, Git safety, and recovery |
| [Publishing](publishing.html) | Markdown sources, automatic HTML publishing, and local reference synchronization |

Each fact has one authoritative home. HTML is generated presentation. Crown-Call's pinned reference documents are generated copies for offline continuity; their source revision is recorded in `docs/reference/SOURCE.json`.

## Starting a fresh session

For HOG, start with the
[accepted terrain/water architecture](hog.html#accepted-terrain-and-water-architecture-2026-10-04):
**DESIGN ACCEPTED / FROZEN FOR PRODUCTION PLANNING**. Validation is safely committed
through private Crown-Call `7bf9abc`; the prototype remains offline and non-authoritative.
The next task is **HOG Production Integration — Stage 1: Implementation Plan**.
Do not resume discarded reconciliation/drainage-first experiments or infer production
implementation/deployment approval from the architecture freeze.

> Open Crown-Call. Read WORK_START_HERE.md and follow it. Inspect the repository and current status before making changes.

The game runs at [game.crownandcall.com](https://game.crownandcall.com) on DigitalOcean. Documentation publication and game deployment are separate actions. PROD deployment always needs explicit authorization from Thomas.

For Step 10 efficiency work, read the September 27 conclusions in HOG and the
[evidence index](evidence.html) before proposing another benchmark. The full reports
are version-controlled in private Crown-Call `docs/evidence/hog-step10-efficiency/`.
Distinguish measured findings and settled design from implemented runtime behavior.

## Current bounded HOG release

Ordinary wilderness LOOK now uses [Cormac v1](hog.html#cormac-v1-2026-09-28), with stable titles and BRIEF/NORMAL/MAXIMUM session verbosity. Admin RAW remains separate.

Read [Water Movement v1](hog.html#water-movement-v1-2026-09-28) and [Status](status.html).
The server now implements explicit WADE/SWIM/FLOAT modes, persisted SET settings,
current displacement, STATUS and deliberate ENTER-water behavior. Ordinary <=3-foot
water walking is superseded. Geometry remains deterministic and certification remains
separate from movement permission. Independent terrain/structure hazards stay gated.
Thomas reviews the implementation report before manually deploying DEV. No DEV or
PROD deployment is part of this task. The geometry and prior release reports remain
historical evidence; do not treat their three-foot rule as current gameplay policy.


## Bounded rabbit interaction (2026-09-30)

After Hunting Knife Slice 1, Thomas authorized Rabbit MOB + THROW, REMOVE,
wounds/death/corpse, and then SKIN into carcass/raw pelt/guts. MOB (mobile) is
CCMUD's NPC terminology. Rabbit severity is +2 and MORT means immediate animal
death; ordinary character rules are unchanged. SKIN consumes an accessible held
or ground corpse with a cutting tool held, placing all outputs at the character's
feet. MAKE SPIT and GATHER FIREWOOD now provide ground objects from HOG woodland, through the data-driven Craft Engine, with sector expressions and real-time Work Timer accounting; MAKE SPIT requires a held cutting tool. Web Builder/API is the content-authoring boundary. Read sections 15-19 of [the object contract](objects.html) and current status.
No cooking, broader resource system or game deployment is authorized.
