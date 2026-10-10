# Build readiness review

Use this workflow whenever the player wants to reassess the current build after leveling up, gaining a companion, obtaining a potentially important item/equipment/skill, accumulating JP or resources, preparing for a difficult segment, or simply deciding it is time to reorganize.

## Refresh and identify the evidence

1. Follow `journey` and its pack guard. If the question needs the current Boss, HUD or loadout screen,
   use `game-navigator readiness-context --pretty`: it joins one authorized frame capture with that
   same observation's structured readiness context. Otherwise use `game-context --purpose readiness`.
2. Confirm the foreground game, AppID, BuildID, profile/slot identity, and eligible adapter status. Use only installed, consented, Build-valid sensors; an experimental reader is not an available capability.
3. Identify the source and freshness of each needed area independently: current live observation, saved checkpoint, or unknown. For checkpoint evidence, inspect `sourceFile`, `sourceSlotKind`, `sourceLastWriteTimeUtc`, state fingerprint, and warnings. `stateVerifiedAt` advancing while the fingerprint and source write time stay unchanged means the same checkpoint was re-read; it does not include unsaved play. A refreshed snapshot alone does not make checkpoint or historical facts live.
4. If the player says he saved after known changes but the checkpoint did not advance, explain which fields are not captured. Continue independently verified areas without substituting stale values. When Play is unavailable, cached observations are historical, even if they were live when captured; confirm the minimum current information before giving action-sensitive advice.
5. Inspect the returned context's policy and missing requirements; `readiness-context` puts these
   under `game`. Do not make another context/frame request just to reconstruct the same observation.

Only `frameCurrent=true` makes the returned local `frameArtifacts` usable as this observation's
screen. Open the image before reading visible values. An image identifies only what is visible;
it never proves off-screen equipment, hidden enemy state or that a saved checkpoint is active.
Failed, missing, mismatched or aged frames remain unknown. This command does not replay offline
images; offline preparation may instead use the explicitly stale `game-context` checkpoint.

For checkpoint-only areas an autosave is sufficient; do not require a manual save. Eligible live facts can cover unsaved changes without a save. Use the product's reviewed sensor output, never ad-hoc memory inspection, save writes, or input automation.

## Build one joined review

Join current state to Build-valid reviewed entity definitions, then inspect:

1. **Roster and formation**: available and active characters, rows/positions, jobs, levels, stats, role coverage, and characters temporarily unavailable.
2. **Equipment**: empty slots, direct upgrades already owned, role fit, passive synergy, shared-item conflicts, and near-term replacement horizon.
3. **Skills**: learned and equipped skills, mastery/action/support slots, JP, prerequisites, weapon/element coverage, break hits, damage, sustain, buffs/debuffs, turn order, SP pressure, and redundant choices.
4. **Items and resources**: recovery stock, permanent-stat consumables, rare materials, quest/key items, money and other currencies, plus anything best held for a later decision.
5. **Team composition**: front/back or active/reserve placement, break and damage coverage, sustain, control, buff/debuff coverage, and comfort for the player's preferred play style.
6. **Progress gates**: active bookmarks and any observed quest/location condition that should be handled now. Keep light-hint spoiler policy.

Do not optimize every field independently. Prefer a coherent team plan, then assign equipment and skills to support it. Preserve challenge, meaningful Break/Boost and resource trade-offs, and low-friction play.

## Classify recommendations

### When the player cannot beat a particular Boss

Identify the opponent from the current visible encounter or the player's explicit name;
an adjacent quest Boss, world-defeat flag or old battle is not the current opponent.
Use that game's Build-valid reviewed definitions when available. Separate static attack
rules from the phase, health and effects actually visible now; never predict exact damage
or victory from a nominal level, saved equipment or an unreviewed difficulty assumption.

Give usable encounter advice first, then a personalized preparation plan where evidence
allows it: the dangerous move and response, this player's missing defensive/offensive
coverage, and the smallest useful equipment/skill/recovery change. Explain an acquisition
route only when its prerequisites and location are known; otherwise name the missing clue.
Compare owned alternatives before suggesting grinding, purchases or scarce consumables.
Missing parts of the full build review do not prevent advice supported independently by
the visible encounter and reviewed mechanics. Mark the narrower scope instead of claiming
a complete readiness assessment. Offer nearby achievement hints only from already
authorized fresh progress and known conditions; do not start a background achievement read.

Return one compact operation bundle in this order:

- **Must do now**: empty slots, clearly dominated loadout, an unspent point that unlocks required coverage, or an acquisition that materially changes the current team plan.
- **Recommended**: meaningful but nonessential improvements with the expected benefit and trade-off.
- **Hold**: irreversible or scarce consumables, uncertain purchases, or resources better saved until the roster/build is clearer.
- **No action**: key items, already coherent slots, and inventory that triggers automatically later.
- **Unknown**: exact fields blocked by a named capability or catalog gap.

For each change, name the character, slot/menu, current value, target value, reason, opportunity cost, and whether it is reversible. Give all related menu changes together; do not drip-feed one click at a time.

## Bounded fallback and verification

Use at most one deliberate overview screen per missing area, such as the full equipment, skill, roster, or shop list. Do not ask the player to highlight every item. Record reviewed reusable definitions in the Game Pack; keep transient ownership and loadouts in SQLite only.

After the player applies changes, rerun `readiness` and verify the resulting state diff from eligible current evidence. Ask for one normal save when convenient only for changes that the available reader observes through checkpoints. Do not claim a selection is applied merely because it was recommended, briefly highlighted, or remains in an offline cache. Keep unverified areas unknown. The review does not imply that story progression must immediately follow.
