# Kimai Bulk Submit Helper – Problem & Goals

## Problem Statement
Employees at Technogise use the existing Kimai timesheet system. Kimai is good at recording time, but it lacks an efficient way to **fill and submit many day entries in one go** over a period (typically a whole month). As a result:
- Users must repeatedly navigate day-by-day or entry-by-entry.
- It’s hard to see which days or tasks are missing entries.
- Filling historical gaps (e.g., for the past month) is slow and error-prone.

You want a **separate helper web app** (not a fork of Kimai) that talks to Kimai’s public APIs against the Technogise Kimai instance, using each user’s personal API token, to:
- Show an entire month at once as a scrollable list of days.
- Auto-detect which days already have submitted entries and which are still empty.
- Let the user define a **month template** (Customer/Project/Activity/Time) once and apply it to many unfilled days.
- Let the user fill descriptions per day, review all new entries in a table, and submit them to Kimai in one bulk action.

## Target Users & Context
- **Primary users**: Technogise employees who already have access to Kimai and a personal API token.
- **Usage context**:
  - Regular weekly or monthly timesheet completion.
  - Backfilling older periods where entries are missing.
  - Quick checking of whether a given month/week is "complete".
- **Scale**: A small group (up to ~20 users initially), but design should not block more users later.

## Core User Journeys (V1)
1. **First-time connection to Kimai @ Technogise**
   - User opens the web app.
   - Sees a simple token screen: “Enter your Kimai API token”.
   - A small popup explains how to find the token in the Technogise Kimai UI.
   - User pastes the token and clicks “Validate & Save”.
   - App verifies the token by calling Kimai and, on success, stores it locally (if the user opts in).
   - User is taken straight to the **current month** filling screen.

2. **Returning user**
   - On subsequent visits, if a valid token is stored, the app skips the token screen.
   - User lands directly on the **current month view**.
   - If the token is missing or invalid, the app shows a banner or redirects back to the token screen to fix it.

3. **Month view & existing entries**
   - User selects a month (default: current month).
   - App fetches all timesheet entries for that month for the current user from Kimai.
   - Screen shows a **vertical list of days** for the month:
     - Days that already have entries in Kimai appear as compact, **read-only “already submitted” cards**.
     - Days with no entry appear as full **editable day cards** with all fields visible (Customer, Project, Activity, From/Duration, Description).

4. **Define and apply a month template**
   - At the top of the screen, the user sets a **month template**:
     - Customer, Project, Activity, From time, Duration.
   - User clicks “Apply template…” which opens a small dialog with options:
     - Apply to **all unfilled workdays in this month**.
     - Apply to unfilled days only between a chosen start and end date.
   - The template is only applied to **days that do not already have entries in Kimai**.
   - Editable day cards for those days now have their Customer/Project/Activity/Time fields pre-filled.

5. **Fill remaining days and review**
   - User scrolls through the editable day cards and mainly fills the **Description** for each unfilled day (tweaking other fields only when needed).
   - A sticky bar shows how many new day entries are ready to be created.
   - User clicks “Review & Submit”, opening a **modal with a table**:
     - One row per new day: `Include? | Date | Customer | Project | Activity | Duration | Description (truncated)`.
     - Each row has a checkbox so the user can deselect specific days before submission.

6. **Bulk submission to Kimai**
   - From the review modal, user confirms submission.
   - App sends one create request per included day to Kimai.
   - On success, the month view is refreshed from Kimai:
     - Newly created days now appear as read-only “already submitted” cards.
     - Any failed or deselected days remain as editable cards.

## Goals
- **Reduce friction and time** required to fill and submit timesheets for a whole month, especially for users who usually log a single 8-hour entry per day.
- **Make gaps obvious**: clearly highlight which days already have entries in Kimai and which days are still empty.
- **Leverage existing Kimai**: do not replace or duplicate the core system; instead, use Kimai’s public APIs.
- **Keep architecture simple**: start as a **pure frontend React SPA** that runs fully in the browser against the fixed Technogise Kimai URL.
- **Respect tokens and privacy**: avoid storing Kimai API tokens on any server; keep them in the user’s browser only.

## Explicit Non-Goals (V1)
- No replacement for Kimai’s own UI, approvals, or reporting.
- No separate backend database for timesheets; **Kimai remains the source of truth**.
- No complex roles/permissions beyond what Kimai already enforces.
- No advanced analytics/dashboards (beyond basic completeness indication).
- No organization-wide user management or SSO; each user manages their own Kimai API token.

These boundaries keep the first version focused on the pain point: **fast, bulk-friendly timesheet entry and submission on top of Kimai.**
