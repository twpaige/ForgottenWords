---
title: Current status
description: What is implemented, what remains, and what has actually been verified.
reviewed: 2026-09-26
nav: status
permalink: /CCMud/status.html
---

## Release-blocking DEV failure and correction

**Do not activate `9e70cced3948635a2285e2dd563b32c11f8e64c5`.** Thomas reports its
actual DEV server-side pytest gate terminated with a native segmentation fault,
before a successful activation message. Earlier local 215-test success did not
establish server safety. The previous deployment recommendation is superseded.

Corrective implementation `e5e54571a6e14c97339156cefbb3f38c01027578` is committed and pushed to private Crown-Call
main. Its final documentation-sync revision is the new DEV deployment candidate.
It has passed local checks below; its Linux server-side gate is **not yet verified**.
PROD is untouched. No database migration or player relocation is introduced.

## Established mechanism and ownership fix

The failing fixture shared in-memory SQLite through StaticPool. The new game worker
could run `travel_snapshot` while the pytest thread ran `load_character`. Separate
SQLAlchemy Sessions then received the same underlying DBAPI connection, allowing
overlapping execution and one session's close/rollback to affect another session.
`check_same_thread=False` removes a check; it does not give each session an independent
transaction. This alias is reproduced deterministically without racing native SQL.

The exact Linux native crash/core was not reproduced locally. Loaded psycopg and
SQLAlchemy native extensions alone do not identify the active failing driver. The
test's actual bound engine was SQLite; the application module also imports the
PostgreSQL driver. No dependency downgrade or native-extension disabling is used.

Services now require fresh-session factories bound to an Engine with independent
connection checkouts. Shared Session callbacks, pre-bound Connections, StaticPool
and SingletonThreadPool are rejected before work begins. Application PostgreSQL
retains its normal pool. Each service operation creates, uses and closes its own
session in one thread. Detached results do not retain a usable session across the
worker boundary. Travel copies immutable scalar character/load/structure facts and
closes its read session before integration/observations; no live ORM state remains
in the ContextVar. Existing mutation commit boundaries remain intact.

Concurrent fixtures and benchmarks use isolated file-backed SQLite WAL/QueuePool.
No database-wide application/test lock is added. Connection auditing checks exclusive
checkout ownership, same-thread execution/checkin and release at test teardown.
Barrier tests prove a normal reader finishes on another native connection while
the travel worker is held in its character query; another test proves a reader's
rollback cannot cancel an overlapping writer's commit.

## Verification and responsiveness

- Three fresh-process fault-handler stress runs: **11 passed each**, in
  54.78/54.68/54.86 seconds, including the original real-HOG socket test each time.
- Those runs cover 15 real-HOG concurrency rounds: **4,500 direct character reads,
  600 HTTP account reads and 1,200 travel ticks**, plus background materialization.
- Final complete suite: **225 passed**, 13 upstream warnings, **331.35 seconds**.
  Existing tests were retained; their database plumbing now permits safe concurrency.
- Changed-file lint passes. Full lint retains eight existing findings in untouched
  generators/tests. Native extensions remain enabled.

Game operations still run outside the network event loop with per-character ordering.
Background HOG generation, five-ms sampling yields, bounded directional prewarming,
precise water interception and explicit stop presentation are preserved.

New command latency, milliseconds (30 samples per command, generation pending):

| Workload | SAY median / p95 / max | LOOK median / p95 / max | STOP median / p95 / max |
| --- | ---: | ---: | ---: |
| Nine-chunk burst, warm regional catalog | 0.64 / 0.98 / 185.30 | 28.10 / 167.10 / 317.25 | 5.73 / 7.46 / 8.24 |
| Sustained generation, cold regional catalog | 0.81 / 1.16 / 279.23 | 31.10 / 260.07 / 359.69 | 5.82 / 259.84 / 770.87 |

Event-loop nominal-five-ms heartbeat p95/max: 32.39/88.09 ms (burst), 48.18/97.96 ms
(stress). Cold GIL/storage scheduling tails remain. This does not claim uniformly
improved tails or live-server latency. Prior StaticPool measurements are historical,
not concurrency-safety evidence; the storage change also limits strict comparisons.
Private `docs/hog-concurrency-benchmark.json` records environment, results and commands.
Local environment: Windows, Python 3.12.14, SQLite 3.53.1, SQLAlchemy 2.1.1, psycopg 3.3.6.
No local Linux/PostgreSQL execution or live DB inspection was available.

## Preserved HOG scope

Seed `867359018957601`, GROUND, existing characters and the Pioneer Cabin are unchanged.
Inferred local channel/lake exclusions permit certification only of dry exterior
ground. Strict origin-east travel passes the old stream `...:7346:617` bounding-box
false refusal and stops before stream `...:7346:593` at `(197096,0)`, around 3.11 miles.
Thomas's reported earlier 1.27-mile stop versus the strict-route 1.515-mile reproduction
remains unresolved without exact live coordinates/history. Full geometry details
remain in HOG; unknown depth/banks/current/consequences stay blocked, including `east!`.
Distant features remain admin RAW diagnostics with `perceived=false`; no new game-design
decision, swimming system or prose generator is introduced. The cabin remains unaudited.

## Failed updater state and next DEV action

Thomas manually verified that `/srv/crown-call/dev/current` still points to
`/srv/crown-call/dev/releases/6b270d460cb9`. The failed `9e70cce` candidate was not
activated. The tracked updater uses `set -Eeuo pipefail`; its pytest gate precedes
migrations, the symlink switch and service restart. This agrees with the live symlink
evidence. The failed candidate directory may remain. SSH port 22 still times out
from this environment, so service/health status has not been independently rechecked.

Verify on the server:

```sh
sudo readlink -f /srv/crown-call/dev/current
systemctl is-active crown-call-dev
curl --fail --silent --show-error http://127.0.0.1:8001/health
```

Use the new exact final revision supplied with this correction in
`sudo cc-update dev <new-commit>`. Do not rerun the blocked `9e70cce` release. Preserve
the test-gate output and compare the new active release to the requested commit's
first 12 characters only after successful activation. Health remains
`hog_exploration=water_exclusions_v3`. A new server failure remains release-blocking.

After successful DEV activation, exercise RAW eastward travel while another browser
loads account/character data; issue SAY/LOOK/STOP during generation and verify safe
water stops. Publication/pinned-reference synchronization is separate from game
deployment. No PROD operation is authorized.
