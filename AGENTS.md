# AGENTS.md — Word Web

## Purpose

This repository contains **Word Web**, a static daily word puzzle hosted on GitHub Pages.

The current game is already live and receives real traffic. Changes should therefore be treated as production changes, not prototype work. The immediate goal is to improve **sharing, ratings, return paths, audience capture, and voluntary support** without making the puzzle itself feel commercial or cluttered.

For the current growth/retention pass, read `MILESTONES.md` before changing code.

## Product principle

**Protect the puzzle; improve what happens around it.**

The unsolved-game experience should remain extremely clean. Growth and monetization calls-to-action should be concentrated primarily in the **post-game state**, after the player has solved or revealed the puzzle.

Do not make the player fight through marketing in order to play.

## Repository overview

The site has no application server and no build step. It is a static GitHub Pages project.

Important files:

- `index.html`
  - Page shell.
  - Header, graph board, word-entry form, score display, tutorial modal, reveal confirmation, completion/share panel, footer.
  - GoatCounter script is loaded here.
- `css/rmlp-tokens.css`
  - Brand/design tokens.
  - Prefer these variables over one-off styling.
- `css/word-web.css`
  - Page/UI styling.
  - Graph bubble/thread styling does **not** belong here.
- `js/app.js`
  - Main game state and UI orchestration.
  - Daily puzzle selection, persistence, input validation, reveal flow, completion flow, and current share-button wiring.
- `js/graph-view.js`
  - D3 graph rendering and layout.
- `js/bubble-theme.js`
  - Shared visual language for graph bubbles and threads.
- `js/tutorial.js`
  - Nine-step tutorial.
- `js/rmlp-share-card.js`
  - Share image generation and share-text generation.
- `js/lib/wordgraph.js`
  - Word graph access.
- `js/lib/steiner.js`
  - Exact Steiner-tree logic and optimal-tree reconstruction.
- `js/lib/puzzle-generator.js`
  - Deterministic puzzle generation.
- `data/words.json`
  - Production word graph.
- `tools/build_word_graph.py`
  - Word-graph build utility.

The README contains important implementation history and invariants. Read it before altering game logic.

## Non-negotiable game invariants

Unless a milestone explicitly says otherwise, do **not** change any of the following:

1. There are exactly three target words in the standard daily puzzle.
2. Player-entered words are five letters.
3. A submitted word must be in the production word graph.
4. A submitted word must differ by exactly one letter from at least one word already in the web.
5. A newly submitted word may bridge multiple currently separate connected components.
6. Score is **words submitted by the player**, not graph edges.
7. There is no undo.
8. `perfectWords` is the true optimum derived from the solver.
9. `parWords = perfectWords + PAR_BONUS`.
10. Revealing the answer is a terminal state and does not inflate the player's submitted-word score.
11. After a non-perfect solve, the optimal solution may be added visually in rust without removing or rewriting the player's own path.
12. The daily puzzle is deterministic from the player's local calendar date.
13. Same-day progress must continue to restore correctly from local storage.
14. The game must remain usable as a fully static site on GitHub Pages.

Do not casually touch `js/lib/*`, graph connectivity logic, solver logic, daily seeding, or persistence while working on growth UI.

## Current implementation facts that matter for this pass

- The completion flow is driven by `showSharePanel()` in `js/app.js`.
- The existing completion panel contains:
  - share-card preview;
  - `Copy image`;
  - `Copy text`;
  - `Download`.
- Existing share actions are tracked in GoatCounter as event paths such as:
  - `share-copy-image`
  - `share-copy-text`
  - `share-download`
- The current `Reveal answer` control is a header pill beside `How to play`.
- The current support link is in the small muted footer and is therefore easy to miss.
- The current support URL is the existing PayPal support link in `index.html`.
- Word Web is listed on Listdle at:
  - `https://listdle.com/games/word-web`
- There is currently no newsletter/email-list integration.
- There is currently no cross-promotion area for other puzzle games.

## Design rules for this growth pass

### 1. The game remains the hero

Before the player finishes:

- do not add popups asking for email;
- do not add interstitials;
- do not add display ads;
- do not add account requirements;
- do not make support/donation a primary header action;
- do not make Listdle rating a primary pre-game action.

The board and input should remain the visual focus.

### 2. The completion state becomes the growth engine

After a solve or reveal, the completion area should be able to contain, in priority order:

1. result/share card;
2. primary share action;
3. secondary share actions;
4. Listdle rating/review CTA;
5. future “play another puzzle” / puzzle-network CTA;
6. email notification signup;
7. voluntary support CTA.

This ordering may be adjusted slightly for visual clarity, but **sharing and another puzzle outrank monetization asks**.

### 3. Progressive enhancement for sharing

The preferred primary action is `Share result`.

- Use the browser's native share capability when available and suitable.
- Do not remove working clipboard/download fallbacks.
- If native sharing is unavailable or fails, the player must still have an obvious useful share path.
- Existing share text/image generation should be reused rather than duplicated.

Do not regress the current `Copy image`, `Copy text`, or `Download` behavior while simplifying the interface.

### 4. External services are configuration, not inventions

Never fabricate:

- API keys;
- newsletter form endpoints;
- account IDs;
- secret tokens;
- provider-specific URLs that have not been supplied or verified.

Known public destinations may be hard-coded when appropriate, such as the existing Listdle game page.

For the newsletter integration, if the exact provider/form action is not yet configured:

- implement the code so a single clearly documented constant/attribute can enable it later;
- keep a nonfunctional signup form hidden rather than shipping a broken form;
- document exactly what value the owner needs to provide.

No secrets belong in this static repository.

### 5. Analytics must describe actual actions

Use GoatCounter for lightweight product analytics.

Prefer a small reusable event helper rather than duplicating GoatCounter calls throughout the code.

Recommended event names for this pass:

- `puzzle-solved`
- `puzzle-revealed`
- `share-native`
- `share-copy-image`
- `share-copy-text`
- `share-download`
- `listdle-click`
- `newsletter-submit`
- `support-click`
- `more-puzzles-click`

Preserve the existing share-event names where possible so historical data remains comparable.

Important distinction:

- `newsletter-submit` means the player submitted the form.
- Do not label it `newsletter-subscribed` unless the application actually knows the subscription succeeded.

Analytics must fail silently. Ad blockers or a missing GoatCounter object must never break gameplay or navigation.

### 6. Accessibility and mobile behavior matter

For every new interactive element:

- use semantic buttons, anchors, labels, and forms;
- retain visible keyboard focus;
- provide useful accessible names;
- respect `hidden` states;
- ensure layout works at narrow mobile widths;
- do not depend on hover;
- keep tap targets reasonable.

External links that intentionally open a new tab must use appropriate `rel` attributes.

### 7. Reuse the existing visual language

Use the existing RMLP tokens and component vocabulary.

Prefer:

- existing `ww-btn`, `ww-btn-accent`, panel, hint, and type styles;
- existing spacing/radius/color variables;
- a small number of new classes specific to the completion/growth panel.

Avoid:

- a second visual system;
- SaaS-looking bright newsletter widgets;
- giant gradients;
- attention-grabbing donation banners;
- intrusive animation.

The page should still look like a coherent retro puzzle publication.

## Scope discipline

### In scope for the current pass

- Completion/share UX.
- Native share progressive enhancement.
- Listdle rating/review CTA.
- Newsletter signup UI and safe external configuration hook.
- More-puzzles/cross-promotion area.
- Making voluntary support visible in the post-game state.
- De-emphasizing the reveal control without removing it.
- GoatCounter events for the above.
- Small README updates documenting new behavior/configuration.
- Regression testing.

### Explicitly out of scope unless the owner asks

- Ads or ad-network integration.
- Paid subscriptions.
- User accounts.
- Archives/back-catalog.
- Hints.
- Streaks.
- Leaderboards.
- Hard mode / k=4.
- Par recalibration.
- Word-list replacement.
- Solver changes.
- Puzzle-generator changes.
- Hosting migration.
- A framework rewrite.
- A bundler/build-system migration.
- Large visual redesign.

Do not “helpfully” expand the project into these areas.

## Coding approach

This is a small static application. Keep it that way.

- Prefer small targeted edits.
- Do not introduce a framework just to implement these milestones.
- Do not introduce a package manager or build step unless a concrete requirement makes it necessary.
- Do not duplicate existing share-card or puzzle-state logic.
- Keep DOM lookups together with the existing `els` object when practical.
- Keep completion-state orchestration near the existing completion/share code.
- If analytics are generalized, use one small helper.
- Use named constants for public external URLs/configuration that may change.
- Keep failure modes graceful.

## Testing expectations

Before declaring a milestone complete, verify the relevant behavior on both a desktop-size viewport and a narrow/mobile-size viewport.

At minimum, regression-test:

1. First load still shows the tutorial only according to its existing local-storage gate.
2. Today's puzzle still loads.
3. Valid word submission still works.
4. Invalid-word feedback still works.
5. Perfect solve still reaches the completion state.
6. Non-perfect solve still shows the optimal answer and then reaches the completion state.
7. Reveal confirmation still requires confirmation.
8. Revealed games still reach the completion state.
9. Same-day refresh still restores progress.
10. Share-card rendering still works.
11. Existing copy/download share actions still work.
12. New external CTAs do not interfere with gameplay.
13. GoatCounter being blocked/unavailable does not break any button.
14. The layout remains usable on a narrow phone-sized viewport.

For native sharing, test both branches:

- supported/available;
- unsupported or rejected, with fallback behavior.

For newsletter configuration, test both branches:

- configured;
- unconfigured, where no broken signup UI should be exposed.

## Completion standard

Do not mark a milestone complete merely because the markup exists.

A milestone is complete when:

- the feature works;
- the fallback works;
- the page still looks intentional;
- accessibility basics are present;
- analytics are wired where specified;
- no core-game invariant changed unintentionally;
- relevant README/config notes are updated;
- regressions above have been checked.

## Working style for Codex

Work milestone-by-milestone from `MILESTONES.md`.

For each milestone:

1. Inspect the relevant existing implementation before editing.
2. Make the smallest coherent change.
3. Test it.
4. Summarize exactly what changed and any remaining external configuration needed.
5. Do not proceed by silently redesigning unrelated areas.

If an external value is missing, implement a safe configuration seam and continue with everything else that can be completed without guessing.
