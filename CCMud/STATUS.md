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
| Inspected and DEV-deployed code revision | `e853131853720b8a2a9f9e81b8ead6f1a1676afb` |
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
- The working server-local `cc-update` implementation is now preserved unchanged at `ops/cc-update` in Crown-Call, with its installation, server contract, behavior, and verification procedure documented in the development guide.

## Active work and next actions

1. Thomas reviews this documentation and the current Step 9 preview when an authorized DEV environment is available.
2. Review coastal character, waterfall density, and unusual landforms before selecting a permanent world seed.
3. Choose the next implementation task explicitly; Step 10 is a roadmap item, not automatic authorization to begin it.

No MUD implementation changes are included in the continuity installation.

## Known issues and limits

- HOG previews are regional/inferred geography, not a persisted walking-scale world. The existing playable terrain service is separate from full HOG integration.
- `WorldFramework` still reports a Step 3 name/default in `worldgen.py`; do not use that field alone to infer overall completion. The later modules and tests establish the actual preview capabilities.
- The preserved design text contains historical progress statements. This page supersedes those statements for current status, without rewriting durable design decisions.
- Existing TTRPG crossover text in Crown-Call needs a separate ownership/cleanup review. Nothing has been moved to public storage during this installation.

## Deferred

Additional zones and zone addressing; persistent HOG integration; player discovery/naming; explorable cave connectivity; walking-scale terrain; procedural transportation. Civilization generation is intentionally skipped for the virgin initial world; the settlement Origin is hand-placed and is distinct from coordinate `(0,0)`.

Upcoming tabletop combat modifications require a separate MUD adoption review. They are not automatically imported.

## Testing state

Verification on 2026-09-26, using Python 3.12 in an isolated local environment:

- Existing application suite at the inspected code baseline: `python -m pytest -q` — **97 passed**, 13 dependency deprecation warnings, 160.94 seconds. This is local regression evidence, not DEV/PROD verification.
- `sudo cc-update dev` at Crown-Call `e853131853720b8a2a9f9e81b8ead6f1a1676afb` — **101 passed**, 13 dependency deprecation warnings, 535.70 seconds; forward migrations completed; the DEV service restarted and passed its readiness check.
- Post-deployment verification: the mirrored `main` revision and active DEV release both identify `e85313185372`; `crown-call-dev` is active/running; local `/health` reports ready, database up, version `0.6.0`; and the installed and tracked updater copies share SHA-256 `6d6ea347199d5df97604f15a75c1bdaa256970c63be5fad93c539381cb1f03f1`.
- New documentation-reference safety tests: **4 passed**. They cover modified-copy protection, failed-fetch preservation, exact-commit adoption, repeatability, and Windows newlines.
- Lint for the new sync tool and its tests: passed. No claim is made about a fresh full-repository lint run.
- Source checks: all six page metadata records and Markdown page links pass; the full existing HOG engineering content is preserved; original design and tabletop pages are unchanged.
- Published-site checks: all six HTTPS documentation pages, navigation, contents anchors, stylesheet, source-revision footer, and legacy reference links pass. Desktop and narrow-screen layouts were visually reviewed. Live legacy/tabletop content matches the preserved repository files.
- Reference integration: the sync tool successfully downloaded and verified all seven pinned public references. This status update also exercises automatic republication from a Markdown-only change.

Historical performance figures in HOG are attributed measurements, not fresh benchmarks. The application suite emitted upstream TestClient/httpx and cookie deprecation warnings; these did not fail tests.

## Environment state

| Environment | Known state |
| --- | --- |
| Documentation | Published independently through ForgottenWords GitHub Pages; the page footer identifies its build revision |
| DEV game | Revision `e85313185372` deployed and verified through the tracked `sudo cc-update dev` workflow on 2026-09-26 |
| PROD game | Remained at revision `96ce97abe6f1`; not modified or revalidated, and no production deployment was authorized |

## Temporary continuation context

After changes to public documentation, publish ForgottenWords first, then refresh Crown-Call's pinned references using the documented synchronization command and commit those references. The source manifest records the exact public revision. A local commit, successful remote push, website publication, and game deployment are separate facts.
