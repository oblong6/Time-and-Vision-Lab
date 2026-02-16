
# Upcoming meetings (next 12 months)

This index is generated from the standard alternating slot schedule:
- Monday 12:00 to 13:00
- Thursday 14:00 to 15:00

Each entry corresponds to a folder that already exists in this repository.

## Regenerating this index (Python)

The script below computes the next 12 months of meeting dates from the machine’s current date and time (it uses your system clock at runtime). It outputs a markdown index in the same style as this file.

Important: the alternating Monday/Thursday pattern needs a phase anchor (otherwise there are two valid “offsets”). The script includes an `ANCHOR_DATE` that represents a known Monday-slot meeting. If the schedule is ever reset, update the anchor to a known Monday-slot meeting date.

```python
#!/usr/bin/env python3
"""
Compute the next 12 months of Time and Vision Lab Group meeting dates.

Schedule:
- Alternating slots:
  - Monday 12:00-13:00
  - Thursday 14:00-15:00
- The combined sequence is:
  - Monday -> Thursday (+10 days)
  - Thursday -> Monday (+18 days)

This script generates an index for the next 12 months from the machine's current
date (system clock), and prints markdown to stdout.

Note on phase:
The alternation requires a known anchor meeting date for the Monday slot.
Update ANCHOR_DATE if the schedule phase changes.
"""

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
    weekday: int  # Monday=0 ... Sunday=6


MON_SLOT = Slot(
    key="MON",
    label="Mon 12:00-13:00",
    start_time="12:00",
    end_time="13:00",
    weekday=0,
)

THU_SLOT = Slot(
    key="THU",
    label="Thu 14:00-15:00",
    start_time="14:00",
    end_time="15:00",
    weekday=3,
)

# Phase anchor: a known Monday-slot meeting date.
# Update this if the schedule phase changes.
ANCHOR_DATE = date(2026, 2, 16)
ANCHOR_SLOT = MON_SLOT


def add_one_year_safe(d: date) -> date:
    """
    Return the same calendar date next year where possible.
    If the date does not exist next year (29 Feb), fall back to 28 Feb.
    """
    try:
        return date(d.year + 1, d.month, d.day)
    except ValueError:
        # Handles 29 Feb
        if d.month == 2 and d.day == 29:
            return date(d.year + 1, 2, 28)
        raise


def next_meeting_on_or_after(start: date) -> tuple[date, Slot]:
    """
    Given a start date, return the next meeting date on or after it,
    following the anchored alternating schedule.
    """
    dt = ANCHOR_DATE
    slot = ANCHOR_SLOT

    # Step forward until we reach start
    while dt < start:
        if slot.key == "MON":
            dt = dt + timedelta(days=10)  # Mon -> Thu
            slot = THU_SLOT
        else:
            dt = dt + timedelta(days=18)  # Thu -> Mon
            slot = MON_SLOT

    return dt, slot


def iter_meetings(start: date, end_exclusive: date):
    """
    Yield (date, slot) pairs from the schedule, starting at the first meeting
    on or after 'start', stopping before 'end_exclusive'.
    """
    dt, slot = next_meeting_on_or_after(start)

    while dt < end_exclusive:
        yield dt, slot
        if slot.key == "MON":
            dt = dt + timedelta(days=10)
            slot = THU_SLOT
        else:
            dt = dt + timedelta(days=18)
            slot = MON_SLOT


def folder_path_for_meeting(d: date) -> str:
    """
    Path fragment using the repository convention:
    YEAR/Month/DD-MM-YY/
    """
    year = d.strftime("%Y")
    month = d.strftime("%B")
    ddmmyy = d.strftime("%d-%m-%y")
    return f"{year}/{month}/{ddmmyy}/"


def to_markdown_index(start: date) -> str:
    end_exclusive = add_one_year_safe(start) + timedelta(days=1)

    lines: list[str] = []
    lines.append("# Upcoming meetings (next 12 months)\n\n")
    lines.append(
        "This index is generated from the standard alternating slot schedule "
        "(Mon 12:00-13:00, Thu 14:00-15:00).\n"
    )
    lines.append(
        "This output is computed for the next 12 months from the machine's current date.\n\n"
    )

    current_year = None
    current_month = None

    for d, slot in iter_meetings(start=start, end_exclusive=end_exclusive):
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
        lines.append(f"- `{rel}` ({d.isoformat()}, {slot.label})\n")

        # blank line after each month block (when month changes) is handled naturally
        # by the next "###" or "##" header. Add a newline between months for readability.
        # (We insert it when the next month/year header is emitted.)

    return "".join(lines)


def main() -> int:
    today = date.today()
    md = to_markdown_index(start=today)
    sys.stdout.write(md)
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
````

## 2026

### February

* `2026/February/26-02-26/` (2026-02-26, Thu 14:00-15:00)

### March

* `2026/March/16-03-26/` (2026-03-16, Mon 12:00-13:00)
* `2026/March/26-03-26/` (2026-03-26, Thu 14:00-15:00)

### April

* `2026/April/13-04-26/` (2026-04-13, Mon 12:00-13:00)
* `2026/April/23-04-26/` (2026-04-23, Thu 14:00-15:00)

### May

* `2026/May/11-05-26/` (2026-05-11, Mon 12:00-13:00)
* `2026/May/21-05-26/` (2026-05-21, Thu 14:00-15:00)

### June

* `2026/June/08-06-26/` (2026-06-08, Mon 12:00-13:00)
* `2026/June/18-06-26/` (2026-06-18, Thu 14:00-15:00)

### July

* `2026/July/06-07-26/` (2026-07-06, Mon 12:00-13:00)
* `2026/July/16-07-26/` (2026-07-16, Thu 14:00-15:00)

### August

* `2026/August/03-08-26/` (2026-08-03, Mon 12:00-13:00)
* `2026/August/13-08-26/` (2026-08-13, Thu 14:00-15:00)
* `2026/August/31-08-26/` (2026-08-31, Mon 12:00-13:00)

### September

* `2026/September/10-09-26/` (2026-09-10, Thu 14:00-15:00)
* `2026/September/28-09-26/` (2026-09-28, Mon 12:00-13:00)

### October

* `2026/October/08-10-26/` (2026-10-08, Thu 14:00-15:00)
* `2026/October/26-10-26/` (2026-10-26, Mon 12:00-13:00)

### November

* `2026/November/05-11-26/` (2026-11-05, Thu 14:00-15:00)
* `2026/November/23-11-26/` (2026-11-23, Mon 12:00-13:00)

### December

* `2026/December/03-12-26/` (2026-12-03, Thu 14:00-15:00)
* `2026/December/21-12-26/` (2026-12-21, Mon 12:00-13:00)
* `2026/December/31-12-26/` (2026-12-31, Thu 14:00-15:00)

## 2027

### January

* `2027/January/18-01-27/` (2027-01-18, Mon 12:00-13:00)
* `2027/January/28-01-27/` (2027-01-28, Thu 14:00-15:00)

### February

* `2027/February/15-02-27/` (2027-02-15, Mon 12:00-13:00)

```

Example output

"C:\Users\liam_\PycharmProjects\PhD Experiment Building 2026\.venv\Scripts\python.exe" "C:\Users\liam_\PycharmProjects\PhD Experiment Building 2026\meeting indexing.py" 
# Upcoming meetings (next 12 months)

This index is generated from the standard alternating slot schedule (Mon 12:00-13:00, Thu 14:00-15:00).
This output is computed for the next 12 months from the machine's current date.

## 2026

### February
- `2026/February/16-02-26/` (2026-02-16, Mon 12:00-13:00)
- `2026/February/26-02-26/` (2026-02-26, Thu 14:00-15:00)
### March
- `2026/March/16-03-26/` (2026-03-16, Mon 12:00-13:00)
- `2026/March/26-03-26/` (2026-03-26, Thu 14:00-15:00)
### April
- `2026/April/13-04-26/` (2026-04-13, Mon 12:00-13:00)
- `2026/April/23-04-26/` (2026-04-23, Thu 14:00-15:00)
### May
- `2026/May/11-05-26/` (2026-05-11, Mon 12:00-13:00)
- `2026/May/21-05-26/` (2026-05-21, Thu 14:00-15:00)
### June
- `2026/June/08-06-26/` (2026-06-08, Mon 12:00-13:00)
- `2026/June/18-06-26/` (2026-06-18, Thu 14:00-15:00)
### July
- `2026/July/06-07-26/` (2026-07-06, Mon 12:00-13:00)
- `2026/July/16-07-26/` (2026-07-16, Thu 14:00-15:00)
### August
- `2026/August/03-08-26/` (2026-08-03, Mon 12:00-13:00)
- `2026/August/13-08-26/` (2026-08-13, Thu 14:00-15:00)
- `2026/August/31-08-26/` (2026-08-31, Mon 12:00-13:00)
### September
- `2026/September/10-09-26/` (2026-09-10, Thu 14:00-15:00)
- `2026/September/28-09-26/` (2026-09-28, Mon 12:00-13:00)
### October
- `2026/October/08-10-26/` (2026-10-08, Thu 14:00-15:00)
- `2026/October/26-10-26/` (2026-10-26, Mon 12:00-13:00)
### November
- `2026/November/05-11-26/` (2026-11-05, Thu 14:00-15:00)
- `2026/November/23-11-26/` (2026-11-23, Mon 12:00-13:00)
### December
- `2026/December/03-12-26/` (2026-12-03, Thu 14:00-15:00)
- `2026/December/21-12-26/` (2026-12-21, Mon 12:00-13:00)
- `2026/December/31-12-26/` (2026-12-31, Thu 14:00-15:00)
## 2027

### January
- `2027/January/18-01-27/` (2027-01-18, Mon 12:00-13:00)
- `2027/January/28-01-27/` (2027-01-28, Thu 14:00-15:00)
### February
- `2027/February/15-02-27/` (2027-02-15, Mon 12:00-13:00)

Process finished with exit code 0
```
