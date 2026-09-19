# World Clock & Meeting Planner

What time is it there, and when can everyone actually meet — a world clock with a working-hours overlap grid, daylight saving handled properly, and a link you can send. Runs entirely in your browser.

**Live:** <https://world-clock.slippylabs.com/>

## What it does

- Add cities (65 curated, plus every IANA zone your browser knows — 418 of them) and read them side by side.
- Drag the time or pick a date and every clock follows; the URL always carries the setup, so it is shareable.
- An overlap grid showing, for each hour of your day, who is working, who is awake, and who is asleep — with the best meeting window named in words.
- Each row says when that city next changes its clocks, and by how much.

## How it works

No bundled timezone data. Offsets come from the browser's own IANA database through `Intl`, asked per instant: format the instant in the target zone, read the wall-clock fields back, treat them as UTC, and the difference is the offset. That cannot disagree with the formatter the page displays, and it handles the 30- and 45-minute zones for free.

Going the other way is harder, because wall time is not a function of instant in both directions. The tool builds one candidate instant per offset in force either side of the requested wall time and keeps the ones that are real: two means the time happens twice (clocks going back), none means it never happens (clocks going forward). Most tools quietly pick one and say nothing; this one says which case you are in.

## Verification

Everything is checked against Python's `zoneinfo` — the same IANA database reached by a completely different route:

- 8,400+ offset, wall-clock and DST-flag comparisons across a year for every shipped city, sampled hour by hour around every transition;
- 122,000+ wall-time→instant conversions: every hour of 2026 in 14 zones, plus quarter-hour steps through every transition, with the nonexistent and ambiguous flags checked against `fold=0`/`fold=1`;
- the next-clock-change date for every city.

Two things that surfaced and are worth knowing. **Dublin has negative daylight saving** in the database — Irish Standard Time is legally the summer time, so `dst()` is −1h in January; the DST badge here means "on the larger offset", which is what a reader expects. And **tzdata versions differ between engines**: this box's system tzdata (2026b) has British Columbia dropping DST in November 2026, while Node's bundled ICU (tz 2024b) does not. A browser can only be as current as its own copy, which the page says out loud.

## Run it locally

A static site. No build step, no package manager, no dependencies:

```
git clone git@github.com:slippylabs/world-clock.slippylabs.com.git
cd world-clock.slippylabs.com
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

---

Part of [Slippy Labs](https://slippylabs.com). Every tool is indexed at
[projects.slippylabs.com](https://projects.slippylabs.com).
