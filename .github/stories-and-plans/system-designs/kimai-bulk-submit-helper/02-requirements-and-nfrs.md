# Requirements & NFRs – Kimai Bulk Submit Helper

## Functional Requirements

### 1. Kimai Connection & Configuration (Technogise)
- Kimai base URL is **fixed in the app configuration** to the Technogise Kimai instance.
- User only needs to provide a **personal Kimai API token**.
- App validates configuration by calling a simple Kimai endpoint (e.g., current user or ping).
- A small helper popup explains how to find the token in the Technogise Kimai UI (step-by-step).
- App can optionally store the token in browser storage (localStorage or similar) to avoid re-entering each time.
- User can clear or update the stored token at any time.

### 2. Period Selection (Month-Focused)
- Primary flow is **month-based**:
  - User selects a month (e.g., "February 2026") using a month picker or previous/next controls.
- App computes the exact date range (start/end dates) for the month.
- Week view can be added later as an enhancement, but is not required for V1.

### 3. Fetching Existing Timesheet Entries
- For the selected month, the app queries Kimai for **all timesheet entries** for the current user.
- The app also fetches required reference data (as needed by the UI), such as:
  - Customers / projects.
  - Activities / tasks.
- The app merges these into an in-memory model suitable for a **per-day list view**, distinguishing:
  - Days that already have entries (submitted days).
  - Days that have no entry yet (unfilled days).

### 4. Month View – Per-Day Cards
- The app presents a **vertical list of days** for the selected month:
  - Each day shows its date and weekday.
  - If Kimai already has an entry for that day:
    - Show a compact, **read-only card** summarizing the entry (customer/project/activity, duration, description first line).
    - Mark it visually as "Submitted"; no editing is allowed in V1.
  - If the day has no entry:
    - Show a full **editable day card** with inputs for:
      - Customer.
      - Project.
      - Activity.
      - From time and Duration/End time.
      - Description (multi-line textarea).
- The list should be scrollable and easy to scan so the user can see the entire month on one screen.

### 5. Month Template & Draft Entries
- **Month template**:
  - At the top of the screen, user can define a template for fields that are usually constant:
    - Customer.
    - Project.
    - Activity.
    - From time and Duration.
  - When the user clicks "Apply template…", open a dialog that lets them choose:
    - "Apply to **all unfilled workdays** in this month".
    - "Apply to unfilled days only from [start date] to [end date]".
  - Template application rules:
    - Only days without existing entries in Kimai are affected.
    - Existing entries are never overwritten.
- **Draft entries per day**:
  - For each unfilled day card, after template application, the user mainly edits the **Description** field (and can tweak other fields if needed).
  - Draft values (for days that do not yet exist in Kimai) are stored client-side in React state until explicitly submitted.
  - The app tracks which days have draft entries vs. those that are untouched.

### 6. Review Modal & Bulk Submission
- When the user is ready, they click **"Review & Submit"**.
- The app opens a **modal with a table** summarizing all draft entries that will create new Kimai records:
  - Columns: `Include? | Date | Customer | Project | Activity | Duration | Description (truncated)`.
  - Each row corresponds to one unfilled day that has a draft entry.
  - The `Include?` column has a checkbox so the user can deselect specific days before submission.
- When the user confirms submission:
  - App builds a list of **create operations** (one per included day) to send to Kimai.
  - App executes these operations, sequentially or in small batches to avoid rate limits.
- App shows submission feedback:
  - Overall status (e.g., "7 of 7 entries submitted successfully").
  - Per-day success/failure indication (e.g., icons in the table and toast notifications).
- On success, app refreshes the month view from Kimai so that newly submitted days now appear as read-only "Submitted" cards.

### 7. Basic History / Completeness Visualization
- For the selected month, the UI provides **visual cues** for:
  - Days with existing entries (submitted days).
  - Days without entries (missing days); optionally, days considered underfilled based on a target hours rule.
- A small summary at the top or bottom can show counts such as:
  - "Submitted days: X", "Unfilled days: Y".
- The app does not need a separate history database; it computes this from fetched Kimai data.

### 8. Error Handling (Functional)
- If Kimai is unreachable or returns errors:
  - Show a clear, user-friendly error message.
  - Allow user to retry or adjust configuration.
- If specific entries fail to submit (e.g., validation errors):
  - Mark corresponding cells and show a short error description.

---

## Non-Functional Requirements (Lightweight)

### Performance
- Optimized for **single-user / small-group** usage:
  - Period queries typically cover 1 month or 1 week at a time.
  - The app should remain responsive for a few hundred entries in the grid.
- Fetch operations should be limited to what is needed for the selected period.

### Reliability & Availability
- No backend server component for this app; it is a **static React SPA**.
- Availability primarily depends on:
  - Where the static assets are hosted (e.g., GitHub Pages, internal web server).
  - Kimai’s own uptime.
- The app should:
  - Handle transient Kimai errors gracefully (with messages and retry options).
  - Avoid leaving the user in an inconsistent state after partial submissions (always allow refresh/reload).

### Security & Privacy
- Kimai API tokens and base URLs:
  - Are **never sent to any server** controlled by this app; calls go directly from browser → Kimai.
  - If persisted, are stored only in browser storage (e.g., localStorage) on the user’s machine.
- Users can explicitly clear stored configuration.
- All communication with Kimai should be over **HTTPS**.
- The app does not introduce new authentication/authorization; Kimai remains the authority.

### Maintainability & Simplicity
- Keep the codebase small and understandable:
  - Clear separation between UI components, state management, and Kimai API client code.
- Avoid premature optimization or complex abstractions:
  - Focus on the core bulk-view-and-submit use case.

### Compatibility
- Target modern desktop browsers (latest Chrome/Edge/Firefox) used in the company.
- Mobile support is optional and not a primary goal for V1.

These requirements emphasize a practical tool: **simple, safe to run from the browser, and tightly focused on making Kimai timesheet submission faster and less error-prone.**
