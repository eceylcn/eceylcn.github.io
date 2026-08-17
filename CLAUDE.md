# Portfolio site — working rules

Ece Yalçın's portfolio. Read this before adding a page, a case study, or a simulator.

## Structure

- The whole site is one file: `index.html` (markup, CSS and JS inline). There is no build
  step and no framework. Keep it that way.
- Routing is hash-based and handled in the script at the bottom of the file:
  `#/project/<id>`, `#/lesson/<id>`, `#/case/<id>`, `#/about`. Every page has a
  `_show*()` DOM-only function plus an `open*()` wrapper that pushes history.
- Subfolders (`entropy/`, `oyunlar/`, `sinif1-yeryon/`, …) are standalone embedded
  builds. Don't refactor them from the shell page.
- Site copy is English. Only Turkish content that is part of a project itself stays Turkish.

## Detail-page conventions

These apply to every detail page — project, lesson, case study — so the pages feel like
one site. When a new page type is added, it follows the same rules.

- **Back button text is always `← Back`.** Never "Back to Products & Tools", never
  "Back to Work", never a destination name.
- **No eyebrow chip above a detail-page title.** The pink/red `.eyebrow` pill is for
  home-page section labels only. A case study opens on its `<h1>`, not on a
  "Product · Madlen" style tag.
- **Body copy runs the full width of its container.** Don't cap prose at a narrow measure
  while the boxes and frames around it span the full `1080px` wrap — the ragged right
  edge reads as broken. If a block needs to be narrower, everything in that section does.

## Copy rules

- The feature is **comprehension questions**, not "comprehension checks". Use the same
  term in prose, field labels and captions.
- **Never hardcode a number the user chooses.** Teachers pick how many checkpoints go in
  an episode, so prose says "as many checkpoints as the teacher asked for", not "four
  checkpoints". A specific number is fine inside the simulator, where it is showing one
  concrete run.
- Case-study prose is written in full sentences, no bullet lists, no em dashes as
  connectors.

## Flow simulator (`PODCAST_FLOW`)

The case-study simulator is a step machine. Its invariants matter more than its markup:

- **All visitor-facing strings live in `PODCAST_FLOW`** (`meta`, `overview`, `labels`,
  `options`, `captions`). Render functions read from it and never hardcode copy.
- **Each step carries fully resolved frames**, not diffs. Going back is a plain index
  change with nothing to unwind. When inserting a step, extend the cumulative state
  chain (`A → B → Cc → …`) rather than mutating an earlier state.
- **Nothing may resize between steps.** The frame keeps a constant height; grid columns
  are pinned with `minmax(0, …)` so long option text cannot widen the form column and
  squeeze the preview panel. Check this after adding any field or long string.
- **The form owns its own scroll** (`.sim-gen-form` is `overflow-y:auto` with
  `scrollbar-gutter:stable`). Content must never be clipped and unreachable. The
  simulated cursor scrolls its target into view via `simScrollBy()`, and the viewer can
  scroll the form by hand — `SIM.scroll` is restored after every repaint, so a repaint
  never yanks the form back to the top.
- Dropdowns are absolutely positioned, so an open one renders a `.sim-dropspace` filler
  to give the scroll container room to reach it.
- Captions are keyed `s1, s2, …` in step order. Renumber them when inserting a step;
  don't leave gaps.
- Everything must still work with `prefers-reduced-motion: reduce`, which paints each
  step's final frame with no cursor.
