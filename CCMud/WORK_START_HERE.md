---
title: Start here
description: A durable home for Crown & Call MUD design, architecture, and development continuity.
reviewed: 2026-09-27
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
| [HOG investigation evidence](evidence.html) | Full Step 10 reports, measurements and private reproduction artifacts |
| [Heart of Gold](hog.html) | World-generation architecture and engineering constraints |
| [Development](development.html) | Operating instructions, testing, Git safety, and recovery |
| [Publishing](publishing.html) | Markdown sources, automatic HTML publishing, and local reference synchronization |

Each fact has one authoritative home. HTML is generated presentation. Crown-Call's pinned reference documents are generated copies for offline continuity; their source revision is recorded in `docs/reference/SOURCE.json`.

## Starting a fresh session

> Open Crown-Call. Read WORK_START_HERE.md and follow it. Inspect the repository and current status before making changes.

The game runs at [game.crownandcall.com](https://game.crownandcall.com) on DigitalOcean. Documentation publication and game deployment are separate actions. PROD deployment always needs explicit authorization from Thomas.

For Step 10 efficiency work, read the September 27 conclusions in HOG and the
[evidence index](evidence.html) before proposing another benchmark. The full reports
are version-controlled in private Crown-Call `docs/evidence/hog-step10-efficiency/`.
Distinguish measured findings and settled design from implemented runtime behavior.

## Current bounded HOG release

Read [Wetland Footprint v1](hog.html#wetland-footprint-v1-2026-09-27),
[current status](status.html) and the [release report](https://github.com/twpaige/Crown-Call/blob/3812799921c4fa5ecfaef070fd82fcaf36d861d9/docs/wetland-footprint-v1-release.md) before further
Step 10 work. Ordinary wetlands use a deterministic traversable ellipse; water,
springs and Ravine Reservation Contract v1 remain independent. The prior wetland
study is preserved in private `docs/evidence/wetland-footprint-v1/` and indexed in
[evidence](evidence.html); its earlier algorithm recommendation is superseded.
Thomas deploys DEV separately through the normal Linux gate, then verifies the
reported JUMP and an interior-marsh location. No automatic game deployment occurs.
