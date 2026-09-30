# Purchase inside the conversation

Use this when the pack guard returns `access-required`, when `packs install`/`activate` is refused for access, or
when the player asks about plans, renewal, expiry or refunds. Every price, saving, reason and sentence comes from the
Connector's Server response. Never quote a price, discount or policy from memory, and never invent a plan the
response did not return. You may translate the Server's sentences into the player's language without changing
numbers, dates or conditions.

## 1. Explain and offer

```bash
"$GN" purchase options --pack-id PACK_ID --pretty      # or --game-id steam:APPID; add --locale en for English text
```

- Say `headline` first (why help stopped: `reason` is `trial-available`, `trial-expired`, `trial-picks-used`,
  `membership-expired`, `renewal-payment-failed`, `refunded`, `pack-locked`, or `has-access`).
- `renewal-payment-failed`: a subscription renewal charge failed and the provider is still retrying. Give the
  update-payment link from `headline`; do not offer another subscription or time card (the Server refuses them to
  avoid a double charge). A single-game purchase is still allowed if offered.
- `trial-available`: offer the trial first and follow the normal trial confirmation (`packs install --confirm`).
  Do not show prices unless the player asks.
- `purchasable=false`: say `notPurchasableMessage` and stop. It is a real next step (trial, or the inviter/admin
  path), not a dead end; do not look for another way to buy.
- `purchasable=true`: list the `available` offers in the returned order: subscriptions first with `perMonthDisplay`
  and `savingsNote`, then time cards (one-time, Alipay), then the single-pack option if present. Add the `guidance`
  lines (subscription is cheaper; Alipay → time card; pay on a computer; an error page after paying is not a failure).
  Do not pick a plan for the player; if they say they have no international card or want Alipay, point to time cards.
  Mention an unavailable offer only with its `unavailableReason`.
- If `pendingPurchase` is present, ask whether that payment was completed before starting another one.

## 2. Hand off to payment

After the player names one plan:

```bash
"$GN" purchase start --offer OFFER_CODE [--pack-id PACK_ID] --open --pretty
```

- `--pack-id` is required only for `pack-permanent`; catalog plans cover every game, so the Server ignores it there.

- Always pass `--open`. If `browserOpened=true`, say the payment page opened in their browser. Otherwise (remote,
  headless or another device) give `purchase.checkoutUrl`, and also `purchase.shortUrl` with `purchase.userCode` for
  typing on a phone or another computer. The link works once, in one browser, for about 15 minutes.
- The player pays on the provider's page. Never ask for card numbers, passwords, or verification codes in the chat.
- `outcome=pending-other`: a different plan is already in progress. Ask whether that one was paid. Only after a clear
  "not paid" run `purchase start ... --replace`. Asking for the same plan again simply refreshes its link.
- `outcome=sale-closed`, `unavailable`, `already-subscribed`, `auto-renewing`, `already-owned`: say `message`; rerun
  the pack guard when it says access already exists.
- `outcome=renewal-payment-failed`: say `message` (it carries the update-payment link) and stop; do not retry
  another catalog plan.
- `error=login-required`: run the normal `login --begin` / `login --wait` flow, then repeat `purchase options`.

## 3. Wait for the Server, then continue

```bash
"$GN" purchase wait --timeout 120 --pretty
```

- Run it right after the hand-off and again whenever the player says they paid. Only `status=completed` means paid;
  never trust a browser page, a screenshot, or the player's word as proof of payment.
- `completed`: say `message`, rerun `packs guard`, install the pack if needed (no trial confirmation), and continue
  the player's original request without asking them to repeat it.
- `awaiting-open` / `opened`: say `message`. If the player reports an error page after paying (including a phone
  page that cannot open `https`), reassure them it does not mean the payment failed and tell them not to pay again;
  keep waiting or ask them to come back later. A new conversation can resume with `purchase wait`.
- `expired`, `failed`, `canceled`, `refunded`: say `message` and follow `nextAction`
  (`offer-new-link`, `retry-later`, `contact-support`, `choose-again`, `show-options`, `recheck-access`).
- The player cancels: `purchase cancel`. Say that a payment already made will still take effect.

## 4. Renewal, expiry and refunds

- A guard or journey `accessNotice` / `quietNotice` about expiry, a stopping subscription or a failed renewal
  payment: mention it once per conversation (with its link), then continue.
- `purchase status` returns `membership` (`state`, `endsAt`, `autoRenew`, `notice`) and any `pendingPurchase`.
- Refunds, stopping or resuming renewal, and order history happen in the account center: run `account --pretty`
  and give the one-time link. You may summarize the rules the Server states (one-time purchases: 7-day full-refund
  request window; subscriptions stop at the period end and the current period is not refunded). Never promise a
  refund outcome.
