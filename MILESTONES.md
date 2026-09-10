# MILESTONES.md — Word Web Growth & Retention Pass

## Goal

Turn Word Web's **post-game state** into a modest growth/retention funnel while preserving the current clean game experience.

This pass is not about maximizing revenue today. It is about beginning to measure and strengthen the paths that could matter later:

- sharing;
- Listdle ratings/reviews;
- cross-promotion to other puzzles;
- permission to contact interested players;
- voluntary support.

Do not add display advertising in this pass.

Read `AGENTS.md` before implementation.

---

## Milestone 0 — Baseline and regression check

### Objective

Establish that the current live behavior is understood before changing it.

### Tasks

- Read the repository README, especially:
  - entry mechanics;
  - daily puzzle;
  - persistence;
  - reveal behavior;
  - two-score benchmark model;
  - share-card behavior;
  - documented verified behavior.
- Inspect:
  - `index.html`;
  - `css/word-web.css`;
  - `js/app.js`;
  - `js/rmlp-share-card.js`.
- Run the site through a local HTTP server.
- Record the current completion flow for:
  - perfect solve;
  - non-perfect solve;
  - reveal/give-up.
- Confirm the current share buttons work.
- Confirm same-day persistence still works.

### Do not change

Do not alter solver, puzzle generation, graph rendering, dictionary, or scoring during this milestone.

### Acceptance criteria

- Current behavior is reproducible locally.
- There is a clear baseline for comparison after later milestones.

---

## Milestone 1 — Generalize lightweight analytics

### Objective

Make growth actions measurable without scattering GoatCounter-specific code throughout the UI.

### Current state

`showSharePanel()` currently contains a local `trackShare(action)` helper and sends events such as:

- `share-copy-image`;
- `share-copy-text`;
- `share-download`.

### Tasks

- Replace/refactor the narrow share-only tracker with a small general event helper.
- Preserve the current share event names for continuity.
- Add event support for later milestones.
- Track terminal puzzle outcomes:
  - `puzzle-solved` when the user legitimately solves;
  - `puzzle-revealed` when the user confirms reveal/give-up.
- Ensure a restored completed state does not accidentally generate duplicate outcome events merely from page reload, unless the existing state model makes a fresh legitimate event distinguishable.
- Analytics failures must be silent and non-blocking.

### Planned event vocabulary

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

### Acceptance criteria

- Existing share events still appear under their old names.
- Solved/revealed events fire at the correct user action.
- Blocking GoatCounter does not break the application.
- No duplicate outcome event is introduced by ordinary same-day refresh.

---

## Milestone 2 — Make “Share result” the primary completion action

### Objective

Reduce friction between finishing the puzzle and sharing it.

### Product behavior

After completion, the player should see one obvious primary action:

**Share result**

Secondary options should remain available but visually subordinate:

- Copy text
- Copy image
- Save/download image

### Tasks

- Preserve the existing result-card preview.
- Add a primary `Share result` button.
- Use progressive enhancement:
  - if browser-native sharing is available and can share the useful result payload, use it;
  - otherwise fall back cleanly to an existing share mechanism.
- Reuse the current result text and/or generated share card rather than reimplementing the score logic.
- Do not expose technical browser limitations to the user unless an operation actually fails.
- Retain `Copy text`, `Copy image`, and `Download` as secondary/fallback controls.
- Track a successful invocation attempt of the native share path as `share-native`.
  - Do not count user cancellation as a successful share if the API exposes that distinction.
- Keep existing share analytics for the other controls.

### UX note

The player should be choosing **“share my result”**, not first having to understand clipboard image support.

### Acceptance criteria

- A single obvious primary share action exists.
- Existing secondary actions still work.
- Unsupported native sharing has a useful fallback.
- Share-card text/image contents are unchanged unless a specific improvement is necessary.
- Mobile layout remains clean.

---

## Milestone 3 — Add a Listdle rating/review CTA

### Objective

Turn satisfied finishers into Listdle ratings, reviews, and referral signals.

### Known destination

`https://listdle.com/games/word-web`

Listdle states that games can link players to their Listdle page to leave a rating/review and that ratings/clicks contribute to ranking.

### Tasks

- Add a post-game CTA such as:
  - `Rate Word Web on Listdle`
  - or the current official Listdle badge, if its official embed asset/snippet can be verified at implementation time.
  -https://listdle.com/badge contains the instructions on how to do this
- Link to the Word Web Listdle page above.
- Prefer opening the rating destination in a way that does not needlessly destroy the player's completed result state.
- Track the click as `listdle-click`.
- Keep the CTA below the primary share controls.
- Do not place this prominently before the puzzle is completed.

### Visual requirement

The CTA should be noticeable but not louder than `Share result`.

If using Listdle artwork, use their official current asset. Do not imitate/redraw their logo from memory.

### Acceptance criteria

- CTA is visible in the post-game area.
- Destination is correct.
- Click is tracked.
- It does not appear as a pre-game interruption.
- It fits the existing RMLP visual language.

---

## Milestone 4 — Add “More puzzles” / cross-promotion infrastructure

### Objective

Prepare Word Web to send a player directly into the next puzzle once the puzzle portfolio grows.

### Current state

SpellSweep is the next known puzzle destination:

`https://blendletan.github.io/SpellSweep/`

It is expected to be production-ready before these changes move from WordWebDebug into WordWeb.

### Tasks

- Add a small post-game “More puzzles” section.
- Make its behavior configuration-driven enough that a future game can be added by changing one obvious value/entry.
- Configure SpellSweep using the supplied production URL above.
- If no second production URL is configured:
  - show a restrained `More puzzles coming soon` message;
  - do not render a dead link.
- Once a real destination is configured:
  - present it above newsletter/support CTAs;
  - track clicks as `more-puzzles-click`.
- Avoid building a complicated game catalogue system for one or two links.

### Future-facing design

This should be easy to extend to a small family of games without turning Word Web into a portal rewrite.

### Acceptance criteria

- No dead destination ships.
- One obvious configuration point controls whether a live cross-promotion link appears.
- Cross-promotion is visually more important than support/donation once a second game exists.
- Click is measurable when enabled.

---

## Milestone 5 — Promote voluntary support from invisible footer text to a real post-game CTA

### Objective

Make support discoverable without making the game feel paywalled or needy.

### Current state

The support link currently lives in the small muted footer, beneath contact information.

### Existing destination

Preserve the current support destination from `index.html` unless the owner explicitly supplies a replacement.

### Preferred product framing

Something along the lines of:

**Enjoying Word Web? Help me make more puzzles.**

with a secondary button/link such as:

**Support more puzzles ☕**

Exact wording can be polished to fit the visual design.

### Tasks

- Add a visible but subordinate support card/row in the post-game area.
- Track click as `support-click`.
- Keep the footer contact information.
- Remove redundant footer support wording if the new post-game CTA makes it unnecessary, or reduce the footer version to a quiet fallback link.
- Do not show a modal asking for money.
- Do not imply that payment unlocks today's puzzle.

### Acceptance criteria

- Support is actually discoverable after completion.
- It is quieter than sharing and cross-promotion.
- The existing support destination still works.
- Click is measurable.
- The unfinished-game screen still does not feel monetized.

---

## Milestone 6 — De-emphasize “Reveal answer”

### Objective

Keep the necessary give-up/reveal function while reducing its prominence beside the main onboarding action.

### Current state

`Reveal answer` is a header pill beside `How to play`.

### Tasks

- Preserve the existing confirmation modal and terminal reveal behavior.
- Move or restyle the trigger so it reads as a secondary “stuck?” action rather than a peer of `How to play`.
- Good conceptual treatments include:
  - a quiet text-style control near the entry/status area;
  - `Stuck? Reveal solution`;
  - another subdued location that remains discoverable.
- Keep it disabled until the puzzle is ready, as today.
- Do not change what reveal actually does.

### Acceptance criteria

- Reveal remains easy enough to find intentionally.
- It is no longer one of the strongest controls in the header.
- Existing confirmation/reveal/persistence behavior is unchanged.
- Keyboard accessibility remains intact.

---

## Milestone 7 — Completion-panel information hierarchy and polish

### Objective

Make all the new pieces feel like one intentional end-state rather than several unrelated widgets stacked beneath the game.

### Target hierarchy

A good default order after the result card is:

1. **Share result** — primary.
2. Copy/save fallbacks — secondary.
3. **Rate on Listdle**.
4. **Play another puzzle** when available; otherwise `More puzzles coming soon`.
5. **Support more puzzles**.

The deferred email-notification milestone can add its CTA between cross-promotion and support once a provider and form endpoint have been chosen.

The exact grouping may be improved if visual testing suggests a cleaner solution.

### Tasks

- Consolidate new completion content into a coherent layout.
- Use existing typography, tokens, borders, and spacing.
- Avoid excessive nested cards.
- Ensure the result itself still dominates the panel.
- Check long text, narrow screens, focus order, and external-link behavior.
- Avoid adding unnecessary animation.

### Acceptance criteria

- The post-game state looks designed, not accreted.
- The primary action is obvious.
- The order of importance is visually legible.
- No horizontal overflow at phone widths.
- Unconfigured integrations disappear gracefully.

---

## Milestone 8 — README/configuration documentation

### Objective

Leave the repository understandable for the next development pass.

### Tasks

Update the README with concise notes for:

- primary share behavior and fallbacks;
- Listdle destination;
- analytics event vocabulary;
- cross-promotion configuration point;
- post-game support CTA;
- any new IDs/classes that future work needs to know about.

Do not bloat the README with marketing strategy. Document implementation facts and invariants.

### Acceptance criteria

A future agent can answer these questions from the repo:

- Where do I change the Listdle URL?
- Where do I configure another puzzle link?
- Which GoatCounter events are emitted?
- What happens when native sharing is unavailable?
- Which parts of the completion UI are hidden until the puzzle ends?

---

## Milestone 9 — Final regression and live-site check

### Objective

Confirm that a growth pass did not damage the game.

### Required scenarios

Test at least:

1. Fresh first visit.
2. Returning visit with tutorial already seen.
3. Valid submissions.
4. Invalid submissions.
5. Perfect solve.
6. Non-perfect solve.
7. Confirmed reveal.
8. Cancelled reveal.
9. Same-day refresh during an unfinished game.
10. Same-day refresh after completion.
11. Native share supported.
12. Native share unavailable/cancelled.
13. Copy text.
14. Copy image where browser permits it.
15. Download/save image.
16. Listdle click.
17. Cross-promotion enabled and disabled.
18. Support click.
19. GoatCounter blocked/unavailable.
20. Narrow mobile viewport.
21. Desktop viewport.

### Core invariants to re-check

- Same date still yields the same puzzle.
- Player score is still submitted-word count.
- Perfect/par values are unchanged by this pass.
- Reveal words do not inflate player score.
- Post-solve optimal solution display still behaves as before.
- Persistence still reconstructs the state correctly.
- No growth feature is required to play the puzzle.

### Live deployment check

After the owner deploys to GitHub Pages:

- hard-refresh the public page;
- confirm current assets loaded rather than cached old versions;
- complete at least one real test path;
- confirm external destinations;
- confirm GoatCounter events in a browser where GoatCounter is not blocked.

### Acceptance criteria

The growth/retention features are live, measurable, and unobtrusive, with no known regression to the core puzzle.

---

## Milestone 10 — Deferred newsletter/email notification signup

### Status

Deferred until the owner chooses the mailing-list approach and supplies a real provider form endpoint. This milestone is intentionally last and is not a blocker for shipping Milestones 0–9.

### Objective

Begin collecting permission from players who explicitly want to hear about future puzzle releases.

### Product copy

Do **not** frame this primarily as a generic newsletter.

Preferred idea:

**Want more puzzles? Get an email when I release a new one.**

Keep the copy short and concrete.

### External configuration rule

The repository currently has no known newsletter provider form endpoint.

Codex must **not invent one**.

Implement this so that one clearly documented configuration value can enable the live form later.

Examples of acceptable approaches:

- one named form-action constant;
- one HTML data attribute;
- one small configuration object.

Because this is a static GitHub Pages site, do not put secrets in the repository.

### Behavior when unconfigured

If the actual form endpoint/provider has not been supplied:

- the signup form must remain hidden or clearly disabled for production;
- there must be no fake-success message;
- document the exact external value still needed.

### Behavior when configured

- Show:
  - accessible email label;
  - email input;
  - concise submit button.
- Use appropriate browser email validation.
- Do not add account creation.
- Do not add a popup.
- Track form submission as `newsletter-submit`.
- Do not call the event `newsletter-subscribed` unless success can truly be confirmed.
- Provide a small privacy expectation near the form if appropriate, e.g. that messages will concern new puzzles and the address will not be publicly displayed.
- Prefer a provider that can accept a plain static HTML form without requiring secrets in client code.

### Placement

Post-game, below sharing/Listdle/cross-promotion and above or near voluntary support.

A second extremely quiet footer invitation may be considered only if it does not clutter the page.

### Documentation and testing

When this deferred milestone is implemented:

- document the newsletter configuration point in the README;
- verify both configured and unconfigured branches;
- repeat the relevant completion-panel checks at desktop and narrow mobile widths;
- confirm the form submission event is non-blocking when GoatCounter is unavailable.

### Acceptance criteria

- Configured form works with the supplied real endpoint.
- Unconfigured form does not pretend to work.
- No secret is committed.
- Submission is measured.
- Mobile layout and keyboard behavior are good.
- No popup/interstitial is introduced.

---

# Deferred follow-up questions

These are intentionally **not** blockers for this pass unless the owner decides otherwise:

- Which mailing-list provider should be the long-term home?
- Should the puzzle family eventually have a shared landing page?
- Should all games share one mailing list and brand?
- Should a future supporter tier include archives/stats?
- When is traffic large enough to test direct sponsorship or ads?
- Should Listdle prompts differ between solved and revealed states?
- Should post-game CTAs be A/B tested once traffic is large enough?

First build the clean measurement and growth paths. Optimize them later with actual player behavior.
