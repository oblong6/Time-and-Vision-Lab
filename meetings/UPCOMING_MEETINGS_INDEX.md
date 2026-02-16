
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
