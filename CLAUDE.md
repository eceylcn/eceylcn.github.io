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
- **The word "episode" never appears anywhere**, and neither does an episode number
  (`Ep. 04 · …`) or the phrase "interactive audio lesson". A generated podcast carries an
  engaging title of its own. Say "podcast" or "the audio" instead.
- **It is a listening activity, not a lesson.** Never "audio lesson". Where a noun for
  the whole thing is needed, use "listening activity" or "the activity".
- **Never claim an outcome the product does not measure.** No "corrected after
  feedback", no "incorrect, then resolved". Reported results are only what is actually
  recorded: answered, correct, incorrect, completed.
- **Never hardcode a number the user chooses.** Teachers pick how many checkpoints go in
  a podcast, so prose says "as many checkpoints as the teacher asked for", not "four
  checkpoints". A specific number is fine inside the simulator, where it is showing one
  concrete run. The teacher form's end-quiz row reads just `End quiz`, with no count.
- Case-study prose is written in full sentences, no bullet lists, no em dashes as
  connectors. This holds inside the illustrated cards too.
- **When a section gets too long to read as prose, it becomes cards, not bullets.**
  `PODCAST_FLOW.owned` is the pattern: a short lead, a `.case-cards` grid where each card
  pairs a `CASE_FIG` animation with a heading and two or three sentences, then a closing
  line. Every figure is authored in its finished state and animated away from it, because
  the stylesheet kills all motion under `prefers-reduced-motion` and the leftover frame
  still has to explain the idea.

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
  `scrollbar-gutter:stable`). Content must never be clipped and unreachable. The viewer
  can scroll the form by hand at any point — `SIM.scroll` is restored after every
  repaint, so a repaint never yanks the form back to the top.
- **Auto-scroll follows the cursor but never hides what it just clicked.**
  `simScrollBy()` scrolls the union of the target, its field, its open dropdown and any
  settings the click revealed, and clamps so the field's own top stays inside the frame.
  `simRevealHot()` runs after each click repaint to bring newly revealed rows into view
  and carry the cursor with them. If a step ends with the acted-on row scrolled off,
  that is a bug — the viewer has to see what changed.
- Dropdowns are absolutely positioned. Fields low in the form pass `up` to `simDrop()`
  so the list flips above the field, the way a real select does, instead of forcing a
  scroll that would push the field out of the frame.
- Captions are keyed `s1, s2, …` in step order. Renumber them when inserting a step;
  don't leave gaps.
- Everything must still work with `prefers-reduced-motion: reduce`, which paints each
  step's final frame with no cursor.
