---
title: Commands
description: Current CCMUD commands and reviewed legacy command candidates.
reviewed: 2026-09-28
nav: commands
permalink: /CCMud/commands.html
---

# CCMUD command reference and adoption review

This is the **CCMUD command-design authority**: what a player or staff member can type now, and which familiar Shadows/SOI verbs CCMUD should adopt as their systems become ready. It does not change game code. “Adopt” below records a design decision, **not** an assertion that a command works today.

The September 28 [historical handoff](https://github.com/twpaige/Crown-Call/blob/main/docs/reference/Shadow_CMD_Bible.md) is the survey input, not a verified inventory of every Shadows handler. Some of its verbs are explicitly conditional (“if identified”) and must not be represented as proven historical commands. The [game design](design.html), [HOG rules](hog.html), [current status](status.html), and actual server behavior take precedence over old examples. In particular, current water movement replaces the former ordinary three-foot wading rule. The server owns game behavior; the web client is one of its clients.

* Contents
{:toc}

## Reading the decisions

| Field | Meaning |
| --- | --- |
| **Live** | Documented as implemented on the current source branch; deployment to any particular server is separate. |
| **Adopt: next** | A useful command for an existing or near-term gameplay system. Still needs explicit behavior, code and tests. |
| **Adopt: later** | A good fit once its owning system exists. Do not invent that system just to add a verb. |
| **Merge** | Retain the useful interaction under another command or movement mode. The historical word may become an alias after review. |
| **Review** | Meaning, historical provenance, authority, access or interaction policy is unresolved. |
| **Omit** | Do not carry the independent verb into CCMUD; the reason appears in the table. |

Only the **Live** section gives current syntax. Examples elsewhere describe intent, not usable syntax or promised aliases. Staff access, object selection, privacy and character permissions are checked on the server.

## Live server commands

These are the documented current source-level commands. Release and feature gates still apply; consult [status](status.html) and the private implementation before asserting what any deployed instance accepts.

| Family | Commands | Current meaning |
| --- | --- | --- |
| Observe | `LOOK`, `L`, `LOOK BRIEF`, `LOOK NORMAL`, `LOOK MAXIMUM` | Cormac v1 describes local wilderness geography with a stable title/body. Verbosity is a session preference, initially NORMAL. Admin RAW remains separate. Temporary ordinary local perception does not imply distant certified LOS. |
| Travel direction | `N`, `NE`, `E`, `SE`, `S`, `SW`, `W`, `NW` and full direction names; `TRAVEL <0–359>` | Set a continuous compass heading. North is 0°, east is 90°. |
| Travel pace | `TRUDGE`, `WALK`, `JOG`, `RUN`, `SPRINT` | Select land pace, distinct from direction. |
| Stop | `STOP` | Stop active voluntary travel; when swimming, enter FLOATING. Floating does not cancel current. |
| Water | `WADE <direction>`, `WADE`, `SWIM <direction>`, `FLOAT`, `STAND` | Choose water movement or state as permitted by represented depth. See [Water Movement v1](design.html#water-movement-v1-2026-09-28). |
| Preferences | `SET`, `SET AUTOWADE`, `SET AUTOSWIM` | List settings or toggle the two independent, persisted water-travel preferences. Both default OFF. |
| Character | `STATUS` | Current CCMUD status, including travel state; historical Shadows STATUS has different, unresolved semantics. |
| Places and objects | `ENTER <place>`, `EXIT`, `GET <object>`, `DROP <object>`, `INVENTORY`, `CUT <resource>`, `OPEN <door>`, `CLOSE <door>` | Interact with implemented structures, resources and objects. ENTER can deliberately enter a nearby represented waterway under its current rules; existing feature gates still apply. |
| Communication and session | `SAY <message>`, `HELP`, `QUIT` | Speak to nearby listeners, list help, or close the session. |

`CLEAR` clears the **web transcript locally**. It does not send an in-world command or stop travel. Do not assume its availability or effects in a telnet client.

### Staff diagnostics

| Command | Access and role |
| --- | --- |
| `APPROACH <id|feature_id>` | Admin diagnostic steering to known prepared geography; all normal safety and certification restrictions apply. It is not player navigation or a perception claim. |
| `JUMP <direction> <miles>` | Admin endpoint jump to certified geography; it is not ordinary travel or a general override. |
| `HOGDISPLAY RAW` / `HOGDISPLAY PROSE` | Admin session presentation toggle. RAW is diagnostic; PROSE is the normal path. |

### STATUS name collision

**DUPLICATED / NEEDS ADDITIONAL ATTENTION.** Shadows/SOI also used `STATUS`. Keep current CCMUD `STATUS` authoritative. Compare the historical handler in a targeted review before importing any of its fields. Do not silently replace, merge or drop either meaning.

## Adopt next: familiar verbs that support existing gameplay

These are the highest-value additions to the interaction vocabulary, ordered by the system they need. None is marked implemented. Define target selection, permission, failure messages and tests before adding each to the command parser.

| Family | Proposed verbs | Why they fit; design boundary |
| --- | --- | --- |
| Inspection | `LOOK <target>`, `EXAMINE <target>`, `LOOK IN <container>` | Players need deliberate inspection of nearby objects, doors and features. Reuse one perception and target-selection contract. EXAMINE can be an alias or a more detailed view after behavior is settled; do not claim unimplemented distant sight. |
| Possessions | `PUT <object> IN <container>`, `GIVE <object> TO <character>` | Complete GET/DROP with physical transfers. Require actual containers, ownership, nearby targets and atomic persistence. |
| Equipped items | `WIELD`, `WEAR`, `REMOVE` | Equipment matters for weapons, armor and future tool use. Establish inventory slots and Crown's Calling rules first; HOLD may merge into this family. |
| Expression | `EMOTE`, `WHISPER`, `SHOUT` | Preserve the old game's social feel. Delivery must use physical range, hearing, obstacles and permissions; YELL is a candidate alias for SHOUT, not a second sound engine. |
| Tutorial survival | `SKIN`, `LIGHT` or `START FIRE`, `ROAST` or `COOK`, `EAT`, `DRINK` | Directly support the planned hunt → skin → fire → roast → eat opening. Define resources, tools, food and fire safety before implementation. Choose one canonical fire/cooking verb and document aliases only when accepted. |
| Survival water | `FILL <container>`, `POUR` / `EMPTY` | Needed for the optional waterskin path. Use actual nearby water, containers and quantities; do not equate potable water with merely represented water. |
| Immediate help | `HELP <command>` | Familiar discoverability; filter staff-only details by access. Current `HELP` alone is live; targeted help needs verification or implementation. |

**Practical order:** inspection and targeting → object transfers and equipment → physically ranged speech → survival tutorial and containers. Each depends on the corresponding authoritative world state, not on a separate old room model. This order is a documentation recommendation, not a code release commitment.

## Adopt later: good vocabulary with prerequisites

| Family | Verbs or capabilities | Prerequisite and decision |
| --- | --- | --- |
| Hunt and gather | `HUNT`, `FISH`, `TRACK`, `BUTCHER` | Adopt as distinct verbs only after creatures, tracks, catches and carcass stages exist. If BUTCHER adds no action beyond SKIN/harvesting, merge it there. |
| Doors and structures | `LOCK`, `UNLOCK`, `KNOCK`, `PUSH`, `PULL` | Adopt with real locks, keys, doors and movable objects. OPEN/CLOSE remain the current base; physical state and access govern effects. |
| Physical handling | `DRAG`, `LIFT`, `CARRY` | Adopt with encumbrance and persistent carried/dragged targets. CARRY may become inventory/equipment state; no duplicate position system. |
| Books and boards | `READ`, `WRITE`, `COPY`, `POST`, `REPLY`, `NEWS` | Adopt with physical pages, writing materials, boards, permissions and provenance. COPY must respect book versus parchment rules. Embedded artwork is content, not a magic command. |
| Orientation | `MAP`, `COMPASS`, map bookmarks | Adopt when persistent map knowledge and possession are defined. Compass and map are different affordances; avoid revealing admin geometry. |
| Groups | `FOLLOW`, `LEAD` | Adopt for consent-aware following, companions and mounts through the same continuous travel controller. No room-by-room follower cloning. |
| Rest and posture | `SIT`, `REST`, `SLEEP`, `WAKE` | Adopt when posture, awareness and recovery states are designed. Existing STAND water behavior must remain valid. |
| Stealth and perception | `HIDE`, `SNEAK`, `SEARCH` | Adopt when observers, cover and detection are modeled in continuous space. SNEAK should modify movement, not fork it. SCAN may merge into LOOK/SEARCH. |
| Terrain actions | `CLIMB`, `CRAWL` | Adopt when certified geometry and movement rules can support them. No invented room exits or bypasses of hazard refusal. |
| Economy and NPCs | `ASK`, `BUY`, `SELL`, `PAY`, `HIRE` | Adopt as NPCs, trade, currency and contracts become real. Keep transactions auditable; ASK requires actual dialogue, not invented responses. |
| Equipment care | `REPAIR`, `SHARPEN`, `EXTINGUISH` | Adopt when condition, materials and fire/light rules exist. MEND is a possible REPAIR alias; LIGHT shares the tutorial fire family. |
| Frontier firearms | `AIM`, `FIRE` / `SHOOT`, `LOAD`, `UNLOAD`, `RELOAD` | Adopt with era-appropriate firearms and Crown's Calling ranged combat. Select one canonical fire verb; do not import Shadows combat or modern weapon mechanics. |
| Combat | `ATTACK`, `FLEE`, `THROW` | Adopt only with CCMUD combat state, opposed rolls, wound rules, explicit intent and safe transitions into/out of travel. KILL is a candidate alias requiring review; HIT/STRIKE should not create parallel attack systems. |
| Riding | `MOUNT`, `DISMOUNT` | Adopt when horses, ownership, terrain and mounted travel exist. Ordinary directional TRAVEL should still own the world position. |
| Magic and faith | `CAST`, `PRAY`, healing action | Reserve vocabulary for deliberately adopted MUD magic/faith mechanics. The tabletop Caps do not themselves create server commands or grant inherited spells. |
| Character and world info | `TIME`, `WEATHER`, `WHO` | Adopt when clock, weather and visibility/privacy policy exist. Report only appropriate player knowledge. |
| Homesteads | `CLAIM`, player `BUILD`, property-description submission | Adopt after claims, construction, ownership, version history and review rules are settled; never reuse old room builder semantics. |

## Review before committing to independent commands

| Vocabulary or capability | Decision needed |
| --- | --- |
| `TELL`, OOC channels, `THINK` | Communication/privacy policy; distinguish private remote messages from local roleplay. THINK may remain narrative only. |
| `NOTES`, `PETITION`, `REPORT`, `WATCH` | Scope, visibility, moderation retention and player/staff workflow. Do not conflate player notes, safety reports and staff observation. |
| `STEAL`, `PICKPOCKET`, lock `PICK` | Theft and PvP consent, inventory ownership, lock mechanics and sanctions. PICKPOCKET may merge under STEAL if meaningful. |
| `TEACH`, `LEARN`, `SPEAK` / language selection | Advancement, languages, literacy and player interactions remain unresolved. Caps and languages are systems, not automatic verbs. |
| `BARTER`, `HAGGLE`, `ORDER` / NPC `COMMAND` | Trade and companion semantics. Resolve the ambiguity with ordinary player commands before reserving names. |
| `USE` | A generic fallback only if concrete verbs cannot clearly cover an action; do not hide every interaction behind USE. |
| `RIDE` | Likely merge into MOUNT plus ordinary direction/pace; retain only if it has a separate player meaning. |
| Group/regiment map transfer, vehicle or boat commands | Need settled objects, ownership and transport; boats are explicitly deferred by Water Movement v1. |
| Character-description customization; names and approval | Ownership, moderation, identity and permissions; keep distinct from exterior/property descriptions. |
| Staff `WARN`, bans, communication blocks, naming/description approval and builder verbs | Adopt capabilities through permissioned, validated and logged administrative interfaces; decide exact syntax when the subsystem exists. CCAI may call the same restricted interfaces, never a privileged database/shell shortcut. |

The historical handoff mentions several of these conditionally. Their appearance here is a **CCMUD design candidate**, not proof that a named `do_xxx()` handler existed in Shadows.

## Merge or omit independent verbs

| Candidate | Decision | Reason |
| --- | --- | --- |
| `TAKE`, `INV`, `I` | Review as GET/INVENTORY aliases | Preserve familiarity when parser ambiguity and implementation are checked. Do not advertise today as live aliases. |
| `YELL` | Merge with SHOUT | One loud-speech range and hearing policy. |
| `HIT`, `STRIKE`, `KILL` | Review as ATTACK aliases | One combat-intent and Crown's Calling rules engine; KILL carries a different intent and needs explicit review. |
| `SCAN` | Merge or review under LOOK/SEARCH | Avoid redundant perception code unless directional scanning has a real distinct function. |
| `HOLD` | Review within equipment | An actual occupied hand may justify it; otherwise WIELD/WEAR/equipment state already covers the action. |
| `RIDE` | Likely merge with mounted TRAVEL | Mount changes locomotion context; heading continues in the shared world. |
| `CRAFT` | Review as recipe/workflow entry | Favor concrete FIRE, CUT, ROAST and other physical verbs where they communicate the task. |
| Separate `ART` command | Omit | `<art>…</art>` denotes content in writing, not an independent magic action. |
| `AUTOWADE` or `AUTOSWIM` as standalone commands | Omit | These are live `SET` preferences, not independent commands. |
| `CORMAC`, Cap names, HOG metadata or admin feature IDs as player commands | Omit | Prose renderer, attributes and diagnostics are underlying systems or data. |
| Old room creation/exits, legacy combat/wound state, old account implementation | Omit old semantics | Coordinate geography, current combat decisions and current account system own these behaviors. Useful builder intent can be adapted separately. |

## Organization and implementation rules

1. Keep the live index above the candidate lists. Each implemented command eventually gets a compact entry with syntax, aliases actually accepted, access, intended behavior, important failure cases and links to detailed rules.
2. Group commands by **player action**: observation/navigation, water, objects/equipment, communication, survival, combat, writing/maps, social/economy, then staff. A verb may act on different target types but should share one clear parser contract.
3. Design disposition (adopt/merge/review/omit) and implementation state (live/planned) are independent. No candidate table grants permission to a player or implies deployed behavior.
4. Reuse continuous coordinates and authoritative HOG geometry for movement, perception, sound and nearby targets. Never turn an uncertified crossing into an implicit success.
5. Use specific failure messages. Ambiguous targets should request clarification; water entry should require the explicit current permission path; missing objects should not create guessed objects.
6. When building new verbs, verify current code and relevant tests, then update this reference and [status](status.html). Maintain separate evidence for source implementation, remote push, DEV release and PROD release.

### Focused open questions

- Compare the real historical Shadows `STATUS` handler with current CCMUD `STATUS` before choosing shared fields.
- Determine whether EXAMINE, HOLD, SCAN, RIDE, BUTCHER and CRAFT merit a distinct action after their owning systems exist.
- Confirm old Shadows provenance and syntax only for commands where it changes a concrete implementation decision.
- Establish communication privacy, theft/PvP consent, staff moderation and travel targeting rules before implementing those families.
