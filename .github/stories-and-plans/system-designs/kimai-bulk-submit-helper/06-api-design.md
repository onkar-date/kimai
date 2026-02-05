# API Design – Kimai Bulk Submit Helper

This project does **not** define its own backend APIs. Instead, the React app calls **Kimai’s existing HTTP APIs** directly from the browser.

This document describes:
- The internal "operations" the frontend performs.
- How those operations conceptually map to Kimai’s APIs.

> Note: Endpoint names and payloads here are **conceptual** and should be aligned with the actual Kimai API documentation during implementation.

## 1. Configuration & Validation Operations

### Operation: Validate Connection
- **Purpose**: Ensure the provided Kimai URL and API token are valid.
- **Triggered by**: User saving configuration.
- **Flow**:
  1. Call a simple Kimai endpoint (e.g., "current user" or "ping") with the provided token.
  2. If the call succeeds, treat the configuration as valid.
  3. Optionally store `userId` from the response.

### Operation: Save Configuration (Local)
- **Purpose**: Store configuration in browser storage (optional).
- **Request (internal)**:
  - `UserConfig` object.
- **Behavior**:
  - Serialize and save to `localStorage` (excluding token if user opts out of persistence).

## 2. Data Fetch Operations

### Operation: Fetch Reference Data
- **Purpose**: Load projects and activities needed to build rows and labels.
- **Triggered by**: First valid configuration, or when user refreshes.
- **Flow**:
  - Call Kimai endpoints to list projects and activities accessible to the user.
  - Store results in memory (and optionally cache in session).

### Operation: Fetch Timesheets for Period
- **Purpose**: Load all timesheet entries for the current user within a date range.
- **Triggered by**: User selects a new month/week.
- **Input** (internal):
  - `from` – start date (inclusive).
  - `to` – end date (inclusive or exclusive depending on Kimai API semantics).
- **Flow**:
  - Call Kimai timesheet list endpoint with filters for:
    - User (derived from token or userId).
    - Date range.
  - Normalize response into `TimesheetEntry` and the grid model.

## 3. Grid & Draft Operations (Internal)

These are internal operations implemented in the frontend.

### Operation: Set Cell Draft
- **Purpose**: Record a user edit to a specific grid cell.
- **Input**:
  - `rowKey` – identifies project/activity.
  - `date` – identifies day.
  - `value` – hours (and optionally description or flags).
- **Behavior**:
  - Update draft state (in memory) and mark cell as `modified` or `new`.

### Operation: Compute Submission Plan
- **Purpose**: Derive a list of API operations needed to sync drafts with Kimai.
- **Input**:
  - Current drafts.
  - Existing entries for the period.
- **Behavior**:
  - For each draft cell:
    - If there is no existing entry and value > 0 → create operation.
    - If there is an existing entry and value changed → update operation (in later phases).
    - If value is 0 and there was an entry → optionally delete or ignore (depending on UX rules; likely ignore for V1).

## 4. Submission Operations (Against Kimai)

### Operation: Bulk Submit Drafts
- **Purpose**: Apply all draft changes to Kimai.
- **Triggered by**: User clicks "Submit" in the BulkSubmitPanel.
- **Input** (internal):
  - List of `SubmissionOperation` objects, each with its payload and cell reference.
- **Behavior**:
  1. For each `SubmissionOperation`:
     - For `type = "create"`:
       - Call Kimai timesheet create endpoint with payload derived from:
         - Project ID.
         - Activity ID.
         - Date/time (mapped from `date` + default working hours or start time scheme).
         - Duration or hours.
         - Description (if provided).
     - For `type = "update"` (if implemented later):
       - Call Kimai timesheet update endpoint with new values.
  2. Execute operations sequentially or in small batches.
  3. Mark each operation as `success` or `failed` and record any error messages.
  4. After completion, refetch timesheets for the period to sync the grid.

### Error Handling
- On individual operation failure:
  - Mark the corresponding cell with an error state.
  - Show a concise error message (e.g., "Invalid project/activity" or "Validation failed").
- On network/authorization error for all operations:
  - Abort remaining submissions.
  - Show a top-level error (e.g., "Unable to reach Kimai" or "Token expired/invalid").

## 5. Internal Hook / Service Interfaces

To keep React code clean, wrap raw API calls in reusable hooks/services:

### Hook: useKimaiConfig
- **Responsibilities**:
  - Manage current `UserConfig` state.
  - Provide methods to validate, save, and clear configuration.

### Hook: useTimesheetData
- **Responsibilities**:
  - Given a `UserConfig` and a period (`from`, `to`), fetch:
    - Reference data (projects, activities) if not already loaded.
    - Timesheets for that period.
  - Expose:
    - `data` – normalized grid model.
    - `loading` / `error` states.
    - `refresh` method.

### Hook: useDraftsAndSubmission
- **Responsibilities**:
  - Manage draft cell state.
  - Build submission plan.
  - Execute bulk submit and track progress.

## Summary

Even without a custom backend, the app has a clear **operation model**:
- Validate connection → fetch reference data → fetch timesheets → edit drafts → compute submission plan → submit to Kimai.

These operations will be implemented as:
- Small, focused functions and hooks in the frontend.
- Thin wrappers over actual Kimai endpoints, keeping Kimai as the source of truth.
