# AGENTS.md — Word Web staging

Instructions for coding agents working in this repository.

## Repository and deployment

WordWebDebug is the staging repository for Word Web. It is a static GitHub Pages site
built with vanilla HTML, CSS, JavaScript, and D3.js. It has no application server,
framework, package manager, or build step.

GitHub Pages automatically deploys this repository's `main` branch. A push to `main`
changes the public staging site. The production Word Web repository is separate and is
not automatically synchronized; do not modify or deploy production unless the owner
asks.

## Required reading

Before changing code, read:

1. `README.md` for current implementation facts and invariants.
2. `wordweb-ui-refresh-spec.md` for the current product requirements.
3. `MILESTONES.md` for implementation order and verification gates.

The refresh spec defines what to build. The milestone file defines how the work is
sequenced. If either conflicts with the current code or with direct owner instructions,
stop and resolve the conflict rather than guessing.

## Important files

- `index.html` — page shell, controls, dialogs, completion panel, footer, script loading.
- `css/rmlp-tokens.css` — page-level brand tokens.
- `css/word-web.css` — page and component styling.
- `js/app.js` — game state, persistence, completion flow, sharing, and UI wiring.
- `js/rmlp-share-card.js` — canvas result preview and plain-text result generation.
- `js/graph-view.js` — D3 board rendering and layout.
- `js/bubble-theme.js` — graph-specific bubble and thread drawing.
- `js/tutorial.js` — tutorial steps and rendering.
- `js/lib/*` — word graph, exact solver, and deterministic puzzle generator.
- `data/words.json` — production word graph.

## Game invariants

Do not change these during UI work:

- A standard puzzle has exactly three target words.
- Player-entered words are five letters and must exist in the production word graph.
- A submitted word must differ by one letter from something already in the web.
- One submitted word may connect multiple distinct components.
- Score is submitted-word count, not edge count.
- There is no undo.
- `perfectWords` is the solver-derived optimum.
- `parWords = perfectWords + PAR_BONUS`.
- Reveal is terminal, adds the optimal solution visually, and does not inflate the
  player's submitted-word score.
- The daily puzzle is determined by the player's local calendar date.
- Same-day progress and terminal state restore from `localStorage`.
- The game must remain a static GitHub Pages site.

## Current-pass boundaries

Do not change solver, word-graph, puzzle-generation, graph-layout, scoring, daily-seed,
word-list, or persistence-format code. Do not change tutorial content or Reveal Answer
placement/behavior. Narrow modal lifecycle changes needed for accessibility may touch
the tutorial or reveal dialog wrappers, but not their content or game behavior.

## Working rules

- Make small, targeted edits; do not reformat unrelated code.
- Reuse `css/rmlp-tokens.css`; do not invent colors or fonts.
- Do not add a framework, dependency, bundler, package manager, or build step.
- Reuse existing result generation and state logic rather than duplicating it.
- Keep DOM lookups with the existing `els` object where practical.
- Analytics must fail silently and must never block copying, closing, gameplay, or
  navigation.
- Preserve unrelated owner changes in the working tree.

## Milestone workflow

Work in the order in `MILESTONES.md`. For each milestone:

1. Inspect the affected implementation.
2. Make the smallest coherent change.
3. Run the milestone's local checks, including browser checks where specified.
4. Commit only that completed milestone.
5. Push it to staging `main` and report what changed and what passed.
6. Resolve any owner decision gate before beginning or pushing dependent work.

Do not push partial or untested work. The final regression matrix in `MILESTONES.md` is
required even if milestone-specific checks have already passed.
