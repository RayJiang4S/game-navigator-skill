# Translation assistance

Use this workflow when the player asks to translate or explain game text in another language. Use the existing on-demand game-window snapshot and the AI tool's available vision; no separate OCR service, local model, overlay or continuous capture is required. If this tool cannot inspect images, accept pasted text instead of pretending to see the frame.

## Live workflow

1. Run `game-navigator status --pretty`, then `game-navigator snapshot --no-frame --pretty` to identify the foreground game and BuildID.
2. Request a frame because visible text requires pixels. Never capture an unmatched desktop or a non-game window.
3. Read only the requested region when the player names one: subtitle, item, skill, tutorial, dialogue, or choices. Otherwise cover the text that is relevant to the immediate decision, not every decorative label.
4. Distinguish directly visible text from context inferred from icons, layout, prior state, or game knowledge. Mark cropped, blurred, stylized, or ambiguous text instead of inventing it.
5. Use in-game wording and an existing game glossary first. Research only when an official localization, game-specific term, or mechanic remains unclear; follow the evidence protocol for material gameplay consequences.
6. Give the smallest answer that lets the player continue playing. Lead with the natural meaning in the player's preferred language, then explain only the unfamiliar word or phrase and the immediate gameplay consequence the player needs.

For consecutive screens in one user-guided translation sequence, do the full identity handshake only on the first screen. On each follow-up, request a fresh frame directly and verify that the returned device, Steam AppID, BuildID, foreground process, and capture status still match the established session before reading text. Restart the full handshake after a focus/process/build change, sleep or reconnect, capture error, or a later resumed session. Never reuse the previous pixels as the new selection.

When a menu contains several selectable descriptions, translate every requested option that is fully legible in the current frame and avoid repeating unchanged common text. Ask the player to move to the next entry only when its detail panel is hidden, cropped, or too small to read. Do not turn one readable page into one reply per row.

Maintain a temporary session glossary for proper nouns, places, factions, mechanics, and recurring phrases across consecutive translation and strategy questions. It is context, not durable storage: discard it when the live sequence ends unless the player explicitly confirms a term for the personal glossary.

Scan visible text for explicit future triggers while translating. “Collect at the tavern,” “return after obtaining an item,” and similar instructions are not glossary entries; route them through [live-assistance-memory.md](live-assistance-memory.md), shared with navigation and strategy help, when the action is useful and easy to forget.

If the handheld is unavailable, capture fails, or the text has disappeared, accept a user-uploaded photo/screenshot, pasted text, or a manually reopened screen. Do not substitute stale frames as the current screen.

## Answer modes

- **Quick translation** — Natural wording in the player's preferred language; include the source text only where it helps locate or verify the line.
- **Translate and explain** — Translation, the meaning in this game's context, and any immediate mechanic or action consequence.
- **Choices without spoilers** — Translate each option and its tone or intent. Do not reveal hidden outcomes unless the player asks.
- **Term help** — Give pronunciation when useful, ordinary meaning, in-game meaning, gameplay effect, and one short memory cue.

If the player points to “the last word,” “the last two words,” or another narrow phrase, answer that phrase first, then provide one natural full-sentence translation. Do not bury the requested vocabulary inside a long plot summary.

Preserve names, stats, key labels, resource names, and official localized terminology when known. Prefer natural localized wording over word-for-word output, but do not silently change numbers, negation, conditions, durations, or probability language.

## Confidence and spoilers

- Quote no more source text than is needed to anchor the answer.
- Label uncertain readings such as `[可能是…]` and name the obscured region.
- Separate translation from inferred consequence.
- Translating visible dialogue is allowed under the current spoiler preference; hidden future consequences remain spoilers.
- For an irreversible choice, translate first and ask before revealing outcomes.

## Personal glossary

Do not save ordinary translations. Persist a term only when the player explicitly says “记住这个词/以后按这个译法” or confirms that a recurring game-specific term is worth keeping.

Before saving, list matching terms with `game-navigator translation-terms list --game-id steam:APPID --query "TERM" --pretty`. After explicit confirmation, use `game-navigator translation-terms add --game-id steam:APPID --source "..." --zh "..." --meaning "..." --category term --build-id BUILD --pretty`. Store only the term, translation and short contextual meaning—never the whole screenshot or dialogue transcript.

The glossary supports `term`, `phrase`, `ui-label`, and `mechanic`. Treat a stored entry as a personal convention, not proof of an official localization. If new in-game evidence conflicts, explain the conflict and obtain explicit confirmation before submitting a revised entry for the same normalized term.

## Deferred modes

Do not imply phase one provides a handheld shortcut, overlay, speech output, real-time subtitles, background OCR, or continuous screen monitoring. Those require a separate privacy, latency, power, and interaction design and remain deferred.
