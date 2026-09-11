# AGENTS.md

Guidance for AI coding agents (Codex, etc.) working in this repository.

## Project overview
WordWeb is a browser-based word puzzle game (RMLP brand) — a Steiner-tree puzzle where
players connect three target words via single-letter substitutions, scored against a
mathematically optimal par computed via the Dreyfus-Wagner algorithm. It's a static site
hosted on GitHub Pages, and separately mirrored (manually, not auto-synced) to itch.io —
that mirror lives outside this repo and won't pick up changes here on its own.

Stack: vanilla HTML/CSS/JS, D3.js for the force-directed puzzle graph, a shared
`bubble-theme.js` module for brand tokens. No framework. Check for a `package.json` or
build config before assuming one exists — this may be a plain static site with no build
step at all.

Brand system — reuse these, don't invent new ones:
- Fonts: Fraunces (display), Libre Franklin (UI), Courier Prime (mono)
- Palette: cream, ink, red, teal, gold
- Source of truth for exact values: the shared theme module (`bubble-theme.js` or
  equivalent) — locate it and read the actual values rather than guessing hex codes.

## Setup / running locally
1. Look for a README, `package.json`, or Makefile first and follow whatever's documented
   there.
2. If there's no build step, serve the directory with any static file server (e.g.
   `python3 -m http.server`) and open it in a browser.
3. Confirm which branch GitHub Pages actually deploys from (`main` vs. `gh-pages` vs. a
   `/docs` folder) before assuming — don't guess at the publish path.
4. Do not introduce a bundler, framework, or new dependency to accomplish UI changes
   unless explicitly asked. This is a deliberately lightweight static site.

## Current task
Read `wordweb-ui-refresh-spec.md` before making changes — that's the actual task brief
(simplify the share flow, trim the completion modal, redesign the footer, add a Listdle
badge, optionally link to SpellSweep). This file covers general repo conventions; that
one covers what to build. If it isn't in this repo, ask for it before starting.

## Scope boundaries — do not touch
- Puzzle-solving / par logic (Dreyfus-Wagner / graph computation)
- D3 graph rendering and interaction code
- Daily puzzle seeding / date logic
- localStorage persistence / save-data format
- Tutorial slideshow content or logic

This is a UI-only pass. If a requested change seems to require touching any of the
above, stop and flag it instead of proceeding.

## Design conventions for UI work
- One ask per moment — never stack multiple asks (share / rate / support / cross-promo)
  into a single button, modal, or line.
- Secondary links (feedback, support, rate, cross-promo) are plain text or small
  icon+label pairs, all the same visual weight as each other. No color-shouting buttons,
  no banners competing with the puzzle.
- Any modal is easy to dismiss (X, tap-outside, Esc), never blocks replay, never
  re-appears in a way that reads as nagging.
- When in doubt, prefer removing an option over adding a setting/toggle for it — fewer
  choices, not more configuration.

## Commit conventions
- Scope commits to one piece of the spec at a time (e.g. "remove download/image share",
  "redesign footer", "add Listdle badge") rather than one large commit.
- Minimal diffs — don't reformat or refactor unrelated code while you're in a file.
- If something in the spec's "Open questions" list is still unresolved (Listdle slug,
  badge variant, etc.), leave a clear `TODO:` comment at the relevant line rather than
  inventing a value, and call it out in the PR description.

## Verification before calling this done
No automated test suite is expected for this kind of change — verify manually:
- [ ] Solve a puzzle end-to-end; the completion state still shows correctly
- [ ] Share action works (native share sheet where available, clipboard fallback
      otherwise) and produces the same text that was generated before this change
- [ ] Download button and image-share are fully gone — no dead code, no orphaned assets
- [ ] Footer renders correctly at both desktop and mobile widths (wrap/stack, tap
      targets ≥44px)
- [ ] Listdle badge links to the correct URL and renders at the intended size
- [ ] Tutorial, graph, and daily-puzzle behavior are unchanged
- [ ] Everything in the acceptance checklist in `wordweb-ui-refresh-spec.md` is checked off
