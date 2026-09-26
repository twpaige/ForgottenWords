---
title: Publishing & continuity
description: One editable source, automatic browser pages, and reliable local references for development.
reviewed: 2026-09-26
nav: publishing
permalink: /CCMud/publishing.html
---

* Contents
{:toc}

## One source, generated presentation

The Markdown files in `ForgottenWords/CCMud/` are the editable sources for this knowledge base. GitHub Pages runs Jekyll automatically when changes reach `main`, converts Markdown into HTML, applies the shared layout and stylesheet, and publishes the site.

```text
Edit Markdown → commit and push to main → GitHub Pages builds → website updates
```

Nobody needs to edit a matching HTML file. Generated documentation HTML is a deployment output and is not committed alongside the source. The conversion is deterministic formatting, not AI rewriting. Changes saved only locally are not published.

The existing site uses GitHub Pages branch publishing from `main` at repository root. `_config.yml` assigns the CCMud layout only to pages under `CCMud`. Existing tabletop HTML remains static. No game server deployment occurs during this process.

## Source map

| Editable source | Published page |
| --- | --- |
| `CCMud/WORK_START_HERE.md` | [/CCMud/](/CCMud/) |
| `CCMud/STATUS.md` | [Current status](status.html) |
| `CCMud/DESIGN.md` | [Game design](design.html) |
| `CCMud/HOG.md` | [Heart of Gold](hog.html) |
| `CCMud/AGENTS.md` | [Development guide](development.html) |
| `CCMud/PUBLISHING.md` | [Publishing](publishing.html) |

`_layouts/ccmud.html` owns page structure/navigation. `CCMud/assets/docs.css` owns appearance. Page front matter supplies the title, description, review date, navigation selection, and stable permalink. Use ordinary Markdown headings, lists, links, and tables below that metadata. The footer links to the published source revision.

`CCMUD_Design.txt` remains the preserved, authoritative detailed design record at its existing root URL. Its legacy HTML counterpart is not part of this automatic conversion. The Game design page explains how to consult it; do not independently update two supposedly authoritative versions.

## Updating a page

1. Edit its Markdown source and update `reviewed` when the content was actually reviewed.
2. Review the diff and relevant links. Commit and push to the publishing branch through the repository's normal review policy.
3. Check the [Pages build in GitHub Actions](https://github.com/twpaige/ForgottenWords/actions). A successful push alone does not prove publication.
4. Open the published page and confirm the changed content and footer revision.

A failed build leaves the previously deployed site available. Inspect the build error, fix the source/configuration, and publish again. Do not claim the website is current until the build succeeds and the live content is checked. Publication can take a few minutes.

## Local references in private Crown-Call

Crown-Call keeps root `AGENTS.md` and `WORK_START_HERE.md` for local onboarding. Its public-document references are **generated, pinned copies**, refreshed by `scripts/sync_knowledge_base.py`. The source commit and SHA-256 digest for each copy live in `docs/reference/SOURCE.json`.

The existing path `docs/heart-of-gold.md` remains usable as the generated copy of public `CCMud/HOG.md`. `docs/STATUS.md` is the generated current-status copy. Remaining references and the preserved design text live under `docs/reference/`.

After publishing the public documentation, run from Crown-Call with the full public commit hash:

```text
python scripts/sync_knowledge_base.py --revision FULL_PUBLIC_COMMIT_SHA
python scripts/sync_knowledge_base.py --check
```

When a local ForgottenWords clone is available, avoid downloading by adding:

```text
python scripts/sync_knowledge_base.py --revision FULL_PUBLIC_COMMIT_SHA --source ../ForgottenWords
```

The tool reads committed source files at that exact revision, checks for locally modified generated copies, prepares every input before replacing anything, and records provenance. Review and commit the resulting private-repository diff, then push when authorized. It never copies MUD code into the public repository.

Website conversion is automatic. Updating the private reference snapshot is an explicit development completion step; there is no hidden cross-repository mirroring or broad-access publication bot. This makes adoption of a documentation revision deliberate and reviewable.

## Working offline or after an interruption

A fresh Crown-Call checkout has enough pinned documentation to orient itself without another chat or a network connection. Read its source manifest to understand how old that snapshot is. Inspect current code and Git state before relying on STATUS.

The pinned Markdown is readable locally; its browser-oriented links may point to published pages, so use the local file map in WORK_START_HERE for offline navigation. If a generated reference was edited during interrupted work, preserve that edit and reconcile it into the canonical public source before refreshing. The sync tool refuses to overwrite modified copies.

## Public and private boundaries

Development status and MUD design are public with Thomas's authorization. The implementation remains private. Credentials, private source code, server secrets, and unrelated crossover files are never included in this publication pipeline.

Tabletop pages remain a separate project. Shared hosting and colors do not give MUD documentation authority over tabletop rules. Existing root URLs remain available; no old design document is moved or deleted by this installation.

## Completion check

For a documentation change, confirm source diff, successful remote push, successful Pages build, rendered content/links, and updated private reference manifest where needed. For a code change, additionally verify relevant implementation/tests and update the public status/design source as appropriate. Report website publication separately from DEV/PROD game state.
