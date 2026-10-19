# Steam account, device and store

Keep three evidence sources separate:

1. Navigator local SQLite: the last complete owned-library, playtime, wishlist and user-confirmed disposition snapshots. These work while Play is offline.
2. Play: games installed on the selected Windows device, exact BuildID and bounded Steam actions.
3. Current Steam store/OpenCLI or a visibly logged-in browser: current CN price, sale, language, release state and personalized pages.

## Local account view

```bash
GN="${GAMENAVIGATOR_CLI:-game-navigator}"
"$GN" steam library --pretty
"$GN" steam wishlist --pretty
"$GN" steam play-next --limit 12 --pretty
```

`steam play-next` returns `continueNow`, `returnCandidates`, `newFromLibrary`, and `explicitlyExcluded`.
The score only sorts facts inside a bucket; it is not a taste model. Review a short list against genre,
session length, language, difficulty, handheld fit and the user's current mood.

Playtime proves activity, not progress or preference. Never turn high hours into “liked,” low hours into
“disliked,” or a gap into “abandoned.” Only a user-confirmed disposition can do that. When the latest
account snapshot is old, give its observation time and do not claim the library or wishlist is current.

## Selected Windows device

```bash
"$GN" play-capabilities --pretty
"$GN" steam inventory --pretty
"$GN" steam status --pretty
```

`inventory` reads installed Steam manifests from Play; it requires that device online. The following actions
are bounded but state-changing and therefore require the user's exact confirmation in the current turn:

```bash
"$GN" steam open --confirm --pretty
"$GN" steam install --app-id APPID --confirm --pretty
"$GN" steam launch --app-id APPID --confirm --pretty
```

After one launch request, poll `"$GN" steam launch-status --app-id APPID --pretty` until
`game-process-confirmed`, `not-running`, or a visible decision. Do not resend a launch because the game is large.
`--retry` overrides the four-minute guard only after a visible failure and fresh confirmation. Stop when
`requiresUserAttention=true`. Do not read `steamTask` as an agreement, cloud conflict, or launch option unless the
visible UI says so. Dismiss only the interstitial SKILL.md allows, then re-observe. Play stays sensor-only.

Steam must open in Big Picture / Gamepad UI on a handheld. Never silently add `--confirm`; never buy,
refund, uninstall, trade, post, change a wishlist or handle Steam Guard.

## Public store through OpenCLI

Inspect the installed adapter before relying on it:

```bash
opencli steam --help -f yaml
opencli steam search 'game name' --limit 10 --currency cn -f json
opencli steam app APPID --currency cn -f json
opencli steam top-sellers --limit 20 -f json
```

The public Steam adapter does not need a login. `top-sellers` is discovery only. Deduplicate by AppID and
confirm exact regional offer with `steam app APPID --currency cn` or the current product page.

If a logged-in wishlist/profile page is required, use the user's visible browser session. Verify the rendered
account name, read only the page DOM, and stop if signed out. Never inspect cookies, storage, Steam Guard,
password fields, payment details or chat. An incomplete page never replaces the last complete local snapshot.

## Recommendation source order

1. Ownership and playtime from the latest complete local account snapshot.
2. Installed status and BuildID only from selected Play or its timestamped local cache.
3. Wishlist membership from the latest complete wishlist snapshot.
4. Current offer, reviews, language, online requirements and handheld compatibility from live store evidence.
5. Popularity and personalized discovery only as candidate generation.

Never recommend buying an already-owned AppID. If offer data is stale, call it wishlisted without claiming a
current discount. Use an owned-library rail by default; add at most one or two external discoveries only when a
new release is unusually well matched, a verified sale materially changes the decision, or it fills a clear gap.
Classify external titles as “buy now,” “wishlist/watch,” or “skip,” and explain why despite the existing backlog.
