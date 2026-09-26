---
title: Start here
description: A durable home for Crown & Call MUD design, architecture, and development continuity.
reviewed: 2026-09-26
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
| [Heart of Gold](hog.html) | World-generation architecture and engineering constraints |
| [Development](development.html) | Operating instructions, testing, Git safety, and recovery |
| [Publishing](publishing.html) | Markdown sources, automatic HTML publishing, and local reference synchronization |

Each fact has one authoritative home. HTML is generated presentation. Crown-Call's pinned reference documents are generated copies for offline continuity; their source revision is recorded in `docs/reference/SOURCE.json`.

## Starting a fresh session

> Open Crown-Call. Read WORK_START_HERE.md and follow it. Inspect the repository and current status before making changes.

The game runs at [game.crownandcall.com](https://game.crownandcall.com) on DigitalOcean. Documentation publication and game deployment are separate actions. PROD deployment always needs explicit authorization from Thomas.
