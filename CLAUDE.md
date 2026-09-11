# Portfolio — working notes for agents

Single-file scroll-jacked site (`index.html`) plus static case-study pages in `work/`. Published on GitHub Pages. Owner: Yucong Zhou (Senior Frontend Engineer, MacroHealth). Yucong commits; don't commit or push unless asked.

## Do not touch: the phone scroll experience (frozen 2026-09-11)
Yucong likes the mobile feel exactly as it is. In `index.html` the gesture code branches on input kind (`gesStart('touch' | 'wheel')`, `ges.kind`, `lastKind`, `'notch'` for a mouse wheel notch). Any scroll-feel change must go in the `'wheel'` / `'notch'` branches and leave these alone:
- touch handlers (`touchstart/move/end`), `DRAG_PAGE .5`, `GO_AT .3`, `FLICK_AT/FLICK_V`, release via `springTo` from the finger's velocity
- the elastic edge constants for touch `EL_W 11 / EL_Z .45`, the bump reference `BH = min(H*.75, W*.5)`
- portrait layout: `placePanel()` puts the gallery / experience panel 28px under the copy and stretches it to 24px above the stage bottom; `.art{top:0;height:auto}` and `.copy{top:calc(var(--ch)*.09)}` in the portrait block keep the heading still when the browser toolbar hides
Desktop wheel/trackpad = the fullPage.js model, restored 2026-09-11 because Yucong found it the best desktop feel: one scene per gesture, `preventDefault`, inertia filtered by the accelerating-average check (`avg(10) >= avg(70)`), input locked until the move lands (`wheelLock = SNAP_MS + WHEEL_COOL`), `animateTo` drives the scroll and the stage follows through its spring (`follow` stays false). No finger-tracked drag and no elastic edge on desktop (`pLag = pWant` when `lastKind !== 'touch'`). Several drag-tracking variants for desktop were tried and rejected as "weird" — don't reintroduce them.

## How the stage works (index.html)
- Scroll position -> `target` -> `cur` (spring, or tight `follow` during a gesture) -> one transition between scene `i` and `bi` (`T0=.04`, `T1=.95`).
- Wheel and touch are intercepted (`preventDefault`). Touch: one page per gesture, the page tracks the finger, releases to the nearest page past `GO_AT`. Wheel: one scene per gesture, fullPage style (see above). Inner lists (`.gscroll`, `.xp .body`) scroll natively and hand over at their edge.
- Live wipe (`#wipe2`, z 1) sits between the outgoing page (z 0, fades `1-tt`, lifts no further than the wipe) and the incoming page (z 2, `clip-path` to the wipe shape). Settled colours live in `#wipe`. Elastic band: `pt` (finger) vs `pLag` (baseline), slack drawn as the dome; `wipeD(..., dm)` blends dome -> wave.
- Nav-dot jumps across 2+ pages draw one direct transition (`startJump`). Keyboard still uses the spring + idle snap (`SNAP_AT .35`).
- Syntax check after edits: `node -e "const fs=require('fs');const html=fs.readFileSync('index.html','utf8');new Function(html.match(/<script>([\s\S]*?)<\/script>/)[1])"`

## Case pages (work/*.html)
- Shared template. Keep heroes SHORT so the thumbnail -> hero view-transition morph lands inside the viewport: title on one line (design-system page has its own smaller h1 clamp), lede ~330-360 chars, phones hide `.k .xs`.
- Fixed "sections" pill beside the back pill is built from the numbered `<section>`s (`.num`, `.k`, `h2`); don't hand-maintain it.
- Grid tracks use `minmax(0,1fr)`; `figure{min-width:0}`, `pre.code{min-width:0}`, `html{overflow-x:clip}` — a long code line once made the design-system page scroll sideways on phones.
- Gallery thumbnail `img[data-vt]` and hero `.heroimg .frame` share a `view-transition-name`; names are set only during navigation (`pageswap`/`pagereveal`).

## Confidentiality (Yucong's rules, non-negotiable)
- No employer/client logos or marks anywhere, current or former (MacroHealth, Bell/BluePrint, HealthWave/Fullscript). Pixelate them in screenshots; plain type instead of logo covers.
- Design-system case study: no registry URLs, node/file ids, business logic, or real marketplace mockups; "What comes out the other end" shows component/Storybook images only. Token sync is skill-based (no Tokens Studio). Don't mention "seven weeks"; describe what the repo contains.
- No Behance links.

## Working style
- Yucong tests on device and reports back; don't spend time on Chrome DevTools verification unless asked or debugging something unseen. Run the syntax check instead.
- Small numeric tweaks are welcome iterations ("make it lazier", "reduce for mobile"): change one constant, say which, keep the other platform untouched.
