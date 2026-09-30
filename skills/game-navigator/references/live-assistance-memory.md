# Shared live-assistance memory

Treat translation, navigation, strategy, quests, equipment, shops, choices, and current-state questions as answer modes over one game-scoped context. Never create separate “translation memory” and “guide memory” records for the same fact or future action.

## One live-help pipeline

1. Establish device, Steam AppID, BuildID, foreground process, observation time, and spoiler preference.
2. List active bookmarks for the game once. Load other record types only when relevant: confirmed terms for translation, decisions for a repeated choice, claims for strategy, or experiments for an unresolved mechanic.
3. Collect the minimum fresh structured state or frame needed for the current answer mode.
4. Match the current state against active return conditions before answering. A reminder discovered during translation must be visible during later navigation help, and vice versa.
5. Answer the immediate question.
6. Classify any durable discovery through the shared router below and deduplicate it against existing game records.

Reuse this context across consecutive follow-ups while the game, BuildID, foreground process, and user-guided sequence remain the same. Restart the handshake after focus/process/build changes, sleep or reconnect, capture errors, or a later resumed session.

## Shared memory router

| Discovery | Destination | Persistence rule |
|---|---|---|
| Current pixels, transient menu selection, ordinary translated prose | none | Answer and discard; keep only normal snapshot retention. |
| Future action with a recognizable trigger | `bookmarks` | Persist when useful and not already tracked reliably by the game. |
| User's important build, purchase, story, or accessibility choice | `decisions` | Persist the choice and rationale only when confirmed or actually adopted. |
| Reusable game claim from a source or experiment | knowledge claim/evidence | Keep provenance, build applicability, confidence, and conflicts. |
| Reusable personal translation convention | `translation_terms` | Persist only after the player explicitly confirms the wording. |
| Unresolved mechanic that needs a controlled test | exploration experiment | Store hypothesis, steps, stop condition, and result. |

The source mode does not determine the destination. For example, “collect this at the tavern” seen during translation becomes the same bookmark that a navigation answer would create; an equipment term learned during route guidance still requires explicit confirmation before entering the glossary.

## Conditional bookmarks

Create a `return-later` bookmark when evidence provides a useful action plus a recognizable future trigger and the game is unlikely to preserve the same personal context. Sources can be translated text, visible world geometry, dialogue, tutorials, shop stock, locked encounters, NPC promises, save/log data, or researched guidance.

Examples include collecting a linked-save reward at a tavern, returning after a level threshold, revisiting a merchant with enough money or an item, and retrying an encounter after a party/build condition is met.

Do not duplicate a normal quest whose marker and condition are already clear. Do not store decorative lore, ordinary translated prose, a speculative consequence, or generic walkthrough advice. If the action or trigger is inferred rather than observed, state the uncertainty and ask before persisting.

Before creation, list active bookmarks and avoid semantic duplicates regardless of which answer mode created them. Store an action-led title, game ID, location/NPC, compact JSON trigger and action hints, deferral reason, expected benefit, supported missability, evidence observation, and stable idempotency key.

## Match and complete

Compare structured state first and the current requested frame second with each location, NPC, inventory, level, resource, menu, or interaction trigger.

- High-confidence match: remind the player before the immediate answer without blocking play.
- Partial match: name what may match and ask for confirmation.
- No match: stay silent; do not recite the bookmark list.
- Completed or invalidated action: explain the evidence before changing status.

Reaching a location triggers a reminder; it does not prove the action was completed. Complete the record only after the player confirms it or structured/visible state proves it.

## Runtime boundary

The default system is on-demand. It notices triggers only when the player asks for translation, navigation, strategy, or a current-state check and a fresh observation is taken. It does not continuously capture the screen or wake the handheld.

True proactive reminders require a reviewed game adapter that emits semantic location/event state or an explicitly started bounded co-pilot session. Whole-screen motion, window titles, and stale frames are not sufficient trigger evidence.
