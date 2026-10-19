# Character creation assistance

Use this workflow for avatar creation, appearance selection, naming, voice, motion, background, starting class, or other opening choices that affect the player's attachment to a new game.

## Outcome

Build a coherent character concept and turn it into exact, reproducible selections. Treat a satisfying opening character as part of playability, not as cosmetic trivia. First determine whether a choice is reversible and whether it affects gameplay, story, equipment appearance, animations, or voice.

## Evidence order

Use the highest available trustworthy layer and preserve its limits:

1. reviewed read-only game API, configuration, save metadata, or decoded structured data;
2. game-shipped manifests, resource indexes, or data-table names;
3. build-matched official documentation;
4. live UI enumeration and the player's confirmation;
5. community material as a lead only, never as an unverified option map.

A manifest can prove that an asset or table exists but cannot prove its rows, order, labels, or visual meaning. Keep those fields unknown until decoded or observed. Never seek or bypass encryption keys, inspect process memory, or import copyrighted raw models, textures, audio, screenshots, or save files into Git.

## Catalog ownership

The option catalog lives in the game pack, not in this Skill. Live observations and the confirmed character stay in SQLite. Never fill a missing field by analogy with a sequel, wiki, mod, or nearby asset name. A catalog entry needs the game version and BuildID, category, dependent body type, stable ID when available, UI order, visible label, appearance or audio description, unlock conditions, gameplay effects, evidence, observation time, and confidence.

## Enumerate safely

1. Confirm reversibility and gameplay consequences before optimizing appearance.
2. Inventory trustworthy local categories and structured sources before asking the player to page through the UI.
3. Enumerate one body/physique path at a time and one option per change. Record UI order exactly.
4. Record a palette once only after confirming that it is shared across dependent paths.
5. Avoid a combinatorial screenshot archive. Describe independent options separately and preserve only referenced evidence under the runtime artifact policy.
6. Treat voice and animation as observed only after hearing or seeing the option, or after finding an official label. Do not infer them from an icon or filename.
7. Mark exact unknowns as unknown. If local structured data is inaccessible, continue with live UI enumeration instead of substituting search results.
8. After an update, compare the current version, BuildID, option counts, and source inventory. Mark the old catalog stale until revalidated.

## Recommend a character

Ask only for choices that materially change the concept, then give a complete selection bundle with exact category names, option positions, and one short reason. Translate a requested quality into options the game actually offers. Do not claim that a preset reproduces a named character unless the observed result supports it.

Do not persist a proposed look as final. After the player confirms the completed character, record the exact selections and rationale as one decision linked to the current game/profile and catalog version.

## Source confidence

| Evidence | What it can establish | Default confidence |
|---|---|---|
| Decoded game table with reviewed mapping | IDs, rows, order, labels, dependencies | high |
| Game-shipped manifest or asset index | asset existence and path only | high for existence; none for contents |
| Build-matched official documentation | documented labels, effects, reversibility | high within documented scope |
| Matched live game frame or user-confirmed audio | visible state or heard sample at that moment | high for the observation |
| Community guide, wiki, video, or mod data | candidate clue requiring confirmation | low until independently verified |
