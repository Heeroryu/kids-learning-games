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
- **Works on keyboard AND touch** (computer or iPad). The **glowing target is tappable** = same as
  pressing the key: in Spell-It tap the big letter / the glowing tile; in Math/Color tap the glowing
  number-row button / the glowing tile. Keep this dual input in every game (refactor input into a
  `pressX()`/`typeLetter()`/`handleLetter()` fn that both `keydown` and `onclick` call).
- **Mobile/iPad layout:** body height uses `100vh; height:100dvh;` (dvh = correct visible height on
  iOS so nothing is hidden behind the toolbar). Screens are scroll-safe (`overflow-y:auto` +
  `justify-content:safe center`) with fluid `clamp(..,vh,..)` spacing → scales to any resolution.
- **Can't-fail UX:** the next correct input **glows orange**; wrong keys just **wiggle** (shake),
  never penalize. This is the core design rule — preserve it in every game.
- **Audio kit** (in `sounds/`, reused from the little_paws_puzzle game): `sfx('correct')` =
  `piece_snap.wav` on each correct input, `sfx('win')` = `victory.wav` on completion. Optional
  background music (🎵 button) cycles off→music_1→2→3→off, saved in `localStorage['kg_music']`,
  armed on first user gesture (autoplay rules). Master 🔇 mute silences voice + sfx + music.
- Single-digit math only (**0–9**, one keypress). Going to 10+/teens breaks the single-key model.

## Shared conventions (keep consistent across all games)
- **Voice:** Web Speech `speechSynthesis`. `scoreVoice()` ranks voices (prefers Edge "(Natural)"/
  neural, then Google, then friendly female names; penalizes robotic David/Mark). `say()` for
  prompts, `cheer()` (higher pitch/rate) for praise. Voice picker + 🔊 Test on each start screen.
  **Voice choice is shared** across all games via `localStorage['spellit_voice']`.
  Best voices need **Microsoft Edge** — this is recommended in each game's in-app hint.
- **Guided typing tiles:** word rendered as tiles, `.next` tile glows, correct fills green & advances,
  wrong shakes. Spell-It and the Math/Color word steps all reuse this mechanic + `spellOutHint()`.
- **Two-part "guess → reveal → do it" intro — STANDARD for EVERY game (incl. all future ones):**
  when a new item is introduced, **Part 1** shows the thing (emoji / swatch / digit) + "Can you guess
  what this is?" with the answer hidden (tiles not built / number row not glowed) and **input ignored**
  via a readiness flag (`spellReady` in Spell-It & Color-Fun, `ready` in Math-Fun). After a **~1 second
  pause** (`setTimeout(..., 1000)`), a reveal fn (`revealSpell` / `revealColor` / `revealNumber`) says
  "Yes! It's X! Let's spell it / Press X", shows the tiles / glows the answer, and flips the flag true.
  Spell-It & Color-Fun apply it to the spelling screen; Math-Fun applies it to the number lesson step.
  The 🔁 repeat button is flag-aware (re-asks the guess before reveal, repeats the answer after).
  **Any new game must include this same guess→reveal→do pattern with the ~1s pause.**
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
