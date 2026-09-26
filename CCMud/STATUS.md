---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

This is a current snapshot, not a session log. Replace stale entries as work progresses. Code and executed checks must be consulted before treating this snapshot as current.

## Verified baseline

| Item | Evidence |
| --- | --- |
| Code repository | Private `twpaige/Crown-Call`, branch `main` |
| Inspected code revision | `a6b2fc76dfa27f38eaf623034c98dd6f67c3975e` |
| Latest implementation | PR #54: Step 9 deterministic natural features, implementation commit `548e092` |
| Application package | Version `0.6.0` in `pyproject.toml` |
| Documentation baseline | ForgottenWords `06e825c2c286a0cb4a405da552d284576588ddb5` plus this continuity installation |

## Current phase

**HOG Step 9 — natural features is implemented in the admin preview for Zone C.** This establishes implementation state, not live deployment or completed visual acceptance. Step 10 world integration and persistence remain future work.

## Last completed

- Regional geography, elevation, hydrology, climate, vegetation, and geology preview layers exist.
- Step 4B local hydrology includes selective terrain-following channels and wet-region pond/headwater density tuning.
- Step 9 provides bounded, lazy natural-feature catalogs, admin queries, viewer overlays, and regression coverage.
- This documentation system establishes public Markdown sources, automatic styled HTML, operating rules, and pinned local references for Crown-Call.

## Active work and next actions

1. Thomas reviews this documentation and the current Step 9 preview when an authorized DEV environment is available.
2. Review coastal character, waterfall density, and unusual landforms before selecting a permanent world seed.
3. Choose the next implementation task explicitly; Step 10 is a roadmap item, not automatic authorization to begin it.

No MUD implementation changes are included in the continuity installation.

## Known issues and limits

- HOG previews are regional/inferred geography, not a persisted walking-scale world. The existing playable terrain service is separate from full HOG integration.
- `WorldFramework` still reports a Step 3 name/default in `worldgen.py`; do not use that field alone to infer overall completion. The later modules and tests establish the actual preview capabilities.
- The preserved design text contains historical progress statements. This page supersedes those statements for current status, without rewriting durable design decisions.
- No versioned deployment procedure or `cc-update` implementation was found in the inspected checkout. The reported command `sudo cc-update dev` requires server-side verification before use.
- Existing TTRPG crossover text in Crown-Call needs a separate ownership/cleanup review. Nothing has been moved to public storage during this installation.

## Deferred

Additional zones and zone addressing; persistent HOG integration; player discovery/naming; explorable cave connectivity; walking-scale terrain; procedural transportation. Civilization generation is intentionally skipped for the virgin initial world; the settlement Origin is hand-placed and is distinct from coordinate `(0,0)`.

Upcoming tabletop combat modifications require a separate MUD adoption review. They are not automatically imported.

## Testing state

Verification on 2026-09-26, using Python 3.12 in an isolated local environment:

- Existing application suite at the inspected code baseline: `python -m pytest -q` — **97 passed**, 13 dependency deprecation warnings, 160.94 seconds. This is local regression evidence, not DEV/PROD verification.
- New documentation-reference safety tests: **4 passed**. They cover modified-copy protection, failed-fetch preservation, exact-commit adoption, repeatability, and Windows newlines.
- Lint for the new sync tool and its tests: passed. No claim is made about a fresh full-repository lint run.
- Source checks: all six page metadata records and Markdown page links pass; the full existing HOG engineering content is preserved; original design and tabletop pages are unchanged.

Historical performance figures in HOG are attributed measurements, not fresh benchmarks. The application suite emitted upstream TestClient/httpx and cookie deprecation warnings; these did not fail tests.

## Environment state

| Environment | Known state |
| --- | --- |
| Documentation | Published independently through ForgottenWords GitHub Pages; the page footer identifies its build revision |
| DEV game | Deployed revision and live validation not checked in this documentation task |
| PROD game | Thomas identifies `game.crownandcall.com` on DigitalOcean; deployed revision not checked and no game deployment authorized |

## Temporary continuation context

After changes to public documentation, publish ForgottenWords first, then refresh Crown-Call's pinned references using the documented synchronization command and commit those references. The source manifest records the exact public revision. A local commit, successful remote push, website publication, and game deployment are separate facts.
