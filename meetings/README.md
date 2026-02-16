# Meetings

This folder holds the official record for the Time and Vision Lab Group meetings: agenda notes, minutes, action items, and any slides or supporting files shared in the session.

## Folder structure

Meetings are stored by year, then by month name, then by a date-coded meeting folder:

`meetings/<YEAR>/<Month>/<DD-MM-YY>/`

Example:
- `meetings/2026/February/16-02-26/`

Where:
- `<YEAR>` is four digits (for example `2026`)
- `<Month>` is the month name in English with initial capital (for example `February`)
- `<DD-MM-YY>` is day, month, two-digit year (for example `16-02-26` = 16 February 2026)

Each meeting folder should be self-contained and should include the core files listed below.

## What goes inside a meeting folder

Minimum expected contents:

- `README.md`  
  The meeting record. This should be the first thing someone opens. It should summarise the session and link to the slides and any key files.

- `minutes.md`  
  The minutes or discussion notes. Keep it readable and factual. If minutes are not available, leave the file in place and note that it is pending.

- `action-items.md`  
  Clear actions with owners and due dates (or “TBD” if unknown at the time). If actions are tracked as GitHub Issues, link to them here.

- `slides/`  
  All slide decks shown in the meeting. If there are no slides, keep the folder anyway.

Recommended (optional) additions if useful:
- `files/` for supporting materials that are not slides (figures, PDFs, small datasets, links captured as markdown, etc.)
- `decisions.md` if we want a separate running list of decisions for that meeting (otherwise keep decisions in the meeting `README.md`)

## Creating a new meeting folder

1. Copy the template folder: `meetings/_TEMPLATE/`
2. Create the new path for the meeting:
   - `meetings/<YEAR>/<Month>/<DD-MM-YY>/`
3. Paste the template contents into the new meeting folder.
4. Update the meeting folder `README.md` with:
   - date, time, location
   - attendees and apologies
   - links to slides and any shared files
   - decisions and action items

If a meeting is cancelled, still create the folder and add a short note in `README.md` stating it was cancelled and why (if appropriate).

## Naming conventions

### Meeting folder
Use:
- `DD-MM-YY` (for example `16-02-26`)

This keeps folders short while still sorting correctly within a month.

### Slides and shared files
Use:
- `YYYY-MM-DD_topic_presenter.ext`

Examples:
- `2026-02-16_scanpath_similarity_liam.pptx`
- `2026-02-16_methods_roundtable_milena.pdf`

Rules of thumb:
- Use lowercase in filenames.
- Use underscores instead of spaces.
- Keep titles short but descriptive.

## Important notices and pinned items

Important items (for example schedule changes, room changes, urgent updates) should be:
- pinned as a GitHub Issue or Discussion (recommended), and linked from the relevant meeting folder, and or
- added to `announcements/README.md`, with links back to the relevant meeting folder.

## Quick navigation

- Template: `meetings/_TEMPLATE/`
- Announcements index: `announcements/README.md`

If you want a top-level index by date (optional), add links here as the year grows, for example:

- 2026
  - February
    - 16-02-26: `meetings/2026/February/16-02-26/`
