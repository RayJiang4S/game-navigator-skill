# Build readiness review

Use this workflow whenever the player wants to reassess the current build after leveling up, gaining a companion, obtaining a potentially important item/equipment/skill, accumulating JP or resources, preparing for a difficult segment, or simply deciding it is time to reorganize.

## Refresh the intended checkpoint

1. Call `game-navigator status --pretty`, then `game-navigator snapshot --no-frame --pretty`.
2. Confirm the foreground game, AppID, BuildID, profile/slot identity, and save adapter status.
3. Inspect the save adapter's `sourceFile`, `sourceSlotKind`, `sourceLastWriteTimeUtc`, state fingerprint, and warnings. `stateVerifiedAt` advancing while the fingerprint and source write time stay unchanged means the same checkpoint was re-read; it does not include unsaved play.
4. If the player says he saved after known changes but the checkpoint did not advance, stop the audit and explain that the new state is not captured. Do not substitute stale values or request unrelated screenshots.
5. Run `game-navigator game-context --purpose readiness --pretty`.

An autosave is sufficient; a manual save is not required. Never write the save or inspect process memory.

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

Return one compact operation bundle in this order:

- **Must do now**: empty slots, clearly dominated loadout, an unspent point that unlocks required coverage, or an acquisition that materially changes the current team plan.
- **Recommended**: meaningful but nonessential improvements with the expected benefit and trade-off.
- **Hold**: irreversible or scarce consumables, uncertain purchases, or resources better saved until the roster/build is clearer.
- **No action**: key items, already coherent slots, and inventory that triggers automatically later.
- **Unknown**: exact fields blocked by a named capability or catalog gap.

For each change, name the character, slot/menu, current value, target value, reason, opportunity cost, and whether it is reversible. Give all related menu changes together; do not drip-feed one click at a time.

## Bounded fallback and verification

Use at most one deliberate overview screen per missing area, such as the full equipment, skill, roster, or shop list. Do not ask the player to highlight every item. Record reviewed reusable definitions in the Game Pack; keep transient ownership and loadouts in SQLite only.

After the player applies changes, ask for one normal save when convenient, rerun `readiness`, and verify the resulting state diff. Do not claim an unsaved selection is applied merely because it was recommended or briefly visible. The review does not imply that story progression must immediately follow.
