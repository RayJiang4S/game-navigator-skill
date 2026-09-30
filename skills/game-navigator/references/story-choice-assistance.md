# Story-choice assistance

Use this workflow when dialogue, a moral choice, a relationship response, a faction decision, or another branch may change later events.

## Observe one decision point

1. Run `game-navigator choice-context --spoilers <none|light|outcome|route> --pretty` while the choice is visible.
2. Inspect the returned current frame, Build, current structured facts, active bookmarks and prior confirmed decisions together.
3. Transcribe only the visible options. If text is cropped or uncertain, say so and ask for one bounded UI observation rather than inventing the missing words.
4. Match consequences only against Build-scoped reviewed game facts. Web claims remain claims until corroborated; separate official, game-file, repeated independent, and unverified evidence.
5. Explain in this order:
   - what each option means now;
   - the values or role-play position it expresses;
   - whether it appears reversible, missable, timed, or resource-consuming;
   - verified immediate effects;
   - later effects only to the requested spoiler level;
   - what is still unknown.
6. Recommend an option only after stating the player's likely objective and the important tradeoff. The player always performs the choice.
7. After the player confirms what they chose, record it with `game-navigator decisions add --game-id GAME_ID --category story-choice --decision "..." --rationale "..." [--reversible] --pretty`.

## Spoiler levels

- `none`: explain wording, tone and represented values; reveal no consequences.
- `light`: add reversibility, missable/timed risk and broad consequence category, without revealing later scenes.
- `outcome`: include direct verified consequences of this choice.
- `route`: include verified downstream branch and ending implications when the player explicitly asks.

Never treat a popular guide as authoritative by popularity alone. Never turn a possible consequence into a guaranteed one. If the current frame is unavailable, do not pretend to know the visible options.
