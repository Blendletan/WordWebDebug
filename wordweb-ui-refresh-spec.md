# Word Web UI Refresh — Specification

## Goal

Make sharing one obvious text action, add a small completion-time nudge to share, and
make feedback, Listdle, SpellSweep, and voluntary support visible without making the
puzzle feel promotional.

This is a UI-only change. Puzzle logic, scoring, graph behavior, daily selection,
persistence data, tutorial content, and Reveal Answer behavior remain unchanged.

## Product requirements

### Text-only sharing

- Keep the existing inline completion panel and canvas result preview.
- Replace `Copy image`, `Copy text`, and `Download` with one primary button labelled
  **Share result**.
- The button copies the existing `RMLP.shareCardText()` result. Do not rewrite the title,
  result wording, emoji cells, or production URL.
- Do not use `navigator.share`, a native share sheet, image sharing, or image download.
- On successful copy, announce `Copied! Paste it anywhere.` in an inline polite live
  region.
- If clipboard copying fails, show the generated text in a labelled, selectable form for
  manual copying. Do not claim success.
- The inline panel and completion modal must use the same result data and copy function.
- Retain canvas rendering for the preview. Remove image-copy/download controls, event
  wiring, and helpers that become unused.

### Completion share nudge

Add a modal after:

- a live perfect solve;
- a live non-perfect solve, after the optimal solution is shown;
- a live confirmed reveal.

The modal is additive: the inline completion panel renders underneath it and remains
available after dismissal. Do not open the modal when a completed game is restored from
storage, and do not change the persistence format to accomplish this.

The modal contains only:

- the existing result title and status used by the result card;
- one **Share result** button;
- copy confirmation or manual-copy fallback;
- a close button.

Copying does not close the modal. It closes through its button, backdrop click, or
Escape. Move focus into it, keep keyboard focus inside while open, and restore focus
sensibly on close. Use only a restrained fade/scale entrance and respect
`prefers-reduced-motion`.

Do not put feedback, Listdle, SpellSweep, support, or any other ask in the modal.

### Footer

Replace the sentence-style footer with separate links in this order:

1. **Send feedback** — `mailto:robertparkinson@shaw.ca`
2. **Rate on Listdle** — `https://listdle.com/games/word-web`
3. **More puzzles: SpellSweep** — `https://blendletan.github.io/SpellSweep/`
4. **Support more puzzles ☕** — `https://www.paypal.com/paypalme/AceBlender`

Use a thin top rule and the existing Libre Franklin UI type with teal or ink link colors.
The footer should form one calm row on desktop and wrap or stack cleanly on mobile.
Interactive targets must be at least 44px tall, and the page must not overflow
horizontally.

Open Listdle, SpellSweep, and PayPal in new tabs with appropriate `rel` attributes. The
email link should retain normal mail-client behavior.

Use the official Listdle badge. Before the footer milestone is committed, show the owner
desktop and mobile comparisons of:

- Compact, 32px:
  `https://listdle.com/badges/rate-on-listdle-compact.svg`
- Light, 40px:
  `https://listdle.com/badges/rate-on-listdle-light.svg`

Wait for the owner to choose. Ship only the selected variant, with
`alt="Rate on Listdle"`.

### Analytics

Use the existing fail-silent `trackEvent()` helper:

- `puzzle-solved` — existing live solve event
- `puzzle-revealed` — existing live reveal event
- `share-copy-text` — successful programmatic text copy
- `feedback-click` — feedback activation
- `listdle-click` — Listdle activation
- `more-puzzles-click` — SpellSweep activation
- `support-click` — PayPal activation

A missing, blocked, or throwing GoatCounter must not interfere with copying, modal
controls, gameplay, or navigation. Do not emit `share-copy-text` when programmatic copy
failed and the manual fallback was shown.

### Accessibility and visual constraints

- Use semantic buttons, anchors, dialog labels, and live regions.
- Retain visible keyboard focus and do not depend on hover.
- Prevent background interaction while a modal is open.
- Apply close-button, backdrop, Escape, focus-containment, and focus-restoration behavior
  consistently to the completion, tutorial, and reveal-confirmation dialogs.
- Do not change tutorial content or Reveal Answer behavior while improving dialog
  lifecycle handling.
- Use variables from `css/rmlp-tokens.css`; introduce no new fonts or colors.
- Keep the board and entry interaction visually dominant.
- Do not add a framework, dependency, build system, rating popup, or donation popup.

## Acceptance criteria

- [ ] The canvas result preview remains in the inline completion panel.
- [ ] Inline and modal sharing use one identical text-copy implementation.
- [ ] Image-copy, download, and native-share UI/code are absent from the active flow.
- [ ] Copy success is announced; copy failure exposes selectable text without false
      success.
- [ ] The modal appears after live perfect solve, non-perfect solve, and reveal only.
- [ ] Restored completed games show the inline panel without reopening the modal.
- [ ] Closing the modal leaves the inline completion panel available.
- [ ] All three dialogs meet the agreed dismissal and keyboard behavior.
- [ ] Footer order, destinations, analytics, and new-tab behavior are correct.
- [ ] The owner chooses the Listdle variant after seeing both in context.
- [ ] Desktop and narrow mobile layouts are usable without horizontal overflow.
- [ ] Blocked analytics and clipboard failure do not break the page.
- [ ] Reveal Answer and all core-game invariants remain unchanged.
