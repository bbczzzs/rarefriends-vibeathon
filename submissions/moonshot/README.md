# Moonshot 🚀

**Category:** Token Activity
**Builder:** Ishan — GitHub: [bbczzzs](https://github.com/bbczzzs) · X: TBD
**One-sentence pitch:** A crash-style multiplier game where your Rare Friend pilots a rocket — stake RF, ride the multiplier as high as you dare, and cash out before it crashes; every crash burns the stake.

## Demo

🎮 **Play it:** https://bbczzzs.github.io/moonshot/ (public simulated preview on GitHub Pages — no wallet needed to view)

## Source

https://github.com/bbczzzs/moonshot — game lives in `games/moonshot/`

### Run it locally

- Node.js 22+
- `npm install`
- `npx friendsdk dev games/moonshot` — local preview (mock wallet + sample Friends)
- `npx friendsdk build games/moonshot --outdir dist` — static build, host `dist/` on any HTTPS static host

**Stack:** FriendSDK v0.1.2 · React 19 · Canvas 2D · WebAudio. All art drawn in code, all sound synthesized — zero external assets.

## How to play

1. The runtime connects your wallet and you pick your Friend — they're the pilot.
2. Choose a stake: 10 / 25 / 50 / 100 RF, or a custom amount. You start with 1,000 demo RF.
3. Hit **LAUNCH**. The rocket climbs and the multiplier rises from 1.00x, drawn live as a burning comet trail.
4. Hit **CASH OUT** (the button, tap the sky, or Space) before the crash to win `stake × multiplier`.
5. Crash first and the stake **burns** — watch the 🔥 Burned ticker climb.
6. **FLY AGAIN** — rounds take ~15 seconds, restart is instant.

## RF costs, probabilities, rewards

- Stakes are player-chosen (min 1 RF). Starting balance: 1,000 demo RF, simulated.
- Crash point: `U ~ Uniform[0,1)`; if `0.97/(1-U) < 1` → `1.00x` (exactly 3% of rounds — "instant crash"); otherwise `max(1.01, floor(0.97/(1-U) × 100) / 100)`.
- `P(C ≥ m) = 0.97 / m` for `m ≥ 1.01`. **House edge: 3%** — any fixed cash-out has EV 0.97 per 1 RF staked.
- Growth: `m(t) = e^0.1t` → 2x at ~7s, 10x at ~23s.
- Cash out at `m` → receive `stake × m`. Crash → stake burned, added to the session burn ticker.
- No consumables. **All RF is simulated demo RF**, labeled as such everywhere in the UI — no real funds move in this build.

## Checks / tests

- `node verify-sdk-math.mjs` — 200,000 simulated rounds: 3.003% instant crash, mean return ≈ 0.97 at 1.5x / 2x / 5x / 10x cash-outs.
- `node test-interaction.mjs` — full round in headless Chromium (launch → countdown → cash out / crash → fly again), runtime pause lifecycle, mute + reduced-motion toggles. PASS.
- `friendsdk check` — valid. `friendsdk test` — PASS, no console errors.
- Screenshots reviewed at desktop (1280px) and mobile (360px/390px): no overflow, readable at all sizes.

## Known issues

- The pilot is the player's canonical on-chain Friend sprite (16x16, idle-up frames), voxel-rendered and animated; when the sprite read is unavailable (offline/sandbox), a deterministic generative pixel pilot is used instead. The topbar still labels "Pilot · Friend #id".
- `game.json` carries a placeholder chance-game definition required by the runtime schema; the crash game doesn't use the chance-game economy actions.
- Session stats reset on reload — persistence APIs are still on the Rare Friends roadmap.

## Asset credits

All scene art drawn in code (Canvas 2D voxel/chunky-pixel style), all audio synthesized live (WebAudio). The pilot is the player's own selected Friend's canonical on-chain sprite (their NFT, rendered client-side). Pixel font is Press Start 2P (OFL-licensed, bundled as woff2).

## Why Token Activity

Every round is an RF spend event and every crash is a burn event — the highest-frequency burn loop we could design: a full stake → cash-out-or-burn cycle every ~15 seconds. The always-visible session dashboard (RF staked / RF burned / rounds played) is the activity ledger. The crash math and ledger live in an isolated, documented `economy.ts` module, ready to point at official on-chain plumbing when the Rare Friends team ships it.
