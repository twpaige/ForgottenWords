---
title: Development guide
description: How to work safely, verify changes, and leave enough context for the next session.
reviewed: 2026-09-26
nav: development
permalink: /CCMud/development.html
---

These instructions apply to CCMUD. They do not govern tabletop rules elsewhere in ForgottenWords. Crown-Call's root instructions add repository-specific entry points; this document is the canonical shared MUD workflow.

* Contents
{:toc}

## Start with evidence

Read WORK_START_HERE, STATUS, and task-relevant design. Verify repository identity, current branch, remote, working-tree changes, and recent relevant commits. Inspect implementation and tests before assuming a feature is absent or replacing an existing system.

Preserve intentional behavior. Prefer focused changes. Investigate failures and make routine implementation choices from clear requirements. Bring genuine unresolved design choices or destructive ambiguities to Thomas after completing independent work that can safely proceed.

## Authority and project boundaries

Thomas's current explicit decisions govern the task. Subject-specific design documents establish intended behavior; code and executed tests establish implemented behavior. STATUS describes present development state. Git history and old conversations are supporting evidence, not a substitute for maintained documentation.

Investigate disagreements. Clearly stale documentation may be corrected when evidence is unambiguous. Do not silently redesign behavior to make documents agree. Update the authoritative decision when Thomas changes it.

ForgottenWords owns tabletop rules and the public CCMUD knowledge base in separate areas. Crown-Call owns private MUD implementation. Do not insert HOG or MUD operations into tabletop documentation. Do not import TTRPG rule changes automatically. Do not publish private code or move crossover text to the public repository without authorization for that material.

## Working on HOG

Follow [HOG architecture](hog.html). Preserve deterministic output for fixed seed/configuration/generator versions unless a deliberate generator change is approved. Respect coordinate units, zone bounds, bounded lazy generation, stable feature identity, cache limits, and zoom-independent geography. Preserve reviewed layers when adding derived layers.

Do not invent walking-scale precision from coarse grids. Maintain compatibility and record generator-version or persistence implications when output changes. Future nine-zone design does not mean all zones are currently implemented.

## Development and tests

In Crown-Call, `README-APP.md` owns local application setup. The project requires Python 3.12 or newer. From a configured development environment:

```text
python -m pip install -e ".[dev]"
python -m pytest
python -m ruff check .
```

Run relevant tests incrementally during substantial work. The full suite is appropriate for cross-cutting behavior; smaller focused suites can validate narrow changes. Existing lint failures must be distinguished from regressions. For documentation-only work, verify links, source claims, rendering, publishing, and unchanged application scope; do not invent tests that merely restate prose.

Never claim a test ran when it did not. Report command, result, revision/working state, and limitations. Test fixtures include isolated SQLite and mocked services; do not point test runs at live databases. PostgreSQL setup and `alembic upgrade head` are local development steps in README-APP, not authorization to migrate DEV or PROD.

Database changes require deliberate migrations and tests for affected persistence behavior. Review data-loss and compatibility implications before applying migrations; never casually change live data or production configuration.

## Git safety

- Confirm the correct repository, branch, remote, and upstream before writing or pushing.
- Unknown changes are evidence, not garbage. Inspect tracked changes, staged changes, and untracked files before altering them.
- Preserve unrelated work; stage only the task's intended changes. Never reset or clean away unfamiliar work to simplify a task.
- Never force-push or rewrite shared history without explicit authorization.
- Keep secrets out of commits, logs, public documentation, and reports. Runtime credentials belong in the environment.
- Use focused commits and meaningful messages. Check for remote changes before pushing; integrate deliberately and never overwrite another session's work.
- Honor existing branch/review requirements. Commit and push only within the authorized task scope.
- Report the commit hash and confirm remote push success separately. A local commit is not a backup on GitHub.

## Interrupted-work recovery

Inspect repository identity, branch, remote, relevant worktrees, status, staged/unstaged diffs, untracked files, recent commits, STATUS, affected implementation, and appropriate tests/logs. Determine what was actually completed before resuming.

Do not blindly restart or discard partial work. If ownership or intended behavior remains unclear, preserve the work and explain the specific unresolved issue.

During substantial tasks, update STATUS at meaningful milestones and before intentional stops: objective, affected areas, last verified milestone, next concrete action, test results, and blockers. Replace old context instead of keeping a diary.

Local uncommitted work survives only where that working copy survives. A new clone sees pushed commits. Documentation cannot recover work that was never saved or transferred. The generated reference snapshot makes published decisions available offline; it does not back up local edits.

## Documentation responsibilities

Each fact has one authoritative home. Edit the source Markdown in ForgottenWords/CCMud; never independently edit generated HTML or pinned copies in Crown-Call. The preserved game-design text is the explicitly documented legacy exception to Markdown source format.

Keep STATUS concise, with current phase, last completed milestone, active work, next action, known issues, deferred items, tests, environment state, and temporary context. Define any verified commit as the revision actually inspected/tested, not a claim that a document contains its own future commit hash.

Keep durable decisions and important rationale in design/architecture. Do not add session-history, handoff, archive, or completed-work folders by default. Git preserves historical revisions. Existing useful documentation remains available.

Use the [publishing and synchronization procedure](publishing.html) after source edits. If public publication or private synchronization fails, report the exact incomplete step instead of calling everything synchronized.

## Release and deployment evidence

Record these facts separately, tied to the relevant revision and environment:

| State | Required evidence |
| --- | --- |
| Implemented | Reviewed changes satisfy the task's behavior |
| Tested | Named checks actually ran, with results and limits |
| Committed | Local commit hash exists |
| Pushed | Correct remote/branch confirms the commit |
| Deployed to DEV | DEV revision identified after deployment |
| DEV verified | Relevant live DEV checks passed |
| Approved for PROD | Thomas explicitly authorized the release/scope |
| Deployed to PROD | PROD revision identified after deployment |
| PROD verified | Relevant live PROD checks passed |

Use unknown/not checked where evidence is absent. Different environments may run different revisions. Passing tests, pushing code, or deploying DEV does not authorize PROD. Documentation publication is separate from game deployment.

### `cc-update` authority and installation

The administrator workflow remains `sudo cc-update dev`. The authoritative implementation is `ops/cc-update` in the private `twpaige/Crown-Call` repository. The installed copy lives at `/usr/local/sbin/cc-update` on the game server and must be an exact copy of that tracked file, owned by `root:root` with mode `0755`:

```text
cd /path/to/Crown-Call
sudo install -o root -g root -m 0755 ops/cc-update /usr/local/sbin/cc-update
sudo bash -n /usr/local/sbin/cc-update
sha256sum ops/cc-update
sudo sha256sum /usr/local/sbin/cc-update
```

The two hashes must match. Make updater changes in `ops/cc-update`, review and test them, commit them, and then reinstall that file; never maintain a divergent server-only copy. Secrets and environment files remain server-local and must not be copied into Git.

The script expects this server contract:

- Ubuntu tools including Bash, Git, Python 3.12 or newer with `venv`, `curl`, `flock`, `sudo`, `tar`, and systemd.
- A root-owned bare clone at `/srv/crown-call/repository.git` whose `origin` can read the private Crown-Call repository. The current server mirrors remote refs, so the default `main` resolves locally after fetch.
- Service users/groups `ccdev` and `ccprod`; release directories `/srv/crown-call/{dev,prod}/releases`; and `current` symlinks below each environment root.
- Root-owned environment files `/etc/crown-call/{dev,prod}/{database,app}.env`, group-readable only by the corresponding service account. These files supply runtime settings and credentials and are not repository content.
- Systemd services `crown-call-dev` and `crown-call-prod`. They run from the corresponding `current` release, listen only on `127.0.0.1:8001` and `127.0.0.1:8000`, and expose `/health`.

For either environment, `cc-update` takes an optional Git ref (default `main`), fetches the mirror, resolves the commit, and constructs an immutable release named with its 12-character commit prefix. A new release is exported with `git archive`, receives its own virtual environment, and installs the application. DEV installs development dependencies and runs the complete pytest suite. PROD installs runtime dependencies and runs `pip check`. Both paths apply forward Alembic migrations, switch `current`, restart the matching service, and retry the local readiness endpoint for up to 20 seconds. If readiness fails, the script restores the previous code symlink and restarts it; database migrations are deliberately not reversed automatically. A host-wide `flock` prevents concurrent DEV/PROD deployments.

After an authorized DEV update, record and compare the requested, mirrored, and active revisions and verify both service and readiness explicitly:

```text
sudo cc-update dev
sudo git --git-dir=/srv/crown-call/repository.git rev-parse main
sudo readlink -f /srv/crown-call/dev/current
systemctl is-active crown-call-dev
curl --fail --silent --show-error http://127.0.0.1:8001/health
```

The active release basename must equal the first 12 characters of the resolved revision, the service must be active, and readiness must succeed. Preserve the updater's output because it is the evidence that tests and migrations completed before activation. Do not run `sudo cc-update prod` without Thomas's explicit production-release authorization; a successful DEV deployment does not provide it.

## Chat-to-development handoff

A temporary handoff should contain: objective; repository; documents to read; accepted design decisions; required behavior; explicitly rejected alternatives; constraints; existing behavior to preserve; implementation guidance; tests; documentation updates; Git expectations; deployment expectations; and definition of done.

Absorb accepted durable conclusions into their authoritative documents. Do not retain a permanent file for every chat. The repository and its pinned knowledge base are the next session's handoff.

## Before finishing

Review the final diff and status. Run appropriate checks and fix attributable regressions. Update design where decisions changed and STATUS where project state changed. Publish/synchronize documentation as authorized, then commit/push the relevant repositories and confirm the remote results.

Report changes, verification, unresolved limitations, commit hashes, push results, website publication, and game deployment state separately. Leave enough current information for a fresh session to continue without the conversation.
