# Components & Responsibilities – Kimai Bulk Submit Helper

## Overview

The system is a React single-page application. This document describes the **main frontend components and modules**, and what each is responsible for.

## UI Components

### 1. App Shell
- **Responsibility**:
  - Overall layout and routing (if needed).
  - High-level loading/error boundaries.
  - Deciding whether to show configuration screen or main timesheet view.
- **Interactions**:
  - Reads stored configuration from browser storage at startup.
  - Passes configuration and callbacks down to other components.

### 2. ConfigurationPanel
- **Responsibility**:
  - UI for entering and editing the user’s **Kimai API token** (Technogise Kimai URL is fixed in code).
  - Showing a small helper popup with step-by-step instructions on how to obtain the token from the Kimai UI.
  - Triggering validation call to Kimai.
  - Storing the token locally (if user agrees).
- **Interactions**:
  - Calls KimaiApiClient to validate the token.
  - On success, notifies App Shell with a valid configuration object.

### 3. MonthSelector
- **Responsibility**:
  - Allow user to pick a month (e.g., via month dropdown or previous/next month buttons).
  - Compute and expose the actual start and end dates for the month.
- **Interactions**:
  - Receives current month from parent.
  - Emits new month selection.

### 4. MonthTemplatePanel
- **Responsibility**:
  - UI for defining the **month template** fields that are usually constant:
    - Customer.
    - Project.
    - Activity.
    - From time and Duration.
  - Triggering the "Apply template…" dialog.
- **Interactions**:
  - Receives current template values and change handlers from parent.
  - When user clicks "Apply…", opens an ApplyTemplateDialog (or passes control up).

### 5. ApplyTemplateDialog
- **Responsibility**:
  - Let the user decide where to apply the month template:
    - All unfilled workdays in the month.
    - Only unfilled days in a selected date range.
  - Confirm or cancel template application.
- **Interactions**:
  - Receives month boundaries and list of currently unfilled days.
  - Emits an action describing which days should receive template defaults.

### 6. MonthView / DayCardsList
- **Responsibility**:
  - Render a scrollable list of days for the selected month.
  - For each day, decide whether to show a **SubmittedDayCard** or an **EditableDayCard**.
- **Interactions**:
  - Receives:
    - Month dates.
    - Existing timesheet entries from Kimai.
    - Draft day entries.
    - Applied template defaults.
  - Emits changes when user edits an editable day (e.g., `onDayDraftChange(date, draft)`).

### 7. SubmittedDayCard
- **Responsibility**:
  - Show a compact, read-only summary for days that already have entries in Kimai.
  - Clearly indicate that the day is "Submitted" and not editable in V1.
- **Interactions**:
  - Receives summarized entry data (customer/project/activity, duration, description first line).

### 8. EditableDayCard
- **Responsibility**:
  - Show full form fields for days without existing entries:
    - Date (display only).
    - Customer, Project, Activity.
    - From time, Duration/End time.
    - Description textarea.
  - Pre-fill fields from the month template when applicable.
- **Interactions**:
  - Receives current draft values and validation rules.
  - Emits updated draft when user edits any field.

### 9. ReviewModal
- **Responsibility**:
  - Present a table of all draft day entries that will be submitted to Kimai.
  - Allow the user to deselect specific days via checkboxes.
  - Confirm or cancel the bulk submission.
- **Interactions**:
  - Receives a list of draft entries (one per day) and submission status.
  - Emits a confirm action with the subset of days that should be submitted.

### 10. CompletenessSummary / Legend
- **Responsibility**:
  - Display a quick visual summary of the selected period:
    - Days fully filled vs. underfilled vs. empty.
    - Simple indicators (colors, badges) and legend.
- **Interactions**:
  - Receives aggregated data (e.g., total hours per day) from parent.

## Logic & Service Modules

### 11. KimaiApiClient
- **Responsibility**:
  - Encapsulate all HTTP calls to Kimai.
  - Hide details of URL construction, headers, query parameters.
- **Key methods (conceptual)**:
  - `validateConnection(config)` – check token and URL.
  - `fetchUserProfile(config)` – optional, to confirm which user the token belongs to.
  - `fetchReferenceData(config)` – projects, activities, etc.
  - `fetchTimesheets(config, { from, to })` – entries for a date range.
  - `createTimesheet(config, payload)` – create a new timesheet entry.
  - `updateTimesheet(config, id, payload)` – optional, for editing existing entries in later phases.
- **Interactions**:
  - Used by React components (often via hooks) to fetch and mutate Kimai data.

### 12. PeriodUtils
- **Responsibility**:
  - Utility functions for date calculations:
    - Given a year + month, return all dates in that month.
    - Given a date, compute its week range.
    - Helpers for formatting dates for display vs. sending to Kimai.

### 13. MonthModelBuilder
- **Responsibility**:
  - Transform raw Kimai data (projects, activities, timesheets) into a month-friendly representation.
  - Determine for each date in the month whether it is:
    - A submitted day (has existing entries).
    - An unfilled day (no entries yet).
  - Provide structures used by MonthView and CompletenessSummary.

### 14. DraftStateManager
- **Responsibility**:
  - Hold and manipulate **per-day draft entries** (client-side only).
  - Provide operations such as:
    - `setDayDraft(date, draft)`.
    - `clearDrafts()`.
    - `buildSubmissionOperations()` – convert day drafts into a list of create operations.

### 15. LocalSettingsStore
- **Responsibility**:
  - Abstract access to browser storage (e.g., `localStorage`).
  - Store and retrieve:
    - Kimai API token (if user opts in).
    - Last selected month and view options.
  - Provide a simple API (e.g., `loadConfig`, `saveConfig`, `clearConfig`).

## Cross-Cutting Concerns

### Error Handling & Notifications
- Centralized mechanism (e.g., a simple notification/toast component) for:
  - API errors (failed fetch, failed submit).
  - Validation errors (invalid hours format, missing required fields).

### Loading States
- Components should expose clear loading indicators when:
  - Validating configuration.
  - Fetching data for a new period.
  - Submitting bulk changes.

## Summary
This component breakdown keeps the app **modular and understandable**:
- UI components focus on rendering and user interaction.
- Service/utility modules encapsulate API communication, date logic, and grid modeling.

That separation will make it easier to implement the first version quickly and extend it later (e.g., adding more views, supporting updates to existing entries, or more advanced visualizations).
