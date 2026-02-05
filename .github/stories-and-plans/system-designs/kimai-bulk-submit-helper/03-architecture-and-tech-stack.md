# Architecture & Tech Stack – Kimai Bulk Submit Helper

## High-Level Architecture

The system is designed as a **pure frontend web application** that runs entirely in the user’s browser and communicates **directly** with the existing Technogise Kimai server via its public HTTP APIs.

```mermaid
flowchart LR
  UserBrowser[User Browser (React SPA)] -->|HTTPS, API token| Kimai[(Existing Kimai Server)]
```

### Key Characteristics
- **No custom backend** for this project.
  - All business logic (period selection, month template application, draft management) runs in the browser.
  - All persistent timesheet data lives in Kimai.
- **Configuration and small preferences** (Kimai API token, last used month) are stored in browser storage.
- The Kimai base URL for Technogise is compiled into the frontend (not user-editable).
- The app is deployed as **static assets** (HTML, JS, CSS) on a simple web host.

## Core Architectural Responsibilities

1. **React SPA (Browser)**
  - UI for configuration, month selection, and per-day timesheet editing.
   - State management for:
    - Current user configuration (Kimai API token and small preferences).
    - Selected month.
    - Fetched Kimai data (projects, activities, timesheets).
    - Month template (customer/project/activity/time defaults).
    - Draft day entries (new entries prior to submission).
   - Orchestration of API calls to Kimai for fetching and submitting data.

2. **Kimai Server (Existing)**
   - Remains the **system of record** for all timesheet data.
   - Exposes REST APIs for listing and creating/updating timesheet entries, and fetching reference data.
   - Enforces authentication and authorization via API tokens.

## Data Flow (Typical Scenario)

1. User opens the web app in the browser.
2. If no valid token is stored, user enters their Kimai API token; otherwise the stored token is reused.
3. React app calls Kimai to **validate** the token and may fetch basic reference data.
4. User lands on the **current month** view (or selects a different month).
5. React app calls Kimai to **fetch all timesheet entries** for that month and user.
6. App computes and renders a **per-day month view** from:
  - Month dates.
  - Existing entries, marking days as submitted or unfilled.
  - Month template defaults for unfilled days.
7. User fills descriptions and tweaks fields on unfilled days; draft entries are held in client state.
8. User opens the review modal, sees a table of new day entries, and confirms; app constructs a list of create operations and sends them to Kimai.
9. Upon success, app refreshes data from Kimai so those days now appear as submitted.

## Tech Stack Choices

### Frontend
- **Framework**: React
  - Good support for interactive, form-heavy UIs and modals.
  - Large ecosystem and familiarity.
- **Language**: JavaScript (ES6+)
  - Keeps onboarding simple.
  - Option to migrate to TypeScript later if needed.
- **Build Tool**: Vite (or similar modern bundler)
  - Fast dev server and build times.
  - Simple configuration for a small SPA.
- **Styling**:
  - Lightweight CSS solution (CSS modules or a minimal CSS framework).
  - Focus on clean, clear grid and form UIs over heavy theming.
- **State Management**:
  - React hooks (`useState`, `useReducer`, `useEffect`) for local component state.
  - Optional usage of a small library (e.g., React Query) for API call caching and loading/error states if complexity grows.

### Storage (Client-Side Only)
- **Browser storage** (e.g., `localStorage`):
  - To store the user’s Kimai API token (if the user opts in).
  - To store small UI preferences (last selected month, view options).
- No separate database or backend persistence for this app.

### Backend / Infrastructure
- **None specific to this project**:
  - No custom API server.
  - No database.
- **Existing Kimai instance**:
  - Hosted wherever the company already runs Kimai.
  - Exposes its standard HTTP APIs.

### Deployment
- The React app builds to static files and can be deployed to:
  - GitHub Pages.
  - A simple internal web server (e.g., Nginx serving static files).
  - Any static hosting service (Netlify, Vercel, S3 + CloudFront, etc.), subject to company constraints.

## Architectural Alternatives (Considered but Not Chosen for V1)

1. **Frontend + Custom Backend Proxy**
   - Pros:
     - Central management of tokens and configuration.
     - Easier enforcement of rate limiting and audit logging.
   - Cons:
     - Requires building, hosting, and securing a new backend service.
     - More operational overhead for a small helper tool.
   - Decision: **Not chosen** for V1 to keep development and operations simple.

2. **Browser Extension Instead of Web App**
   - Pros:
     - Could integrate directly into the existing Kimai UI.
   - Cons:
     - More complex distribution and update story (browser stores, policies).
     - Tied to a specific browser environment.
   - Decision: **Not chosen** for V1; standalone web app is easier to share.

## Summary
The chosen architecture is a **lightweight React SPA** that:
- Runs entirely in the browser.
- Talks directly to the existing Kimai APIs using the user’s token.
- Stores only minimal configuration and preferences locally.

This keeps the project small and maintainable while still providing a powerful bulk-view-and-submit experience on top of Kimai.
