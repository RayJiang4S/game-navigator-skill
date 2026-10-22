# Structured game data

Use this for skills, equipment, inventory, party, stats, quests, location, saves, shops, or any request that will recur. The goal is semantic state, not a screenshot archive.

Player state (owned, learned, equipped, selected, completed, affordable, available) and the entity catalog (what an ID means on this Build) are separate joins. Do not give a personalized comparison until both are resolved or the gaps are named. A save may expose only IDs and quantities. A data table may expose definitions and not ownership.

## Evidence ladder

Use the smallest sufficient layer:

1. Reviewed game API, semantic log, or supported modding interface.
2. Build-validated read-only save parser.
3. Build-validated shipped catalog.
4. Current in-game help, inventory, equipment, skill, quest, or map UI.
5. Official build-matched documentation.
6. Community databases or guides, as candidates that need corroboration.

`game-navigator compatibility --pretty` is the same catalog as the 产品页. Read `control` (`尚未接入 / 能确认 / 看得见 / 读得懂 / 跟得上`) and `guidance` (`可直接领航 / 打开现场即可 / 存档后更完整 / 继续适配中 / 本作不涉及`) separately. Do not average them. A frame can guide a choice without being a live event. A checkpoint cannot prove unsaved state.

A file, GVAS signature, table name, asset path, or community ID does not prove a field's meaning. `false` or absent means unknown, not empty. Read `sourcePolicy`: `kind`, `freshness`, `riskTier`, Build scope, continuous observation, and explicit opt-in. An event source may be unsaved current state. A checkpoint cannot. Do not present checkpoint facts as live because the game is open.

## Unsaved live state

1. Start with the no-frame snapshot and whether this AppID and Build has a reviewed `event-live` adapter.
2. `explicit-opt-in-required`, `observer-not-running`, `observer-envelope-rejected`, and `build-mismatch` are real gaps. Do not create a consent file, install a loader, or weaken the check to make the capability green.
3. A reviewed observer envelope is only a receiving contract. It is usable after this game has a separately reviewed, user-enabled observer that emits allowlisted facts. A reviewed exporter (`reviewed-opt-in-mod-exporter`) uses freshness `live-mod-unsaved`: say "实时（未存档，来自 Mod）". `exporter-version-mismatch` means the installed exporter is not the reviewed version (`live-mod status` or reinstall). `build-mismatch` after a game update pauses the exporter until that Build is reviewed. Fall back to the checkpoint and the current frame, labeled as such. Some exporters reuse save fact keys such as `party.active-character-ids` or `inventory.items.<id>.quantity`. For the same key, the live value replaces the checkpoint value. `live.mode=title` is the title screen: there is no current party, place, or objective. Checkpoint party, level, equipment, items, money, and recorded completion may be discussed only as that save for next time, never as this scene. `unavailable` means the game data was not read: use the last save and the frame.
4. Do not install a Mod, DLL loader, runtime hook, script extension, or memory reader because the player asked to “look at the game”. Classify it as `observer`, `experience`, `content`, `balance`, or `cheat`, then get per-game authorization after source, exact version, benefit, reversibility, update and save risk, conflicts, multiplayer and anti-cheat impact, and license. A loader without a reviewed observer that emits useful allowlisted facts is not an enhancement. Show the baseline-versus-enhanced delta and offer one reversible per-game choice. Steam Workshop is only a channel where that game integrates it. Do not treat in-game catalogs, mod.io, Nexus, Thunderstore, GitHub loaders, and Workshop as one ecosystem.
5. With no observer, one current frame covers the visible dialogue, choice, shop, HUD, map, or menu. Off-screen inventory, variables, and NPC flags stay unknown. Do not fill them from an older checkpoint.
6. Games with anti-cheat stay visual and checkpoint-only unless the vendor provides an explicitly supported API. A “read-only” implementation is not automatically safe.

The local observer contract rejects stale (over 30 seconds), wrong-Build, wrong-observer, oversized, duplicate, null, or unreviewed facts. Do not edit its local JSON as a workaround.

## Before a frame

Take the no-frame snapshot and read adapter status, version, BuildID, warnings, and capabilities. Join stable IDs only when the mapping is valid for the observed Build. Request a frame only for fields that are still visual.

Select only a purpose this pack supports. When a reviewed pack tracks worker preparation and skill allocation, use `readiness`, not
`skill-learning`. When current points or a selected worker must be checked, ask for their
skill page and use one `readiness-context`; do not identify them from anonymous saved ordinals.
This is the initial supported route, not permission to retry a denied purpose under another label.

```bash
"$GN" game-context --purpose shop --pretty
"$GN" game-context --purpose skill-learning --pretty
"$GN" game-context --purpose equipment --pretty
"$GN" game-context --purpose party --pretty
"$GN" game-context --purpose readiness --pretty
"$GN" game-context --purpose construction --pretty
"$GN" game-context --purpose exploration --pretty
```

`--focus-entity OBSERVED_ID` prioritizes one ID already in verified `stateFacts`, inside the same 128-reference budget. Do not guess IDs or pass names as IDs. `knowledge-focus-unobserved` means it was not verified in this state, not that the player lacks it. `knowledge-focus-unmapped` means Server returned no reviewed definition. Neither proves live ownership or complete coverage.

`readiness` is the saved whole-build review: stats, inventory, equipment, party, learned and equipped skills, job points, resources, and the matching definitions. Checkpoint and recommendation rules: [build-readiness-review.md](build-readiness-review.md). When the Boss or build screen is needed, `readiness-context` joins that same capture's frame. Open `frameArtifacts` only when `frameCurrent=true`. A fresh frame does not fill a missing structured capability or change a checkpoint's freshness. Construction or exploration that needs the screen uses `visual-context`. A frame-only result proves visible fields, not a learned-recipe catalog, a complete inventory, a reachable route, or material in inaccessible storage.

The answer is complete for a claim only when the relevant facts, definitions, and capabilities are present. `decisionPolicy` is the Server-authorized Build- and purpose-specific minimum. Do not reconstruct a missing policy. `missingCapabilities` lists unproved required state and `entity-type:*` definitions that were not returned. It is a fallback plan, not an empty inventory. `--cached` is only for an explicit offline rules or history answer. Keep `stateChangedAt`, `stateVerifiedAt`, and `stateStale` distinct.

A missing inventory does not erase an independently visible cue or a known saved level, and neither proves the current build. After access, Build, and purpose checks pass, public research may supplement mechanics. Attribute it separately. Do not add it to `stateFacts` or use it to fabricate state.

When `progress.location.trust` is `trust-if-screen-matches` and the screen agrees, or the player just loaded that save, say the place with 「根据上次存档」. Pixels suit the current dialogue, a highlighted choice, map geometry, an unsaved menu selection, animation, appearance, or a parser check. They are not the long-term database for a growing list.

## Saves

Open live files read-only, with sharing where the platform allows. Return minimized fields, provenance, observation time, active-slot confidence, and parse warnings. Never retain raw saves in snapshots. The newest file is not automatically the active slot. Distinguish autosave, manual slots, system data, and Steam Cloud metadata. Stop at the first schema conflict. Never write a save, inspect process memory, bypass encryption, disable anti-cheat, or obtain a decryption key as part of the normal companion workflow. Process-memory research is a separate, disabled-by-default per-game engineering lane outside this user Skill; it must never be used as an automatic fallback.

A reviewed colony pack's saved worker skill records contain checkpoint-local ordinals,
native weighted experience and independent mastery/grant record counts. Join an ordinal
to saved vitals only within the exact same checkpoint. Experience is not seconds, a level
or available skill points; mastery may include grants and is not the count of paid-for
skills. Missing fields stay unknown, while a returned zero is observed. These records can
inform a saved-progress review, not identify a worker by name or prove active perks,
morale requirements or an optimal respec. For a current skill decision, use the relevant
worker skill page when the necessary effective information is not otherwise available.

## Definitions the Server returns

Do not bulk-import an unreviewed wiki, request an empty or full catalog, enumerate guessed IDs, or fall back to a legacy local static catalog. After the pack guard, Navigator may send `gameId`, exact `buildId`, purpose, its installation id, and at most 128 entity IDs already observed locally. Server checks entitlement and the installed package, then returns the purpose policy and the matching subset. Relations return only when both ends are in that subset. `target-knowledge` (see operations) is a static topic: the free-text name stays local, only the matched canonical ID is sent, and the definition must not be added to `stateFacts` or treated as the current opponent or current effective values. A static wire rating does not establish network membership, load, materials, or circuit capacity.

## Compare, then stop

Compare current ownership and equipped slot, role, cost, conditions, restrictions, overlap, and what a later pickup would replace. Separate exact fields from inference. A name or icon does not establish hit count, range, cost, or potency.

After the checks pass, use the evidence already returned. A gap is not a reason to force a hidden-state reader, install an observer, or drop independently supported advice. If one fact still matters, ask the player to open that page, then read one new authorized frame. For example: 「打开装备页，让我看看你现在穿的装备，再帮你比较。」 Do not ask them to type the inventory or to verify JSON. Do not interrupt combat for a checklist. A visible value can correct a stale checkpoint for this answer without rewriting that checkpoint or proving off-screen completeness. One further page needs an explained remaining gap. If they decline or the frame fails, continue the narrower help and name the uncertainty. Do not turn the screen into a screenshot archive or claim that semantic parsing is now available.
