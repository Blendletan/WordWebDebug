# WordWeb UI Refresh — Implementation Spec

**Site:** https://blendletan.github.io/WordWeb
**Stack:** static GitHub Pages site — vanilla HTML/JS/CSS, D3.js force-directed graph, shared `bubble-theme.js` module for brand tokens (Fraunces / Libre Franklin / Courier Prime; cream / ink / red / teal / gold palette).

## Goal
Simplify the share flow, add a Listdle rating badge, and make the support/feedback links genuinely visible — all without the page feeling promotional. No changes to puzzle logic, scoring, or the graph itself. Locate the relevant files yourself (likely `index.html` plus whatever JS module currently builds the share UI and page footer) and adapt this spec to match what's actually there.

## Design principles (apply throughout)
1. **One ask per moment.** Never stack multiple asks (share + rate + support + cross-promo) into a single button, modal, or line.
2. **Calm, not loud.** Secondary links (feedback / support / rate / cross-promo) are plain text or small icon+label pairs, all the same visual weight as each other — no color-shouting buttons, no banners competing with the puzzle.
3. **Respect the dismiss.** Any modal is easy to close (X, tap-outside, Esc), never blocks replay, never re-appears in a way that reads as nagging.
4. **Reuse existing brand tokens.** Fonts, palette, and `bubble-theme.js` values only — no new colors or fonts introduced for this work.
5. **Fewer choices, higher completion.** This is the reasoning behind simplifying share below — same logic as the rest of the design work, applied to UI.

---

## 1. Simplify the share flow
- Remove the download-image option entirely.
- Remove the share-image option entirely.
- Replace with a single **"Share result"** action:
  - If `navigator.share` is available, use it, passing the existing generated share-text string as the payload.
  - Otherwise, copy that same text to the clipboard and show a small inline confirmation (e.g. "Copied!") — no separate modal or page for the fallback.
- Reuse whatever share-text template already exists (par, words used, puzzle #/date, link). This is a plumbing simplification — fewer buttons, one code path — not a copy rewrite, unless Robert separately wants to revisit the text itself.

## 2. Completion / result modal
- If a "you solved it" overlay already exists, trim its contents. If it doesn't exist yet, this section describes what to add.
- Contents, and only these:
  - The result (par vs. actual, words used — whatever's already shown elsewhere in the UI)
  - One primary button: **Share result**
  - A close control (X and/or tap-outside)
- Do **not** add support / feedback / rate / cross-promo links inside this modal. The footer (below) carries those, is visible the instant the modal closes, and doesn't need to be duplicated here.
- Style: match the existing tutorial-slideshow modal treatment (corner radius, cream/ink palette). Prefer a soft fade/scale-in over an abrupt pop.

## 3. Footer redesign — visibility, not volume
Replace the current inline, sentence-style footer with an evenly-weighted row of links, in this order:

1. **Send feedback** — existing mailto link
2. **Rate on Listdle** — badge, see §4
3. **Support me ☕** — existing PayPal.me link
4. *(optional)* **More puzzles: SpellSweep** — see §5

Layout:
- Desktop: one horizontal row, evenly spaced, with clear gaps or thin dividers between items — not embedded in a paragraph of prose.
- Mobile: stack or wrap to two rows; keep tap targets comfortably sized (44px+ tall).
- Add a thin top rule above this row, separating it from the puzzle content as its own footer zone.
- Same type size/weight across all items (Libre Franklin, existing small/UI text size). If you add an icon to one, add icons to all — consistency over any single item standing out.
- Color: teal or ink for these links. Reserve red/gold for in-puzzle feedback, where they already carry meaning.

## 4. Listdle badge
Official embed reference: https://listdle.com/badge/ (three variants: Dark — Listdle's own recommended default, Light, Compact).

- Use the **Compact** (32px) variant sized to match the footer's line height, so it sits at the same visual weight as the plain-text links beside it. Check it visually against the actual cream background before locking in — swap to Dark or Light if Compact looks off.
- Snippet shape:
  ```html
  <a href="https://listdle.com/games/YOUR-GAME-SLUG" target="_blank" rel="noopener">
    <img src="https://listdle.com/badges/rate-on-listdle-compact.svg" alt="Rate on Listdle" height="32">
  </a>
  ```
- **Blocker:** needs Robert's actual Listdle slug for WordWeb. If WordWeb isn't listed yet, submit at https://listdle.com/submit first, then drop the slug in once approved.

## 5. Optional: SpellSweep cross-link
- Only include if it doesn't push the footer past 4 items or make the row read as a promo bar.
- Same treatment as every other footer link — same size, same weight, no thumbnail, no "NEW" tag.
- Label: "More puzzles: SpellSweep" or "Try SpellSweep →", linking to https://blendletan.github.io/SpellSweep/.
- Do not place this in the completion modal — that moment is about the puzzle just solved, not a pitch for a different game.

## 6. Explicit don'ts
- No modal that fires on every completion with escalating asks.
- No auto-playing sound or attention-grabbing animation on the completion modal.
- No more than one strongly-styled button anywhere in this flow ("Share result"). Everything else is a plain link.
- No stacking rate + support + cross-promo into the same modal or line.
- No new colors or fonts outside the existing brand system.

---

## Open questions for Robert (resolve before or during implementation)
- [ ] Listdle slug for WordWeb — has it been submitted / approved yet?
- [ ] Badge variant: Compact vs. Dark vs. Light — confirm after a visual check on the live page.
- [ ] Keep "Support me ☕" wording as-is, or update?
- [ ] Include the SpellSweep link now, or hold it for a later pass?
- [ ] Does a completion modal already exist in the current code, or is this net-new?

## Acceptance checklist
- [ ] Download button removed
- [ ] Image-share removed; single "Share result" action remains, with clipboard/native-share fallback
- [ ] Completion modal (if present) contains only: result, one Share button, close control
- [ ] Footer redesigned as an evenly-weighted link row with a top divider, legible and tappable on mobile
- [ ] Listdle badge present and linking to the correct game page
- [ ] No new colors or fonts introduced
- [ ] Nothing blocks replay or reappears in a way that reads as nagging
