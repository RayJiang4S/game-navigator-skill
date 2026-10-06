# Navigator operations

Use:

```bash
GN="${GAMENAVIGATOR_CLI:-game-navigator}"
```

A nonzero exit is a real failure. Read the JSON. Do not guess device state.

## Status and identity

```bash
"$GN" journey --pretty
"$GN" journey --game-id steam:3014320 --pretty
"$GN" status --pretty
"$GN" version-check --pretty
"$GN" devices --pretty
"$GN" current-device --pretty
```

`journey` is the canonical routing result and already includes the pack guard as `packGuard`, so one call per
question is enough. Follow its one `primaryAction`; never claim game help is available when
`needsUserConfirmation` is true. Mention `quietNotice` at most once and continue the original question. A safe Navigator or Play update has no `quietNotice`; do not announce it. Use `status`
for diagnostics only. `status.liveState` is one of `play-reachable`, `play-offline`, `no-play-device`, and
`status.authenticationState` distinguishes `signed-in`, `signed-out`, and temporary `unavailable` verification.

## Login and pairing

```bash
"$GN" login --begin --open --pretty
"$GN" login --wait --id LOGIN_ID --pretty
"$GN" me --pretty
"$GN" account --pretty
"$GN" logout --pretty
"$GN" feedback --category game-request --game-title "某款游戏" --message "希望加上这款游戏" --pretty
"$GN" feedback --category breakage --game-id steam:3014320 --message "这里读不到" --pretty
"$GN" feedback --category quality --game-id steam:3014320 --message "希望这里讲清楚取舍" --pretty
"$GN" feedback --category product --game-id steam:3014320 --game-title "歧路旅人 0" --message "游戏：歧路旅人 0。失败类型：答不上。玩家上一句：我现在该去哪。服务端原因：quest-ids-not-yet-mapped" --pretty
"$GN" pair-same-machine --pretty
"$GN" pair --code 482193 --pretty
# Only when Play cannot reach public Server but Navigator can:
"$GN" pair-local --address https://192.168.1.25:18780 --code 482193 --pretty
"$GN" set-current --device-id DEVICE_ID --pretty
```

Do not log in during the install command. On the first conversation, if `journey.stage` is `recognize-player`, run
`login --begin --open`, speak `prompt`, and give `loginUrl`. If `browserOpened` is false, explicitly ask the player
to open that link on a device they can use; remote/headless hosts must not claim a browser opened. An opened browser
is not a completed login. Then `login --wait --id`. `login --begin` keeps a one-time secret
on this machine, so `login --wait --id` is safe to rerun: if a previous wait was interrupted (for example the turn
ended), the next wait with the same `LOGIN_ID` still completes the same sign-in until the link expires. Only when it
returns `expired` start a new login. If the stage is
`account-check-unavailable`, retain local state and retry later instead of sending the user through login again. The
page can use a six-digit email code or Google (GitHub if the server has it). Do not mention charging. Pairing and signed
feedback use that same account. Play itself does not receive the account session.

When the user asks to change email, bind or remove a login method, inspect/revoke another session, or delete/cancel deletion of the cloud account, run `account --pretty` and give only the returned short-lived URL. The URL lasts about five minutes and is single-use. Do not put account management on the public guide page, do not ask the user to paste codes or credentials into the Agent conversation, and do not confuse cloud-account deletion with deleting the local Navigator SQLite game memory.

Only introduce Play when a request really needs live state. Ask whether the game is on this Windows or another Windows, then show only that branch. On the same Windows, prefer having the Agent run the verified installer, then run `pair-same-machine`; the short-lived handoff is confined to the current Windows profile and loopback address, so the user does not copy a code. On another Windows, ask for only the 6-digit tray code—never an IP, account login, Tailscale setup, or network diagnosis as the normal path. Run `pair`, then verify `devices`, `current-device`, and `status` before saying setup is complete. If Play cannot reach public Server, the code is still usable: let the Agent resolve the approved LAN device/address and use `pair-local`; this relays only the registration envelope through Navigator and verifies Play's presented certificate against the envelope before the normal pinned-TLS claim. The pairing code expires in about ten minutes; if it expired, ask for one fresh code and retry. After a successful pair, the Connector stores the LAN address and a platform-protected Play credential locally and talks to Play directly. The server does not relay frames. An already configured private mesh can carry the same pinned-TLS path, but is optional and must never be replaced with a public Play-port exposure.

New-game wishes may be submitted by Navigator automatically after an exact unsupported title is known. Query
`compatibility` first: an exact `inDevelopmentGames` match is already in the public adaptation roadmap, but the
player's wish should still be recorded as demand and for completion mail. It remains unsupported by the pack guard
until it moves into `games` and receives an installable pack. Existing-pack
`breakage` and `quality` require durable entitlement to that pack. A trial is not eligible and receives 403.
When the Agent cannot answer or refuses, use `product` instead, including for a logged-in trial. Ask once:
「要不要把刚才这件事反馈给游戏领航师？维护者会看到你的原话，并按账号邮箱回复。」 Localize this invitation to the player's language. If they agree, the message includes the
game name, their question, and the failed sentence. Without explicit agreement, do not submit this row or upload
their question. Silence, a topic change or an interrupted turn never grants consent. Already enabled automatic
contribution remains limited to its separately allowlisted structural codes, not raw questions or answers.
Do not attach screenshots, saves, or hardware ids, and do not enable automatic contribution. Do not use
`pack-access-request` as a purchase substitute. Do not expand this into login or payment feedback.

## Purchase

```bash
"$GN" purchase options --pack-id steam-3014320 --pretty
"$GN" purchase start --offer catalog-quarterly --open --pretty
"$GN" purchase start --offer pack-permanent --pack-id steam-3014320 --open --pretty
"$GN" purchase wait --timeout 120 --pretty
"$GN" purchase cancel --pretty
"$GN" purchase status --pretty
```

Offer codes come only from `purchase options`. `start` returns a single-use browser link (`checkoutUrl`), a short
URL plus 8-character `userCode` for another device, and `browserOpened`; the Connector opens only same-origin Server
links and skips remote or display-less sessions. `wait` long-polls the Server's verified order ledger and remembers
the in-flight purchase locally, so a later conversation can resume it without `--id`. The full dialogue rules are in
[purchase.md](purchase.md).

## Live state

```bash
"$GN" snapshot --no-frame --pretty
"$GN" snapshot --frame-out /tmp/gn-frame.png --pretty
"$GN" game-context --purpose shop --pretty
"$GN" game-context --purpose skill-learning --pretty
"$GN" game-context --purpose equipment --pretty
"$GN" game-context --purpose party --pretty
"$GN" game-context --purpose readiness --pretty
"$GN" game-context --purpose construction --pretty
"$GN" game-context --purpose exploration --pretty
"$GN" choice-context --spoilers light --pretty
```

`exploration` and `readiness` policies may include `decisionPolicy.progression`, a reviewed route guide (chapters,
objectives in the game's wording, bosses with spoiler-labelled hints). `progress` joins it with the save: the current
objective when a saved quest ID is mapped, otherwise `unresolvedReason`; `progress.location.trust` says how far the
saved place can be trusted; `progress.answerRules` are the answer rules for this context.

A frame snapshot returns `framePath`: the local image to open (copied to `--frame-out` when given; a directory keeps
the original file name). If no frame was captured, `framePath` is null and `frameUnavailableReason` says why.

Inspect `available`, `unavailableReason`, `decisionPolicy`, `missingCapabilities`, `warnings`, `stateStale`, and `activeBookmarks` before advising. `decisionPolicy` is the Server-authorized, Build- and purpose-specific minimum rule slice; it is not stored as a complete local game guide. A missing capability is unknown, not empty.

## Game Navigation Packs

One game is one independently installable package. The base Navigator installation intentionally contains no
per-game package:

```bash
"$GN" packs list --pretty
"$GN" packs guard --pretty
"$GN" packs guard --game-id steam:3014320 --pretty
"$GN" packs install --pack-id steam-3014320 --pretty
"$GN" packs install --pack-id steam-3014320 --confirm --pretty
"$GN" packs activate --pack-id steam-3014320 --confirm --pretty
"$GN" packs remove --pack-id steam-3014320 --pretty
```

`guard` is mandatory before every new or resumed game-specific sequence. It returns `ready`, `install-required`, `update-required`,
`trial-confirmation-required`, `access-required`, `unsupported`, or `no-game`. New accounts may claim at most three distinct trial packs; the
five-day trial starts independently when each pack is claimed, not at registration or another pack's claim. Server retains existing legacy deadlines. The first download or use of a copied/legacy local pack permanently consumes that
pack's slot, while reinstalling the same pack does not consume another. Never claim that first use automatically:
explain the consequence and wait for the player before using `--confirm`. Install only the pack needed for the current game. `install` verifies the Server-declared size and SHA-256,
replaces the minimal local runtime manifest atomically, and records installation metadata without uploading saves,
screenshots, Steam library data, or gameplay state. `remove` is explicit and removes that runtime manifest; it does
not delete the player's bookmarks, decisions, observations, or Steam history.

### Optional live reading (only when offered)

Some accounts may see a `liveReaderOffer` object in the `packs install` output. It exists only when Server offers an
optional, read-only per-game component to this account; otherwise the field is absent and there is nothing to mention.

- Show its `title` and `body` once, as part of that install confirmation, as an optional extra that is not selected.
- Install only after the player replies with the offer's `acceptReply` word: `reader install --pack-id ID --confirm`.
  Any other reply means it stays uninstalled. Do not raise it again later, even when a question would benefit from live
  state; the player can ask for it themselves.
- Off, on and removal: `reader off|on|uninstall --pack-id ID`; `reader status` shows the state. Removing the pack also
  removes the component.
- Facts with freshness `live-memory-unsaved` came from the running game and may not be saved; label them
  "实时（未存档）" / "live (unsaved)". When the component pauses (game update, protection software, antivirus, revoked),
  answer from the save checkpoint and screen as usual, without extra warnings. Never suggest disabling antivirus or adding
  folder exclusions; the offer text already explains how to allow that one item.

```bash
"$GN" reader status --pretty
"$GN" reader install --pack-id steam-3014320 --confirm --pretty
"$GN" reader off --pack-id steam-3014320 --pretty
```

Navigator builds `game-context` by joining the latest Play semantic snapshot with local SQLite player memory and a
bounded, Build-matched knowledge subset returned by Server after authorization. Full commercial catalogs are not
stored in new user SQLite databases. Play does not own bookmarks or long-term memory. If Play is offline, the command may
return `available=true`, `refreshed=false`, `stateStale=true`: that is the last verified save checkpoint, not the
current unsaved screen. The same Server request must match the exact installed Navigator instance, package version,
game, Build and purpose. A copied manifest, old installation row, or session token alone cannot unlock game guidance.

## Steam, performance and device state

```bash
"$GN" steam library --pretty
"$GN" steam wishlist --pretty
"$GN" steam play-next --limit 12 --pretty
"$GN" steam inventory --pretty
"$GN" steam status --pretty
"$GN" play-capabilities --pretty
"$GN" performance capabilities --pretty
"$GN" performance capture --seconds 30 --warmup 5 --pretty
"$GN" device-state --pretty
```

The first three work from local account memory. The rest require selected Play. Only after explicit user
confirmation in the current turn:

```bash
"$GN" steam open --confirm --pretty
"$GN" steam install --app-id APPID --confirm --pretty
"$GN" steam launch --app-id APPID --confirm --pretty
```

`steam launch` sends at most one Steam URI for the current launch attempt. If it returns
`steam-awaiting-user-or-preparing` or `launch-request-pending`, do not launch again. Poll the read-only status:

```bash
"$GN" steam launch-status --app-id APPID --pretty
```

Continue polling at a modest interval while Steam prepares the game. A repeated `steam launch` is deliberately
idempotent for four minutes. Use `--retry` only after the status is `not-running`, or after the user has visibly
confirmed that the prior attempt failed and explicitly authorizes another launch in the current turn.
If `requiresUserAttention` is true, stop polling and inspect the exact visible prompt when an approved remote-GUI
capability is already available; include `steamTask` only as diagnostic context, not as a guess about the wording.
Normally explain the decision to the player. If the player has explicitly delegated prompt handling in the current
conversation, the Agent may dismiss only a fully observed, non-binding, non-preference informational interstitial
(for example, a controller recommendation), then re-observe immediately. Agreements, permissions, account/login,
save/cloud conflicts, overwrite/delete, difficulty, accessibility, graphics/language/controller preferences,
purchases, mods, character/build/story choices, scarce resources, and unclear prompts always stay with the player.
Play remains sensor-only; do not build or call a generic input endpoint for this.
The duplicate guard survives a Play restart. Ordinary preparation expires after four minutes; explicit user-attention
state remains active only within the same Steam process session. Do not bypass either state after an update by
immediately relaunching.

Play updates are also an explicit mutation. First use `version-check` and inspect the current Play capabilities.
Only when the user confirms the update in the current turn:

```bash
"$GN" play-update --confirm --pretty
```

By default Navigator downloads the exact immutable artifact from the configured HTTPS Server, validates version,
RID, size, SHA-256 and same-origin versioned URL, then streams it to the paired certificate-pinned Play endpoint.
Play stages one bounded package behind an opaque ID; it never accepts a Navigator file path. The installed updater
then independently re-reads the HTTPS manifest and rechecks the package before atomically replacing Play. Expect a
short disconnect and verify `status.liveState=play-reachable` afterward. Use `--direct` only as an explicit fallback
when the game computer itself can reach Server reliably. Do not substitute SSH or a manually copied build when this
managed path is available.

## Memory

```bash
"$GN" bookmarks --pretty
"$GN" bookmarks add --game-id steam:3014320 --title "回旅馆买药" --category return-later --pretty
"$GN" translation-terms list --game-id steam:3014320 --query "Break" --pretty
"$GN" translation-terms add --game-id steam:3014320 --source "Break" --zh "破防" --meaning "..." --category mechanic --build-id BUILD --pretty
"$GN" decisions list --game-id steam:3014320 --pretty
"$GN" decisions add --game-id steam:3014320 --category character-creation --decision "..." --rationale "..." --reversible --pretty
```

Bookmarks live on the Navigator SQLite file, not on the server.

```
Windows: %LocalAppData%\GameNavigator\state.sqlite3
macOS: ~/Library/Application Support/GameNavigator/state.sqlite3
```

## Synthetic Play (offline verification)

```bash
game-navigator-play --fake --listen http://127.0.0.1:18780 --name "协议测试设备" --server "$SERVER"
```

Real screenshot, save, Steam, performance and Armoury reads require a Windows Play host. Synthetic Play is only
for pairing, failure handling and protocol shape; its output is always labeled `synthetic`.
