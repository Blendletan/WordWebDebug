# Word Web UI Refresh — Milestones

Implement these milestones in order. Test each milestone locally before committing and
pushing it to staging `main`. Stop at the Listdle decision gate until the owner chooses a
variant. The product requirements and exact destinations are in
`wordweb-ui-refresh-spec.md`; stable repository constraints are in `AGENTS.md`.

## Status

| Milestone | Status | Gate |
| --- | --- | --- |
| 1. Text-only inline sharing | Complete | Share behavior verified locally |
| 2. Completion modal and dialog accessibility | Complete | All terminal and dialog paths verified |
| 3. Complete footer and Listdle selection | Not started | Owner chooses Compact or Light |
| 4. Documentation and final regression | Not started | Local and deployed staging checks pass |

Update this table as work progresses.

---

## Milestone 1 — Text-only inline sharing

### Objective

Replace the three sharing choices with one **Share result** action while retaining the
canvas result preview and existing result text.

### Files

- `index.html`
- `css/word-web.css`
- `js/app.js`
- `js/rmlp-share-card.js`

### Work

1. Replace `Copy image`, `Copy text`, and `Download` with one primary
   **Share result** button.
2. Add a polite live-region status and a hidden labelled/selectable manual-copy fallback.
3. Compute the existing share text once from the same result data used by the canvas.
4. Add one reusable copy function:
   - success hides the fallback, announces `Copied! Paste it anywhere.`, and emits
     `share-copy-text`;
   - failure shows the exact text for manual copying without claiming or tracking
     success.
5. Remove obsolete image-copy/download DOM references, handlers, events, helpers, and
   related comments. Keep canvas rendering and text generation.
6. Do not introduce native sharing or rewrite result text.

### Verify before commit and push

- Solved and revealed result variants still render the same canvas and text.
- Only one sharing button is visible.
- Clipboard success and forced/rejected clipboard failure behave as specified.
- Missing or throwing GoatCounter does not prevent copy or fallback behavior.
- Restored completed games still render the inline completion panel.
- Desktop and narrow mobile layouts do not overflow.
- No orphaned share IDs, handlers, helpers, or obsolete event calls remain.

### Completion gate

The inline panel has one reliable text-sharing path, the canvas preview is unchanged,
and core game/persistence behavior is unaffected.

---

## Milestone 2 — Completion modal and dialog accessibility

### Objective

Add a one-time terminal-state sharing nudge without replacing the inline completion UI,
and make dialog lifecycle behavior consistent.

### Files

- `index.html`
- `css/word-web.css`
- `js/app.js`
- `js/tutorial.js` only if its final close action must use shared focus restoration

### Work

1. Add a labelled completion modal containing the existing result title/status, one
   **Share result** button, its status/fallback UI, and a close button.
2. Reuse Milestone 1's result data and copy function.
3. Render the inline panel before opening the modal.
4. Open it after live perfect solve, live non-perfect solve after the optimal overlay,
   and live confirmed reveal.
5. Keep it closed during saved terminal-state restoration without changing saved data.
6. Keep it open after copying.
7. Implement shared close button, backdrop, Escape, initial focus, Tab containment, and
   focus restoration for completion, tutorial, and reveal-confirmation dialogs.
8. Add a restrained fade/scale entrance that is disabled by
   `prefers-reduced-motion`.

### Verify before commit and push

- Perfect, non-perfect, and confirmed-reveal paths render the inline result and open the
  modal once.
- Cancelled reveal opens no completion modal.
- Refreshing solved and revealed states restores the inline panel without the modal.
- Inline and modal buttons produce byte-for-byte identical text.
- Copy success leaves the modal open; clipboard failure shows its local fallback.
- All three dialogs support close button, backdrop, Escape, focus containment, and focus
  restoration without changing their content or underlying behavior.
- Desktop, narrow mobile, and reduced-motion presentations behave correctly.

### Completion gate

The modal is an additive, accessible nudge shown only on live terminal transitions, and
Reveal Answer, tutorial content, persistence, and game logic remain unchanged.

---

## Milestone 3 — Complete footer and choose the Listdle badge

### Objective

Replace the feedback sentence with the complete four-item footer and select the Listdle
badge by reviewing it in context.

### Files

- `index.html`
- `css/word-web.css`
- `js/app.js`

### Work before the owner decision

1. Build the semantic footer in final order: feedback, Listdle, SpellSweep, support.
2. Add the confirmed URLs, labels, new-tab behavior, and `rel` attributes from the spec.
3. Add the top rule, existing typography/colors, desktop row, mobile wrapping, visible
   focus, and 44px minimum targets.
4. Add fail-silent `feedback-click`, `listdle-click`, `more-puzzles-click`, and
   `support-click` tracking without preventing navigation.
5. Render the Compact Listdle badge and capture desktop and narrow-mobile views.
6. Render the Light badge at the same viewports and present both comparisons to the
   owner.

### Owner decision gate

Do not commit or push the comparison state. Wait for the owner to choose Compact or
Light, then remove the unselected variant and retain only the chosen official asset.

### Verify before commit and push

- Final order is feedback, Listdle, SpellSweep, support.
- Every label, URL, `target`, `rel`, alt text, and event name is correct.
- Email uses normal mail-client behavior; the other destinations open in new tabs.
- Missing or throwing GoatCounter prevents no navigation.
- The selected badge loads and the rejected variant is absent from source and runtime.
- Desktop and narrow-mobile layouts are balanced, tappable, and free of horizontal
  overflow.
- The footer remains visually subordinate to the puzzle before completion.

### Completion gate

The owner-selected four-item footer is complete, accessible, responsive, measurable,
and contains no temporary comparison markup.

---

## Milestone 4 — Documentation and final regression

### Objective

Bring current-state documentation in line with the finished implementation and verify
the complete staging change before any separate production decision.

### Documentation

Update `README.md` only where behavior changed. Record concisely:

- the single text-sharing action and manual fallback;
- the canvas as a preview rather than a sharing format;
- the completion modal's live-only trigger;
- final footer destinations and selected badge;
- GoatCounter event names;
- the source locations for public URLs.

Confirm that README, AGENTS, the specification, milestones, and implementation agree.
Remove obsolete share IDs, events, comments, and documentation rather than preserving
change history in current-state files.

### Source checks

- Run `git diff --check` and JavaScript syntax checks.
- Search for stale native/image/download share code and event calls.
- Confirm `renderShareCard()` and `shareCardText()` are still intentionally used.
- Confirm no unrelated or core-logic file changed accidentally.

### Final browser regression

1. **Load and entry:** fresh tutorial gate, returning load, current daily puzzle, valid
   submission, and representative invalid feedback.
2. **Solve:** perfect and non-perfect completions, including the non-perfect optimal rust
   overlay without score inflation.
3. **Reveal:** confirmation, cancellation, confirmed reveal, and unchanged submitted-word
   score.
4. **Persistence:** in-progress restoration and solved/revealed restoration without
   reopening the completion modal or recounting outcomes.
5. **Sharing:** identical inline/modal text, successful copy confirmation, manual fallback
   on copy failure, and no automatic modal close.
6. **Dialogs:** close button, backdrop, Escape, focus containment, focus restoration, and
   reduced-motion behavior for all three dialogs.
7. **Footer:** all destinations, selected badge, new-tab behavior, keyboard focus, and
   44px targets.
8. **Analytics:** agreed events when available; no broken action when GoatCounter is
   missing or throws.
9. **Responsive layout:** representative desktop and 390×844 mobile views with no
   horizontal document overflow.
10. **Runtime:** application assets load and no application console error appears.

### Deployed staging check

After the milestone is committed and pushed, wait for GitHub Pages and hard-refresh the
staging page. Exercise one terminal path, both share locations, all four footer
destinations, and the final desktop/mobile footer.

### Completion gate

All relevant checks pass or have a clearly reported blocker; documentation describes
only current behavior; staging contains no temporary badge markup; and production has
not been modified.
