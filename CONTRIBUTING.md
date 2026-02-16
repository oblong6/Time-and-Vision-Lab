# Contributing

## What goes where

- Meeting materials belong under:
  - `meetings/<YEAR>/<Month>/<DD-MM-YY>/`
  - Example: `meetings/2026/February/16-02-26/`

Within each meeting folder:
- `README.md` is the meeting record and should link out to everything else
- `minutes.md` holds the minutes or discussion notes
- `action-items.md` holds actions with owners and due dates (or “TBD”)
- `slides/` contains any slide decks presented
- Optional: `files/` for supporting material that is not slides (figures, PDFs, small datasets, etc.)

Important notices (for example schedule changes, room changes, urgent updates) should be:
- pinned as a GitHub Issue or Discussion (recommended) and linked from the relevant meeting folder, and or
- added to `announcements/` (and indexed in `announcements/README.md`)

## Naming conventions

### Meeting folder
Use:
- `DD-MM-YY` (for example `16-02-26`) inside the relevant `YEAR/Month/` path.

### Files (slides and supporting materials)
Use:
- `YYYY-MM-DD_topic_presenter.ext`

Examples:
- `2026-02-16_scanpath_similarity_liam.pptx`
- `2026-02-16_methods_roundtable_milena.pdf`

Rules of thumb:
- Use lowercase in filenames.
- Use underscores instead of spaces.
- Keep titles short but descriptive.

## Suggested workflow

- Keep each meeting folder self-contained (README plus links to files in that folder)
- For small edits (typos, adding a slide) committing directly is fine if that is how the repo is managed
- For non-trivial changes (structure, templates, policy wording), use a branch and open a pull request
- If you add a large slide deck or PDF, consider Git LFS to avoid hitting GitHub file limits

## Sensitive or unpublished content

Assume meeting materials are internal unless explicitly agreed otherwise by the presenter or a supervisor. Do not share content outside the group without permission.
