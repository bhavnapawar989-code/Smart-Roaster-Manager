# Smart Roster Manager

Browser-based monthly roster planner for multi-brand operations teams. Single file (`index.html`), vanilla JS, no build step, no backend. State lives in `localStorage`.

**Run:** open `index.html`, or host on GitHub Pages / Netlify / Vercel (drag and drop the file).

## Sample data
30 employees, 24 brands, 33 processes, 4 pods with leads. 19 brands (79%) have one owner (4 owners handle two brands each); 5 brands have 2-3 employees with a mix of separate and shared processes (e.g. Zaara: CS 365 days, ACC Mon-Sat, NDR daily except PH). Nutra needs min 2 on CS. 2 bench employees act as backups. Seeded leave and comp-off exist, and brands such as Glow/Loom are single-owner, so real gaps show up immediately.

## Assumptions
- Calendars: `365` (all days), `DNH` (daily, public holidays off), `SNH` (Mon-Sat, Sundays and PH off). Min staffing per process, with an optional different minimum on Sat/Sun.
- Each employee gets 1 weekly off per Mon-Sun week. A closed day (Sunday/PH where all their processes are closed), leave or comp-off in that week counts as the rest day.
- Brand calendar and employee schedule are separate: the calendar defines `need`, the employee status defines `have`.
- Mandatory = coverage (red gap). Soft = preferred off day (reported as a ratio, never overrides coverage).

## Tell us how you think
1. **Model:** `Pod -> Employee`, `Brand -> Process (calendar, min staff)`, `Employee <-> Process` many-to-many (an employee can hold processes across brands). Leaves, comp-offs and weekly offs are marks per employee-date. Everything is data, editable in Setup.
2. **Generation and checking:** greedy per week. For each employee, choose the weekly-off day that creates the fewest coverage gaps, then matches their preferred day, then balances offs across days (fairness). Validation recomputes `have` vs `need` for every process and day from scratch, so manual edits are always checked.
3. **Single vs multi:** the same rule covers both. A sole owner has `have = 1`, so any off on an open day is a gap; multi-employee processes can rotate offs. Single owners on Mon-Sat calendars naturally rest on Sunday.
4. **Sole owner unavailable:** a red gap appears on that process/day, plus ranked suggestions: bench or spare-capacity employees who are working and would not create a gap elsewhere (one-click "Cover"), or a swap request for teammates on a weekly off. Use "Simulate a change" to see it.
5. **Conflicts:** never silently hidden. Gaps (errors), missing weekly off and double-booking across brands (warnings) are listed with date and cause. If no one can cover, it says so.
6. **Automation vs human:** weekly offs and gap detection are automated; leave approval, applying cover and final sign-off stay manual since they carry business and people judgement.
7. **Doubling scale:** move to a backend DB (Postgres), replace greedy with a CP-SAT/ILP solver, index coverage incrementally instead of full recompute, add auth and role-based approvals.
8. **One more week:** CSV/Excel export, locked cells that survive regeneration, pod-lead approval workflow, multi-week consecutive-day limits, and automated tests for the scheduler.

## Known limits
Greedy generator (not globally optimal); no shifts within a day; no export or notifications yet.
