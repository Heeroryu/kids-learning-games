# Kids Learning Games

Three tiny offline learning games for a **3.5-year-old** (the owner's daughter), plus a launcher.
Pure **HTML/CSS/JS, one self-contained file each, no build, no dependencies.** Each opens by
double-click (works offline) **and** is hosted on GitHub Pages.

## Files
- `index.html` — **launcher** (entry point). Three cards → the games. Click a card or press **1 / 2 / 3**.
- `Spell-It.html` — letters & words A–Z (guided spelling).
- `Math-Fun.html` — numbers 1–9, lesson-first: press the digit → spell the word → counting/adding games.
- `Color-Fun.html` — 10 colors: see the swatch → spell the color word.

## Audience & hard constraints
- **Keyboard-only on a computer.** No touch/tablet support required. Don't add on-screen letter
  buttons for the spelling/word steps (the glowing tile shows which key to press). The math/color
  picker tiles are clickable, which is harmless.
- **Can't-fail UX:** the next correct input **glows orange**; wrong keys just **wiggle** (shake),
  never penalize. This is the core design rule — preserve it in every game.
- Single-digit math only (**0–9**, one keypress). Going to 10+/teens breaks the single-key model.

## Shared conventions (keep consistent across all games)
- **Voice:** Web Speech `speechSynthesis`. `scoreVoice()` ranks voices (prefers Edge "(Natural)"/
  neural, then Google, then friendly female names; penalizes robotic David/Mark). `say()` for
  prompts, `cheer()` (higher pitch/rate) for praise. Voice picker + 🔊 Test on each start screen.
  **Voice choice is shared** across all games via `localStorage['spellit_voice']`.
  Best voices need **Microsoft Edge** — this is recommended in each game's in-app hint.
- **Guided typing tiles:** word rendered as tiles, `.next` tile glows, correct fills green & advances,
  wrong shakes. Spell-It and the Math/Color word steps all reuse this mechanic + `spellOutHint()`.
- **Top bar (every game):** 🏠 home → navigates to `index.html` (the launcher); ⬅️ back → steps back
  one screen through the screens actually viewed (in-app history stack: `backStack`/`content`,
  `pushForward()`; back from a 🎉 win re-shows that item; empty stack → launcher); 🔊 mute; 🔁 repeat.
- Emoji visuals (no image files), confetti on success, progress bar, a start-screen **picker**
  (letters / numbers / color swatches) styled as rounded "candy" tiles.
- **Progress saved** per game: `localStorage` keys `spellit` (Spell-It `li,wi`), `mathfun_n`,
  `colorfun_i`. Picker/Play "jumps" reset the back stack.

## Deployment
- Repo: **https://github.com/Heeroryu/kids-learning-games** (public). Live:
  **https://heeroryu.github.io/kids-learning-games/**
- This folder is the git repo; `origin` is the clean HTTPS URL (no embedded token).
- **To publish changes:** commit + `git push` → GitHub Pages auto-rebuilds (~30s). Push auth goes
  through Git Credential Manager (browser prompt) — the one-time deploy PAT was deleted.
- **Never commit a token / secret.**

## Working on this locally (verification)
- Serve with a static server and drive in the browser to verify (the parent `Downloads/.claude/
  launch.json` has a `spellit` config = `python -m http.server 8077` at the Downloads root; the
  games are then at `/Kids%20Learning%20Games/<file>`).
- Flow-test by dispatching `keydown` events and reading screen state via `preview_eval` — reliable.
- **Known gotcha:** `preview_screenshot` often times out on these pages because of the infinite
  glow/bounce CSS animations (and TTS). It's a capture quirk, not a bug — verify via eval instead.
  Start screens (no looping animation) usually screenshot fine.
- **Known gotcha:** large single-file `Write` has truncated silently before (missing closing
  `</script></body></html>`), which makes the whole script fail with no console error. After writing
  a big file, verify it ends with `</html>` and the `<script>` parses.

## Pending / ideas (not yet built)
- Full visual refresh (rounded kid font like Baloo 2/Fredoka, nicer header/top bar, card launcher).
- "Add to Home Screen" polish: app icon + offline service worker (PWA).
- Math beyond 9 (needs multi-digit entry → rethink can't-fail), subtraction; real photos vs emoji.
