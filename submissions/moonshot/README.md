# Moonshot 🚀

**Category:** Token Activity
**Builder:** Ishan · GitHub [bbczzzs](https://github.com/bbczzzs) · X: TBD
**One-sentence pitch:** A live crash game where your Rare Friend pilots a rocket carrying a crew of real Generations Friends, and **10% of every stake burns as rocket fuel on every launch, win or lose**, with a fresh launch every ~15 seconds.

![Moonshot gameplay](https://raw.githubusercontent.com/bbczzzs/moonshot/76b034c/media/moonshot-demo.gif)

## Demo

🎮 **Play:** https://bbczzzs.github.io/moonshot/ (GitHub Pages)

- **Requirements:** a browser wallet on **Robinhood mainnet (4663)** holding a hardwired Rare Friends Generations NFT (generation ≥ 1). This is the SDK's standard ownership gate, the same as every SDK entry. Connecting only reads: no RF, no signatures, no transactions.
- **No wallet?** The GIF above and the [full recording (MP4)](https://github.com/bbczzzs/moonshot/blob/main/media/moonshot-demo.mp4) show real gameplay.

| Liftoff | Flight | Crash | Hangar |
|---|---|---|---|
| ![Liftoff](https://raw.githubusercontent.com/bbczzzs/moonshot/76b034c/media/liftoff.png) | ![Flight](https://raw.githubusercontent.com/bbczzzs/moonshot/76b034c/media/flight.png) | ![Crash](https://raw.githubusercontent.com/bbczzzs/moonshot/76b034c/media/crash.png) | ![Hangar](https://raw.githubusercontent.com/bbczzzs/moonshot/76b034c/media/hangar.png) |

## Source

https://github.com/bbczzzs/moonshot. The game is in `games/moonshot/` (full rules, math and checks in its [README](https://github.com/bbczzzs/moonshot/blob/main/games/moonshot/README.md)).

**Look:** drawn in the Rare Friends world style with the FriendSDK game palette (meadow, pond, sun, coral, lilac, signal), ink outlines and dither shading, on a floating meadow island. The UI follows the SDK frame (paper, ink, square corners, hard shadows).

**Stack:** FriendSDK **v0.1.2** (runtime, wallet/Friend selection, ownership gate, `createFriendReader` sprites) · React 19 · Canvas 2D pixel renderer · WebAudio. All art is drawn in code and all sound is synthesized.

```sh
git clone https://github.com/bbczzzs/moonshot && cd moonshot
npm install
npx friendsdk dev games/moonshot                    # local preview (real wallet gate)
npx friendsdk build games/moonshot --outdir dist    # static build
```

## How to play

1. **Boarding (6 s):** crew Friends hop onto the rocket's outrigger seats. Pick a stake and hit **Join this launch** (or Space). Your Friend climbs into the cockpit dome. You can cancel for a full refund until liftoff.
2. **Liftoff:** fuel burns, and the multiplier climbs as `m(t) = e^(0.12t)` (2x at ~5.8 s, 10x at ~19 s). You pass the Moon at 2x, Mars at 5x, Saturn at 10x, and beyond.
3. **Eject:** press **Cash out**, Space, or tap the sky to parachute out with `ride × multiplier`. Crew eject at their own targets.
4. **Crash:** everyone still aboard burns with the rocket.

**Auto eject** (target multiplier) and **Auto-launch** (5 / 10 / 25 / 50 / ∞ rounds) keep the loop running hands-free. **M** toggles sound. There's a reduced-motion toggle. The runtime's pause freezes the flight without forfeiting anything.

## RF costs, probabilities, rewards

**All RF is simulated demo RF** (1,000 to start), labelled in the UI. No real funds move.

| | Rule |
|---|---|
| Stake | ≥ 1 RF, player-chosen |
| **Fuel burn** | **10% of every stake, burned at liftoff, every launch, every pilot** |
| Ride | The other 90% of the stake |
| Crash point | `U ~ Uniform[0,1)`, `C = floor(100/(1−U))/100` (the highest multiplier reached) |
| Odds | `P(C ≥ m) = 1/m` for two-decimal targets m ≥ 1.01. About 1% of launches bust at 1.00x |
| Payout | Eject at m → `ride × m`. The expected return at any target is **exactly 90%** of stake |
| House edge | 10%, **all of it burned**. The house keeps nothing |
| Crash | Riders aboard lose their ride to the **Launch Pool**, which pays ejectors (zero-sum in expectation) |
| Hangar | 5 rocket skins and 4 exhaust trails (free to 300 RF), **100% burned**, cosmetic only, no odds change |
| Consumables | None |

## Why Token Activity

- **Frequency:** every launch is a spend event for every pilot aboard and a guaranteed burn event. A launch runs about every 15 seconds, and auto-launch repeats it without input.
- **Volume:** 10% of all staked volume is burned. Hangar purchases add a second, 100% burn sink with no prize liability.
- **Visible:** a session burn counter in the top bar, plus a **Furnace** card showing RF burned per minute and fuel per launch. Per-launch fuel and Launch Pool totals appear in the crew panel.
- **Crowd:** a crew of up to 8 real Generations Friends (canonical on-chain sprites) bets in every launch alongside you (simulated in this preview). At launch these seats would be real holders, and every one of them burns fuel.

## Checks

- `node verify-sdk-math.mjs`: 500,000 simulated launches. Instant bust 1.00%. Returns 89.97% / 90.08% / 89.99% / 89.68% at 1.5x / 2x / 5x / 10x. Fuel burn 10.00% of volume. Launch Pool balanced. Ledger and hangar arithmetic asserted. **PASS**
- `node test-interaction.mjs 960` and `390`: in the real sandboxed runtime (SDK mock wallet), covering joining during boarding, liftoff, pause freeze mid-flight, resume, eject (or a valid crash), history, a hangar purchase burning exactly 25 RF, auto-launch, and the sound toggle. **PASS**, no browser errors
- `npx friendsdk check games/moonshot`: valid. `npx friendsdk test games/moonshot --width 1200` and `--width 360`: **PASS**

## Known issues

- Crew stakes and targets are simulated; the SDK has no multiplayer.
- `game.json` holds the placeholder chance-game definition the runtime schema requires. Moonshot uses its own documented ledger (`economy.ts`), not the chance-game actions. Going live needs a round contract: a fuel `burn()`, a pool that escrows rides and pays ejectors, and a Dice/VRF crash seed committed before boarding closes.
- Balances, cosmetics and history reset on reload (no SDK storage).
- If the pilot's art can't be read (slow RPC), a labelled stand-in sprite is shown.
- The GIF and screenshots come from a local harness mounting the same game component (the wallet screen isn't shown). Automated tests use the SDK's mock wallet. A real-wallet playthrough of this version is pending.

## Asset credits

All scenery, rocket, planets, particles and UI are drawn in code. All audio is synthesized with WebAudio. Friend sprites are canonical Rare Friends Generations artwork via FriendSDK (see its `NOTICE.md`). Fonts: Silkscreen, Sometype Mono and Archivo (all SIL OFL, bundled).
