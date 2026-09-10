# Word Web growth-pass baseline

Recorded on September 10, 2026 before implementation of the growth and retention milestones.

## Environment

- Repository: `WordWebDebug`, branch `main`.
- Local static server using the unmodified repository files.
- Browser verification in the Codex in-app browser at its default desktop viewport and at a 390×844 phone viewport.
- Daily puzzle: Day #21.
- Targets: `COATS`, `COKES`, `BALES`.
- Benchmarks: Perfect 5, Par 8.

## Initial and in-progress behavior

- A fresh origin displayed the first-visit tutorial and stored its existing one-time gate; subsequent reloads did not reopen it.
- The daily puzzle loaded with the entry and reveal controls enabled after graph initialization.
- An invalid three-letter submission displayed `Words need to be exactly 5 letters.` and did not change the score.
- `BOATS` was accepted as a valid submission and changed Words from 0 to 1.
- Reloading after that submission restored Day #21 with Words 1, an enabled entry field, and no reopened tutorial.

## Terminal completion flows

### Perfect solve

Submitted, in order: `BOATS`, `BOLTS`, `BOLES`, `BAKES`, `CAKES`.

- `CAKES` bridged two branches.
- The game ended at Words 5 with `Perfect! All three words are connected.`
- The day label changed to `Day #21 · Solved`.
- Entry and reveal controls became disabled.
- The result card reported `5 words · Perfect score` and rendered five gold cells.
- Reloading restored the same completed state and visible share panel.

### Non-perfect solve

Submitted, in order: `COLTS`, `COLAS`, `CODAS`, `CODES`, `CONES`, `BONES`, `BANES`.

- The game ended at Words 7 with `Solved! The perfect solution is shown in rust.`
- The optimal words `BOATS`, `BOLTS`, `BOLES`, `BAKES`, and `CAKES` were added visually using the rust/waste styling without changing Words 7.
- The result card reported `7 words · beat par by 1`, with five gold and two teal cells.
- Reloading restored the completed state, score, optimal overlay, disabled controls, and visible share panel.

### Reveal/give-up

Started after submitting `BOATS`, at Words 1.

- Opening Reveal displayed the irreversible confirmation dialog.
- Cancelling closed the dialog while leaving Words 1, entry enabled, and the share panel hidden.
- Confirming added the missing optimal solution in rust, changed the day label to `Day #21 · Revealed`, disabled entry and reveal, and displayed `Answer revealed.`
- The player's score remained Words 1.
- The result card reported `This one beat me!`, used the rust accent, and contained no result cells.
- Reloading restored the revealed terminal state and visible share panel.

## Existing sharing behavior

- `Copy text` placed the expected revealed result on the clipboard:

  ```text
  Word Web #21
  This one beat me!
  https://blendletan.github.io/WordWeb/
  ```

- `Copy image` placed an `image/png` item on the clipboard and displayed its success status.
- The Download control invoked the existing download path. A direct contract check confirmed that it creates a blob URL, assigns `word-web.png` to the anchor download attribute, clicks the anchor, and revokes the blob URL. The in-app browser did not expose its blob download as a capturable download event.

## Layout and runtime observations

- At the default desktop viewport, the board, entry panel, stats, result card, and three share controls rendered without overlap.
- At 390×844, the completed non-perfect flow had no horizontal document overflow (`scrollWidth` equaled `clientWidth`), the share card scaled within its panel, and all three share controls remained visible.
- Every local application asset and `data/words.json` returned HTTP 200 or 304.
- No application console errors were recorded. GoatCounter emitted its expected localhost-only warning and did not affect gameplay or sharing.

## Open prerequisite discovered

`AGENTS.md` and `MILESTONES.md` refer to an existing PayPal support link in `index.html`, but the current file and reachable Git history contain no PayPal or other support destination. A verified support URL must be supplied before the voluntary-support milestone is implemented.
