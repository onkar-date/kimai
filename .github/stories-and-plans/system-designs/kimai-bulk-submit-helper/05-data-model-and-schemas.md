# Data Model & Schemas – Kimai Bulk Submit Helper

This app does not introduce a new backend database. All authoritative timesheet data lives in **Kimai**. However, the frontend still maintains its own **in-memory models** and uses browser storage for configuration.

## Conceptual Entities (Frontend)

### 1. UserConfig
Represents how the app connects to Kimai for a specific user.

Fields (conceptual):
- `apiToken` – string, personal API token (sensitive; stored only in browser if user opts in).
- `userId` – string, optional, inferred from Kimai after validation.
- `persistLocally` – boolean, whether to store the token in `localStorage`.
- `kimaiBaseUrl` – string, optional; fixed Technogise Kimai URL compiled into the app (not user-editable).

### 2. Project (Kimai Reference Data)
Simplified view of what the app needs from Kimai’s project/customer model.

Fields (example):
- `id` – string or number, Kimai project ID.
- `name` – string, display name.
- `customerName` – string, optional, for grouping or labeling.
- `isActive` – boolean.

### 3. Activity / Task (Kimai Reference Data)
Represents an activity/task in Kimai, often associated with a project.

Fields (example):
- `id` – string or number.
- `name` – string.
- `projectId` – string or number, link to Project.
- `isActive` – boolean.

### 4. TimesheetEntry (Kimai Timesheet Record)
Represents an existing timesheet entry fetched from Kimai.

Fields (example):
- `id` – string or number (Kimai timesheet ID).
- `userId` – string or number.
- `projectId` – string or number.
- `activityId` – string or number.
- `begin` – date/time string (start).
- `end` – date/time string (end), or `duration` – numeric.
- `description` – string, optional.

The frontend may normalize this into a simpler per-day representation when building the month view, e.g. by aggregating to `date` and total `hours` for that day.

### 5. MonthTemplate
Represents the per-month defaults that are applied to many unfilled days.

Fields (conceptual):
- `customerId` – string or number.
- `projectId` – string or number.
- `activityId` – string or number.
- `fromTime` – time-of-day string (e.g., `09:00`).
- `durationHours` – numeric, typical daily hours (e.g., `8`).

### 6. DayDraft
Represents the draft data for a single day in the month view (only for days that do not yet have entries in Kimai).

Fields (conceptual):
- `date` – date string (YYYY-MM-DD).
- `customerId` – string or number.
- `projectId` – string or number.
- `activityId` – string or number.
- `fromTime` – time-of-day string.
- `durationHours` – numeric.
- `description` – string.
- `fromTemplate` – boolean, whether initial values came from the month template.

### 7. SubmissionOperation
Represents a single planned API call to Kimai during bulk submission.

Fields (conceptual):
- `type` – enum: `"create"` or `"update"` (V1 can start with `"create"` only).
- `targetEntryId` – optional, required for `"update"`.
- `payload` – object with fields needed by Kimai (project, activity, date/time, duration, description).
- `dayRef` – link back to the corresponding DayDraft/date so the UI can mark success/failure.
- `status` – enum: `"pending"`, `"success"`, `"failed"`.
- `errorMessage` – optional string for failures.

## Browser Storage Schema

### LocalSettingsStore
The app uses a dedicated module to read/write JSON blobs to `localStorage` (or similar). Example keys:

- `kimaiConfig`:
  - Stores serialized `UserConfig` (excluding token if user chose not to persist it).
- `lastMonthSelection`:
  - `{ "year": number, "month": number }`.

No timesheet data is persisted; it is always fetched from Kimai on demand.

## Data Relationships

- A **Project** can have many **Activities**.
- A **TimesheetEntry** belongs to one **Project** and one **Activity**, and is associated with a **User** and a specific **date/time range**.
- A **MonthTemplate** provides default values used to initialise multiple **DayDrafts**.
- A **DayDraft** is uniquely identified by `date` and refers to a specific project/activity combination for that day.

## Indexing / Performance Considerations (Frontend Only)

Even though this is all client-side, it is useful to structure data for efficient lookups:

- Maintain maps keyed by ID:
  - `projectsById[projectId]`.
  - `activitiesById[activityId]`.
- For timesheets:
  - Index by date for quick access:
    - `entriesByDate[date]` → aggregated TimesheetEntry info for that day.
- For drafts:
  - Keep a dictionary of DayDrafts keyed by `date`.

This structure keeps the UI responsive even when dealing with a few hundred entries in a month view.

## Summary
The data model is intentionally **simple and frontend-focused**:
- Kimai remains the authoritative store.
- The app builds an in-memory representation tailored for month-based, per-day interaction and bulk submission.
- Browser storage is used only for configuration and small preferences, not for storing timesheets.
