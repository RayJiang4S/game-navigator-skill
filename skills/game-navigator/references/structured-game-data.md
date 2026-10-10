# Structured game data

Use this workflow for skills, equipment, inventory, party, stats, quests, location, save progress, shops, or any request that will recur as the player's state grows. The goal is semantic state, not a screenshot archive.

## Separate state from definitions

Treat these as two independent joins:

- **Player state**: what the player currently owns, learned, equipped, selected, completed, can afford, or has available in the active save.
- **Entity catalog**: what a skill, item, weapon, armor, quest, NPC, location, status, or currency means in the current Build.

A save may expose only stable IDs and quantities. A shipped data table may expose definitions but not the player's current ownership. Do not answer a personalized comparison until the required state and definitions have both been resolved or their gaps are stated.

## Evidence ladder

Use the smallest sufficient layer:

1. Reviewed game API, semantic log, or supported modding interface.
2. Build-validated read-only save parser for current state.
3. Build-validated shipped static catalog for definitions.
4. Current in-game help, inventory, equipment, skill, quest, or map UI.
5. Official build-matched documentation.
6. Community databases or guides as candidate evidence requiring corroboration.

For product-facing support status, query `game-navigator compatibility --pretty`. The Server's public compatibility catalog is the single source used by both the 产品页 and Navigator. Each game has two different profiles:

- `control`: evidence depth in shared directions such as scene, journey, build, economy, encounter and performance. Its levels are `尚未接入 / 能确认 / 看得见 / 读得懂 / 跟得上`.
- `guidance`: what help is presently deliverable, marked `可直接领航 / 打开现场即可 / 存档后更完整 / 继续适配中 / 本作不涉及`.

Do not average dimensions into a single score or infer that guidance is impossible merely because a source is visual. A current-frame choice can be useful guidance without being a live structured event. Conversely, a structured checkpoint cannot prove the current unsaved state. Promote only the affected dimension after Build-scoped validation.

Do not treat file existence, a GVAS signature, a table name, an asset path, or a community item ID as proof of a field's meaning. Preserve the adapter's capability flags. `false` or absent means unknown, not empty.

Also inspect each adapter's `sourcePolicy`: `kind`, `freshness`, `riskTier`, Build scope, continuous-observation flag, and explicit-opt-in requirement. An event source may describe unsaved current state; a checkpoint or save source cannot. Never silently present checkpoint facts as live merely because the game is still open.

## Unsaved live state

Treat “the game is open” and “all internal state is observable” as separate claims:

1. First use the no-frame snapshot and inspect whether the exact AppID/Build has a reviewed `event-live` adapter.
2. `explicit-opt-in-required`, `observer-not-running`, `observer-envelope-rejected`, and `build-mismatch` are genuine missing states. Do not create consent files, install a loader, or weaken the check merely to make the capability green.
3. A reviewed observer envelope is only a receiving contract. It becomes usable only after this exact game has a separately reviewed, user-enabled observer that emits allowlisted facts.
   A reviewed exporter mod (source kind `reviewed-opt-in-mod-exporter`) reports facts with freshness `live-mod-unsaved`: say "实时（未存档，来自 Mod）". Its extra failure states are `exporter-version-mismatch` (the installed exporter is not the reviewed version; suggest `live-mod status`/reinstall) and `build-mismatch` after a game update (the exporter pauses until the new Build is reviewed). Fall back to checkpoints and the current frame, labelled as such.
   Some exporters (OCTOPATH TRAVELER 0) use the same fact keys as the save file, for example `party.active-character-ids` or `inventory.items.<id>.quantity`. For the same key, the live value replaces the checkpoint value. `live.mode` tells you where the player is: `title` means the title screen (no party yet; do not describe a party), and `unavailable` means the game data could not be read (use the last save and the frame).
4. Never install a Mod, DLL loader, runtime hook, script extension, or memory reader from a broad request to “look at the game”. First classify it as `observer`, `experience`, `content`, `balance`, or `cheat`, then obtain per-game authorization after explaining source, exact version, benefit, reversibility, update and save risk, conflicts, multiplayer/anti-cheat impact, and commercial-license status.
   A loader without a reviewed observer that produces useful allowlisted facts is not an enhancement and should not be installed. When an observer is ready, show the exact baseline-versus-enhanced capability delta and offer a reversible per-game choice; never present a bundle-wide “install all Mods” action.
   Steam Workshop is only one possible distribution channel and exists only where the game integrates it. Do not describe official in-game catalogs, mod.io, Nexus, Thunderstore, GitHub loaders, and Steam Workshop as one interchangeable ecosystem.
5. When no observer is available, request one current frame for the immediate dialogue, choice, shop page, HUD, map or menu. State that off-screen inventory, variables and NPC flags remain unknown; do not fill them from an older checkpoint.
6. Games with anti-cheat remain visual/checkpoint-only unless the vendor provides an explicitly supported API. A “read-only” implementation is not automatically safe.

The local observer contract rejects stale (>30 seconds), wrong-Build, wrong-observer, oversized, duplicate, null, or unreviewed facts. Do not advise users to edit its local JSON as a workaround.

## Before requesting a frame

1. Take the no-frame identity snapshot and inspect every matching adapter's status, version, BuildID, warnings, and capability block.
2. Query the Game Pack for a reviewed entity catalog and source policy.
3. Join stable IDs only when the mapping is valid for the observed Build.
4. Request a frame only for fields that remain visually necessary or for a bounded validation sample.

For personalized decisions, use the Navigator's read-through context rather than independently querying each store:

```bash
"$GN" game-context --purpose shop --pretty
"$GN" game-context --purpose skill-learning --pretty
"$GN" game-context --purpose equipment --pretty
"$GN" game-context --purpose party --pretty
"$GN" game-context --purpose readiness --pretty
"$GN" game-context --purpose construction --pretty
"$GN" game-context --purpose exploration --pretty
```

When a question targets one exact entity ID already present in returned verified `stateFacts`,
add `--focus-entity OBSERVED_ID` to prioritize it within the same 128-reference budget. Do not guess
IDs, enumerate a catalog, or use names/questions as IDs. `knowledge-focus-unobserved` means the
reference was not verified in this state, not proof that the player does not own it;
`knowledge-focus-unmapped` means it was selected but Server returned no reviewed definition.
Neither a priority request nor a returned definition proves live ownership or complete coverage.

Use `readiness` for any saved whole-build review: after a level-up, new companion, strong acquisition, accumulated resources, before a difficult segment, or whenever the player wants to reorganize. It intentionally requests the union of character stats, inventory, equipment, party, learned/equipped skills, job points, and resources, plus reviewed character/job/skill/equipment/item definitions. Read [build-readiness-review.md](build-readiness-review.md) for its checkpoint and recommendation rules.

When the current Boss or build screen is needed too, prefer `readiness-context`. Its `game`
contains the same capture's structured readiness context; `frameArtifacts` are local and usable
only with `frameCurrent=true`. Read the image before interpreting visible state. A fresh frame
does not fill a missing structured capability or change a checkpoint's freshness/owner.

For a construction or exploration question needing the screen, prefer `visual-context --purpose
construction|exploration`: its `game` and local current frame share one observation. Open only when
`frameCurrent=true`; absence or failure cannot replay a retained frame. A reviewed frame-only policy
may require no entity definitions, but this proves only visible fields, not a learned-recipe catalog,
complete inventory, reachable route or material availability in inaccessible storage. Keep any checkpoint
facts labelled as saved and follow the remaining `missingCapabilities` and purpose-specific fallback.

The response is usable as a complete structured answer only when the relevant state facts, reviewed entity definitions, and required capabilities are present. Read `decisionPolicy`: it is the Server-authorized, exact-Build and purpose-specific minimum state, entity type, comparison, objective, output, and fallback slice. Do not reconstruct a missing policy from remembered rules or stale local files. `missingCapabilities` includes required policy state that was not proved by the current facts/capabilities and `entity-type:*` entries for required Build-scoped definitions that were not returned; it is a bounded fallback plan, not an empty inventory. Use `--cached` only for an explicitly offline rules/history answer; preserve both `stateChangedAt` (the save content changed) and `stateVerifiedAt` (the same content was most recently rechecked), plus `stateStale`, in the answer.

A save checkpoint is not live state, but it is not worthless either. When `progress.location.trust` is
`trust-if-screen-matches` and the screen agrees (or the player just loaded that save), say the place as fact with
「根据上次存档」. When a reviewed route guide is present, answer "what next" and boss questions from it instead of
sending the player to a menu; label inferences as inferences.

Pixels remain appropriate for current dialogue, a highlighted choice, map geometry, an unsaved menu selection, animation, appearance, visual quality, or confirming a new parser mapping. Pixels should not be the long-term database for a growing skill/equipment/inventory list.

## Save adapters

- Keep adapters read-only and open live files with sharing enabled where the platform permits it.
- Return minimized semantic fields, provenance, file observation time, active-slot confidence, and parse warnings. Never emit or retain raw saves in snapshots.
- Distinguish autosave, manual slots, system data, and Steam Cloud metadata. The newest file is not automatically the active or intended slot.
- Validate a field through controlled before/after saves with one changed variable. Require at least two consistent observations before promoting a mapping from experimental.
- Bind mappings to AppID, BuildID, platform, save schema version or signature, and DLC/mode where relevant.
- Stop parsing at the first schema conflict. Do not silently carry offsets or property names across updates.
- Never write a save, inspect process memory, bypass encryption, disable anti-cheat, or obtain a decryption key as part of the normal companion workflow. Process-memory research is a separate, disabled-by-default per-game engineering lane outside this user Skill; it must never be used as an automatic fallback.

## Static entity catalogs

Keep a per-game, build-scoped catalog outside the total Skill. Store only normalized facts and permitted text, not copyrighted raw tables or assets. Each entity should carry:

- stable ID and aliases;
- category and localized display name;
- exact visible effect fields such as target, weapon/element, hits, SP cost, potency class, duration, conditions, and restrictions;
- provenance, observation time, applicable Build, confidence, and conflicts.

Do not bulk-import an unreviewed wiki into authoritative storage. Candidate catalogs can accelerate discovery, but important numbers and conditions require in-game, official, or independently validated support.

The commercial Server keeps the reviewed Build-scoped `entities.json`, decision model, provenance research, and
performance guidance private. A user's installed Game Navigation Pack contains only the minimal identity and Build
routing manifest. After the pack guard succeeds, Navigator may send the current `gameId`, exact `buildId`, purpose,
its random Navigator installation id, and at most 128 entity IDs already observed in the player's local structured
state. Server verifies current entitlement plus an exact installed package row/version, then returns only the
purpose policy and matching reviewed entity subset; relations are returned only when both endpoints are in that
subset. Never request an empty/full catalog, enumerate guessed IDs, or fall back to a legacy local static catalog.
Legacy source packs may still be imported by migration and test tooling, but they are not the commercial runtime
payload.

An explicitly named Boss has a separate bounded lookup (`target-knowledge`, see operations).
Its reviewed public-name index is authorized by the same installed pack and matched locally;
only that match's canonical ID is sent back for a reviewed definition. The free-text name and
question remain local. This is a static topic, not an observed player-state reference, and must
never be added to `stateFacts` or used to claim a current opponent or current effective values.

## Comparing skills or equipment

Build one compact comparison from the joined state and catalog:

1. current ownership and equipped slot;
2. role: break, damage, sustain, control, exploration, or economy;
3. target, weapon/element, hits, cost, conditions, and restrictions;
4. overlap and complementary coverage;
5. best scene for each option;
6. recommendation now and what future acquisition would replace it.

Separate exact fields from inference. A name or icon can suggest a hypothesis but cannot establish hit count, target range, cost, or potency.

## Bounded fallback

When semantic parsing is unavailable, use one deliberate UI inspection rather than requiring a screenshot for every future acquisition. Prefer a menu that lists the full owned set and detail panel, enumerate a bounded batch, and record only confirmed normalized entity facts. Explain that this is a temporary compatibility fallback, not the intended steady state.
