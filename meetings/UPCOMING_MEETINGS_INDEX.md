
# Upcoming meetings (next 12 months)

This index is generated from the standard alternating slot schedule:
- Monday 12:00 to 13:00
- Thursday 14:00 to 15:00

## 2026

### February
- `2026/February/16-02-26/` (16/02/2026, Mon 12:00-13:00)
### March
- `2026/March/05-03-26/` (05/03/2026, Thu 14:00-15:00)
- `2026/March/16-03-26/` (16/03/2026, Mon 12:00-13:00)
### April
- `2026/April/02-04-26/` (02/04/2026, Thu 14:00-15:00)
- `2026/April/13-04-26/` (13/04/2026, Mon 12:00-13:00)
- `2026/April/30-04-26/` (30/04/2026, Thu 14:00-15:00)
### May
- `2026/May/11-05-26/` (11/05/2026, Mon 12:00-13:00)
- `2026/May/28-05-26/` (28/05/2026, Thu 14:00-15:00)
### June
- `2026/June/08-06-26/` (08/06/2026, Mon 12:00-13:00)
- `2026/June/25-06-26/` (25/06/2026, Thu 14:00-15:00)
### July
- `2026/July/06-07-26/` (06/07/2026, Mon 12:00-13:00)
- `2026/July/23-07-26/` (23/07/2026, Thu 14:00-15:00)
### August
- `2026/August/03-08-26/` (03/08/2026, Mon 12:00-13:00)
- `2026/August/20-08-26/` (20/08/2026, Thu 14:00-15:00)
- `2026/August/31-08-26/` (31/08/2026, Mon 12:00-13:00)
### September
- `2026/September/17-09-26/` (17/09/2026, Thu 14:00-15:00)
- `2026/September/28-09-26/` (28/09/2026, Mon 12:00-13:00)
### October
- `2026/October/15-10-26/` (15/10/2026, Thu 14:00-15:00)
- `2026/October/26-10-26/` (26/10/2026, Mon 12:00-13:00)
### November
- `2026/November/12-11-26/` (12/11/2026, Thu 14:00-15:00)
- `2026/November/23-11-26/` (23/11/2026, Mon 12:00-13:00)
### December
- `2026/December/10-12-26/` (10/12/2026, Thu 14:00-15:00)
- `2026/December/21-12-26/` (21/12/2026, Mon 12:00-13:00)
## 2027

### January
- `2027/January/07-01-27/` (07/01/2027, Thu 14:00-15:00)
- `2027/January/18-01-27/` (18/01/2027, Mon 12:00-13:00)
### February
- `2027/February/04-02-27/` (04/02/2027, Thu 14:00-15:00)
- `2027/February/15-02-27/` (15/02/2027, Mon 12:00-13:00)

Each entry corresponds to a folder that already exists in this repository.

## Regenerating this index (Python)

The script below computes the next 12 months of meeting dates from the machine’s current date and time (it uses your system clock at runtime). It outputs a markdown index in the same style as this file.

Important: the alternating Monday/Thursday pattern needs a phase anchor (otherwise there are two valid “offsets”). The script includes an `ANCHOR_DATE` that represents a known Monday-slot meeting. If the schedule is ever reset, update the anchor to a known Monday-slot meeting date.

```python
#!/usr/bin/env python3
from __future__ import annotations

import sys
from dataclasses import dataclass
from datetime import date, timedelta


@dataclass(frozen=True)
class Slot:
    key: str
    label: str
    start_time: str
    end_time: str


MON_SLOT = Slot(
    key="MON",
    label="Mon 12:00-13:00",
    start_time="12:00",
    end_time="13:00",
)

THU_SLOT = Slot(
    key="THU",
    label="Thu 14:00-15:00",
    start_time="14:00",
    end_time="15:00",
)

MON_ANCHOR_DATE = date(2026, 2, 16)
THU_ANCHOR_DATE = date(2026, 3, 5)

PERIOD_DAYS = 28


def add_one_year_safe(d: date) -> date:
    try:
        return date(d.year + 1, d.month, d.day)
    except ValueError:
        if d.month == 2 and d.day == 29:
            return date(d.year + 1, 2, 28)
        raise


def next_occurrence_on_or_after(start: date, anchor: date) -> date:
    if start <= anchor:
        return anchor
    delta_days = (start - anchor).days
    steps = (delta_days + (PERIOD_DAYS - 1)) // PERIOD_DAYS
    return anchor + timedelta(days=steps * PERIOD_DAYS)


def iter_slot(start: date, end_exclusive: date, anchor: date, slot: Slot):
    dt = next_occurrence_on_or_after(start, anchor)
    while dt < end_exclusive:
        yield dt, slot
        dt = dt + timedelta(days=PERIOD_DAYS)


def folder_path_for_meeting(d: date) -> str:
    year = d.strftime("%Y")
    month = d.strftime("%B")
    ddmmyy = d.strftime("%d-%m-%y")
    return f"{year}/{month}/{ddmmyy}/"


def uk_date(d: date) -> str:
    return d.strftime("%d/%m/%Y")


def to_markdown_index(start: date) -> str:
    end_exclusive = add_one_year_safe(start) + timedelta(days=1)

    meetings = []
    meetings.extend(list(iter_slot(start, end_exclusive, MON_ANCHOR_DATE, MON_SLOT)))
    meetings.extend(list(iter_slot(start, end_exclusive, THU_ANCHOR_DATE, THU_SLOT)))
    meetings.sort(key=lambda x: (x[0], x[1].key))

    lines: list[str] = []
    lines.append("# Upcoming meetings (next 12 months)\n\n")
    lines.append("This index is generated from the standard schedule:\n")
    lines.append("- Monday 12:00 to 13:00 (every 4 weeks)\n")
    lines.append("- Thursday 14:00 to 15:00 (every 4 weeks)\n\n")
    lines.append("This output is computed for the next 12 months from the machine's current date.\n\n")

    current_year = None
    current_month = None

    for d, slot in meetings:
        year = d.strftime("%Y")
        month = d.strftime("%B")

        if year != current_year:
            lines.append(f"## {year}\n\n")
            current_year = year
            current_month = None

        if month != current_month:
            lines.append(f"### {month}\n")
            current_month = month

        rel = folder_path_for_meeting(d)
        lines.append(f"- `{rel}` ({uk_date(d)}, {slot.label})\n")

    return "".join(lines)


def main() -> int:
    today = date.today()
    sys.stdout.write(to_markdown_index(today))
    return 0


if __name__ == "__main__":
    raise SystemExit(main())

````



```

Example output

"C:\Users\liam_\PycharmProjects\PhD Experiment Building 2026\.venv\Scripts\python.exe" "C:\Users\liam_\PycharmProjects\PhD Experiment Building 2026\meeting indexing.py" 
# Upcoming meetings (next 12 months)

This index is generated from the standard schedule:
- Monday 12:00 to 13:00 (every 4 weeks)
- Thursday 14:00 to 15:00 (every 4 weeks)

This output is computed for the next 12 months from the machine's current date.

## 2026

### February
- `2026/February/16-02-26/` (16/02/2026, Mon 12:00-13:00)
### March
- `2026/March/05-03-26/` (05/03/2026, Thu 14:00-15:00)
- `2026/March/16-03-26/` (16/03/2026, Mon 12:00-13:00)
### April
- `2026/April/02-04-26/` (02/04/2026, Thu 14:00-15:00)
- `2026/April/13-04-26/` (13/04/2026, Mon 12:00-13:00)
- `2026/April/30-04-26/` (30/04/2026, Thu 14:00-15:00)
### May
- `2026/May/11-05-26/` (11/05/2026, Mon 12:00-13:00)
- `2026/May/28-05-26/` (28/05/2026, Thu 14:00-15:00)
### June
- `2026/June/08-06-26/` (08/06/2026, Mon 12:00-13:00)
- `2026/June/25-06-26/` (25/06/2026, Thu 14:00-15:00)
### July
- `2026/July/06-07-26/` (06/07/2026, Mon 12:00-13:00)
- `2026/July/23-07-26/` (23/07/2026, Thu 14:00-15:00)
### August
- `2026/August/03-08-26/` (03/08/2026, Mon 12:00-13:00)
- `2026/August/20-08-26/` (20/08/2026, Thu 14:00-15:00)
- `2026/August/31-08-26/` (31/08/2026, Mon 12:00-13:00)
### September
- `2026/September/17-09-26/` (17/09/2026, Thu 14:00-15:00)
- `2026/September/28-09-26/` (28/09/2026, Mon 12:00-13:00)
### October
- `2026/October/15-10-26/` (15/10/2026, Thu 14:00-15:00)
- `2026/October/26-10-26/` (26/10/2026, Mon 12:00-13:00)
### November
- `2026/November/12-11-26/` (12/11/2026, Thu 14:00-15:00)
- `2026/November/23-11-26/` (23/11/2026, Mon 12:00-13:00)
### December
- `2026/December/10-12-26/` (10/12/2026, Thu 14:00-15:00)
- `2026/December/21-12-26/` (21/12/2026, Mon 12:00-13:00)
## 2027

### January
- `2027/January/07-01-27/` (07/01/2027, Thu 14:00-15:00)
- `2027/January/18-01-27/` (18/01/2027, Mon 12:00-13:00)
### February
- `2027/February/04-02-27/` (04/02/2027, Thu 14:00-15:00)
- `2027/February/15-02-27/` (15/02/2027, Mon 12:00-13:00)

Process finished with exit code 0

```
## Generating meeting directory structure (Python)

The script below generates the upcoming meeting folders for a fixed run length (in months) starting from a chosen start date, using the lab’s anchored 28 day cadence for both the Monday and Thursday slots. It creates the full directory structure under `meetings/<YEAR>/<Month>/<DD-MM-YY>/`, populates each meeting folder with the standard files (`README.md`, `minutes.md`, `action-items.md`, and a `slides/` folder), writes an updated `meetings/UPCOMING_MEETINGS_INDEX.md`, and outputs everything as a single zip file.

The run length is computed from the configured start date, so “12 months” means up to the same calendar date 12 months later (with normal month-length handling).


```python
#!/usr/bin/env python3
from __future__ import annotations

import calendar
import os
import shutil
import tempfile
import zipfile
from dataclasses import dataclass
from datetime import date, datetime, timedelta
from typing import List, Tuple

# ============================================================
# CONFIG (edit these only)
# ============================================================

START_DATE_UK = "16/02/2026"          # DD/MM/YYYY
RUN_LENGTH_MONTHS = 12               # integer months to generate ahead

INCLUDE_MEETINGS_ON_START_DATE = False

OUTPUT_ZIP_NAME = "time-and-vision-lab_meetings.zip"

MON_ANCHOR_DATE_UK = "16/02/2026"    # DD/MM/YYYY (Monday slot anchor)
THU_ANCHOR_DATE_UK = "05/03/2026"    # DD/MM/YYYY (Thursday slot anchor)

PERIOD_DAYS = 28                     # do not change unless the schedule changes

# ============================================================
# END CONFIG
# ============================================================

MONTH_NAMES = [
    "", "January", "February", "March", "April", "May", "June",
    "July", "August", "September", "October", "November", "December",
]

WEEKDAY_NAMES = [
    "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"
]


@dataclass(frozen=True)
class Slot:
    key: str
    label: str
    start_time: str
    end_time: str


MON_SLOT = Slot(key="MON", label="Mon 12:00-13:00", start_time="12:00", end_time="13:00")
THU_SLOT = Slot(key="THU", label="Thu 14:00-15:00", start_time="14:00", end_time="15:00")


def parse_uk_date(s: str) -> date:
    return datetime.strptime(s, "%d/%m/%Y").date()


def uk_date(d: date) -> str:
    return f"{d.day:02d}/{d.month:02d}/{d.year:04d}"


def dd_mm_yy(d: date) -> str:
    return f"{d.day:02d}-{d.month:02d}-{d.year % 100:02d}"


def month_name(d: date) -> str:
    return MONTH_NAMES[d.month]


def weekday_name(d: date) -> str:
    return WEEKDAY_NAMES[d.weekday()]


def add_months(d: date, months: int) -> date:
    y = d.year + (d.month - 1 + months) // 12
    m = (d.month - 1 + months) % 12 + 1
    last_day = calendar.monthrange(y, m)[1]
    day = min(d.day, last_day)
    return date(y, m, day)


def next_occurrence_on_or_after(start: date, anchor: date) -> date:
    if start <= anchor:
        return anchor
    delta_days = (start - anchor).days
    steps = (delta_days + (PERIOD_DAYS - 1)) // PERIOD_DAYS
    return anchor + timedelta(days=steps * PERIOD_DAYS)


def iter_slot(start: date, end_exclusive: date, anchor: date, slot: Slot):
    dt = next_occurrence_on_or_after(start, anchor)
    while dt < end_exclusive:
        yield dt, slot
        dt = dt + timedelta(days=PERIOD_DAYS)


def folder_relpath_for_meeting(d: date) -> str:
    y = f"{d.year:04d}"
    m = month_name(d)
    f = dd_mm_yy(d)
    return os.path.join("meetings", y, m, f)


def write_text(root: str, rel_path: str, content: str) -> None:
    full = os.path.join(root, rel_path)
    os.makedirs(os.path.dirname(full), exist_ok=True)
    with open(full, "w", encoding="utf-8") as f:
        f.write(content)


def ensure_dir(root: str, rel_path: str) -> None:
    os.makedirs(os.path.join(root, rel_path), exist_ok=True)


def meetings_readme_text() -> str:
    return (
        "# Meetings\n\n"
        "This folder holds the official record for the Time and Vision Lab Group meetings: agenda notes, minutes, action items, "
        "and any slides or supporting files shared in the session.\n\n"
        "## Folder structure\n\n"
        "Meetings are stored by year, then by month name, then by a date-coded meeting folder:\n\n"
        "`meetings/<YEAR>/<Month>/<DD-MM-YY>/`\n\n"
        "Example:\n"
        "- `meetings/2026/February/16-02-26/`\n\n"
        "## What goes inside a meeting folder\n\n"
        "Minimum expected contents:\n"
        "- `README.md` (meeting record and links)\n"
        "- `minutes.md` (minutes or discussion notes)\n"
        "- `action-items.md` (actions with owners and due dates)\n"
        "- `slides/` (slide decks presented)\n\n"
        "Optional (when useful):\n"
        "- `files/` (supporting material that is not slides)\n"
        "- `decisions.md` (if you want decisions separated from the main meeting record)\n\n"
        "## Naming conventions\n\n"
        "### Meeting folder\n"
        "Use:\n"
        "- `DD-MM-YY` (for example `16-02-26`) inside the relevant `YEAR/Month/` path.\n\n"
        "### Files (slides and supporting materials)\n"
        "Use:\n"
        "- `YYYY-MM-DD_topic_presenter.ext`\n\n"
        "Examples:\n"
        "- `2026-02-16_scanpath_similarity_liam.pptx`\n"
        "- `2026-02-16_methods_roundtable_milena.pdf`\n"
    )


def minutes_template_text() -> str:
    return (
        "# Minutes\n\n"
        "## Summary\n\n"
        "TBD\n\n"
        "## Detailed notes\n\n"
        "### Notices and updates\n\n"
        "TBD\n\n"
        "### Progress round\n\n"
        "TBD\n\n"
        "### Presentations and feedback\n\n"
        "TBD\n\n"
        "### Any other business\n\n"
        "TBD\n"
    )


def action_items_template_text() -> str:
    return (
        "# Action items\n\n"
        "- [ ] TBD (Owner: TBD, due: DD/MM/YYYY)\n"
    )


def meeting_readme_text(d: date, slot: Slot) -> str:
    slot_name = "Monday slot" if slot.key == "MON" else "Thursday slot"
    return (
        "# Time and Vision Lab Group meeting record\n\n"
        "## Meeting details\n\n"
        f"- Date: {uk_date(d)} ({weekday_name(d)} {d.day:02d} {month_name(d)} {d.year:04d})\n"
        f"- Time: {slot.start_time} to {slot.end_time} (UK)\n"
        f"- Slot: {slot_name}\n"
        "- Location: TBD (typically GDC)\n"
        "- Chair: TBD\n"
        "- Note-taker: TBD\n\n"
        "## Attendees\n\n"
        "- TBD\n\n"
        "## Apologies\n\n"
        "- TBD\n\n"
        "## Agenda\n\n"
        "1. Notices and updates\n"
        "2. Introductions (if any)\n"
        "3. Progress round\n"
        "4. Presentations and feedback\n"
        "5. Actions and decisions\n"
        "6. Any other business\n"
        "7. Next meeting\n\n"
        "## Notices and pinned items\n\n"
        "Link any relevant pinned Issues or Discussions or files in `announcements/`.\n\n"
        "- TBD\n\n"
        "## Progress round (brief)\n\n"
        "- TBD\n\n"
        "## Presentations and discussion\n\n"
        "### Talk 1: Title (Presenter)\n"
        "- Slides: `./slides/FILENAME`\n"
        "- Summary:\n"
        "- Key feedback:\n"
        "  - \n"
        "- Follow-ups:\n"
        "  - \n\n"
        "## Decisions\n\n"
        "- TBD\n\n"
        "## Action items\n\n"
        "See `action-items.md` (and link Issues here if actions are tracked as Issues).\n\n"
        "## Links and files\n\n"
        "- Minutes: `./minutes.md`\n"
        "- Action items: `./action-items.md`\n"
        "- Slides: `./slides/`\n"
        "- Supporting files (if used): `./files/`\n\n"
        "## Next meeting\n\n"
        "- Proposed date:\n"
        "- Proposed time:\n"
        "- Proposed location:\n"
        "- Proposed presenters (if known):\n"
    )


def upcoming_index_text(meetings: List[Tuple[date, Slot]]) -> str:
    lines: List[str] = []
    lines.append("# Upcoming meetings (next 12 months)\n\n")
    lines.append("This index is generated from the standard schedule:\n")
    lines.append("- Monday 12:00 to 13:00 (every 4 weeks)\n")
    lines.append("- Thursday 14:00 to 15:00 (every 4 weeks)\n\n")
    lines.append("Each entry corresponds to a folder that already exists in this repository.\n\n")

    current_year = None
    current_month = None

    for d, slot in meetings:
        y = f"{d.year:04d}"
        m = month_name(d)

        if y != current_year:
            lines.append(f"## {y}\n\n")
            current_year = y
            current_month = None

        if m != current_month:
            lines.append(f"### {m}\n")
            current_month = m

        rel = f"{y}/{m}/{dd_mm_yy(d)}/"
        lines.append(f"- `{rel}` ({uk_date(d)}, {slot.label})\n")

    return "".join(lines)


def build_zip(
    output_zip: str,
    start_date: date,
    months: int,
    mon_anchor: date,
    thu_anchor: date,
    include_start: bool,
) -> None:
    effective_start = start_date if include_start else (start_date + timedelta(days=1))
    end_exclusive = add_months(start_date, months)

    meetings: List[Tuple[date, Slot]] = []
    meetings.extend(list(iter_slot(effective_start, end_exclusive, mon_anchor, MON_SLOT)))
    meetings.extend(list(iter_slot(effective_start, end_exclusive, thu_anchor, THU_SLOT)))
    meetings.sort(key=lambda x: (x[0], x[1].key))

    tmp_root = tempfile.mkdtemp(prefix="tvlg_meetings_")
    try:
        write_text(tmp_root, os.path.join("meetings", "README.md"), meetings_readme_text())

        for d, slot in meetings:
            rel_root = folder_relpath_for_meeting(d)
            write_text(tmp_root, os.path.join(rel_root, "README.md"), meeting_readme_text(d, slot))
            write_text(tmp_root, os.path.join(rel_root, "minutes.md"), minutes_template_text())
            write_text(tmp_root, os.path.join(rel_root, "action-items.md"), action_items_template_text())
            ensure_dir(tmp_root, os.path.join(rel_root, "slides"))
            write_text(tmp_root, os.path.join(rel_root, "slides", ".gitkeep"), "")

        write_text(tmp_root, os.path.join("meetings", "UPCOMING_MEETINGS_INDEX.md"), upcoming_index_text(meetings))

        output_zip_abs = os.path.abspath(output_zip)
        os.makedirs(os.path.dirname(output_zip_abs), exist_ok=True)

        if os.path.exists(output_zip_abs):
            os.remove(output_zip_abs)

        with zipfile.ZipFile(output_zip_abs, "w", zipfile.ZIP_DEFLATED) as z:
            for root, _, files in os.walk(tmp_root):
                for fn in files:
                    full = os.path.join(root, fn)
                    rel = os.path.relpath(full, tmp_root)
                    z.write(full, rel)
    finally:
        shutil.rmtree(tmp_root, ignore_errors=True)


def main() -> int:
    start = parse_uk_date(START_DATE_UK)
    mon_anchor = parse_uk_date(MON_ANCHOR_DATE_UK)
    thu_anchor = parse_uk_date(THU_ANCHOR_DATE_UK)

    build_zip(
        output_zip=OUTPUT_ZIP_NAME,
        start_date=start,
        months=RUN_LENGTH_MONTHS,
        mon_anchor=mon_anchor,
        thu_anchor=thu_anchor,
        include_start=INCLUDE_MEETINGS_ON_START_DATE,
    )
    return 0


if __name__ == "__main__":
    raise SystemExit(main())

```
