# Performance tuning workflow

## Sequence

1. Run `performance-capabilities`. If unavailable, explain the missing reviewed provider; do not substitute a random overlay or executable. If PresentMon reports `access denied`, first resolve the Windows account that actually owns the Play process and compare that exact account with the local `Performance Log Users` group. Do not assume a similarly named SSH or installer account is sufficient. Adding the reviewed interactive Play account is a persistent permission change and needs the user's authorization; after an approved membership change, refresh the Windows logon token by signing out or rebooting before retesting. Do not work around this by leaving Play permanently elevated or widening unrelated permissions.
2. Identify the foreground game/build and choose an experience target before changing settings:
   - twitch/action: 60 FPS, roughly 50+ FPS 1% low;
   - general action/open world: 45 or 60;
   - cinematic AAA: 40, or 45 when that is the available limiter;
   - turn-based/strategy: 30 or 40 with quality priority.
3. Prefer a built-in benchmark. Otherwise agree on a repeatable 30–90 second representative route; exclude loading, cutscenes, first-run shader compilation, and background downloads.
4. Take a no-frame snapshot and verify any `state.adapters` are `observed`; preserve their observation ID, adapter version, BuildID, exact current settings, and warnings. Keep undocumented ASUS/game numeric codes raw. Then capture a baseline and compare average rendered FPS, 1%/0.1% lows, P95/P99/max frame time and stutter percent. Treat displayed FPS and generated-frame percent separately. Without both a settings observation and repeatable scenario, label the result diagnostic rather than baseline.
5. Research the current game build and its exact option behavior. Prefer official material and controlled measurements; community presets are hypotheses until replicated on the target model/build.
6. Change one setting family per A/B test. Repeat close results at least twice.
7. Create a proposed profile. Show the exact diff and ask for confirmation before approval or any write.
8. Default to the player applying it in the live game menu. In a single message, provide the full menu route, all changes in that test family, values that must remain unchanged, the Apply/confirm action, any real restart requirement, and what screen or scenario to return to. One-family A/B isolation does not mean one instruction per turn.
9. After an approved profile is manually applied, capture the same route and report quality loss, performance gain, remaining instability, and whether the target passed.

## Trade-off ladder

Try the least damaging high-return controls first:

1. path/ray tracing;
2. volumetrics, shadow distance, reflections, ambient occlusion, hair/foliage, crowds and draw distance;
3. native in-game FSR/XeSS Quality, then Balanced;
4. 900p/720p plus RSR only if the game lacks a good native scaler; never stack RSR with FSR;
5. lower texture quality only with memory-pressure or texture-streaming evidence;
6. lower the target frame rate before destroying all remaining visual features.

If resolution changes barely help, test CPU-heavy settings rather than assuming GPU limitation. Keep AFMF/game frame generation off for competitive or latency-sensitive play, and use it only with a stable base rendered rate around 40 FPS or higher.

## Device policy

- Identify the exact device, power source and supported manufacturer profile before proposing a baseline; do not assume every handheld supports the same power limit.
- Armoury Crate SE Game Profiles are preferred for supported per-game operating mode, limiter, controls and ROG GPU choices.
- Manual power limits/fan curves, UMA allocation and display-wide changes are separate global decisions requiring explicit review.
- Do not change Windows power plans, sleep, Modern Standby or wake behavior.
- Do not run a generic settings writer. Automated configuration-file application is off by default unless the player explicitly requests it; even then it requires a per-game adapter with backup, read-back, rollback and real-device validation.

## Pass/fail interpretation

- Pass only when the chosen representative route reaches the target with acceptable 1% low and no unexplained long-frame pattern.
- A higher average with worse P99 or stutter can be a regression.
- Frame generation can improve presentation but cannot prove lower input latency.
- One run is not a universal guarantee; keep scenario, build, power source and profile attached to every result.
