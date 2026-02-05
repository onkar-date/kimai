# Deployment, Operations & Roadmap – Kimai Bulk Submit Helper

## Deployment

### Build Artifacts
- The React app builds into a static bundle:
  - `index.html`.
  - Compiled JavaScript bundle(s).
  - CSS and static assets.

### Hosting Options
- Any static hosting solution is suitable, for example:
  - GitHub Pages.
  - Internal company web server (e.g., Nginx serving static files).
  - Static hosting platforms (Netlify, Vercel, S3 + CloudFront), subject to company policies.

### Runtime Configuration
- For flexibility across environments, use either:
  - Build-time environment variables for defaults (e.g., default Kimai base URL).
  - Or a small runtime config file (JSON) fetched on load.
- Each user still enters their **personal token** at runtime.

### Network Requirements
- The hosted app must be able to reach the Kimai server over HTTPS.
- Depending on company network architecture:
  - The app may be hosted inside the corporate network.
  - Or Kimai may be exposed securely over VPN / reverse proxy.

## Operations

### Monitoring & Logging
- There is no backend logging for this project.
- Client-side monitoring is optional but can include:
  - Basic error reporting (e.g., console logs during development).
  - Optional integration with a client-side error tracker (if allowed).

### Upgrades & Releases
- Typical release flow:
  1. Make changes in a Git branch.
  2. Run local/test builds.
  3. Build production bundle.
  4. Deploy static files to hosting location.
- Users automatically get the latest version when they reload the page.

### Token & Configuration Management
- Tokens are managed purely by end users:
  - The app never sends tokens to any server other than Kimai.
  - Users can clear local storage to remove saved configuration.
- If company policy is strict about tokens:
  - You can choose to **not persist tokens** and force re-entry per session.

## Roadmap

### Phase 1 – Core MVP (Bulk Entry & Submit)
- Implement configuration and validation screen.
- Implement month view:
  - Period selector for month.
  - Fetch timesheets for selected month.
  - Basic grid (rows = project/activity, columns = days).
  - Show existing entries and empty cells.
- Allow entering hours in empty cells.
- Build and execute **create-only** submission plan:
  - Submit new entries to Kimai.
  - Show basic success/failure feedback.
- Basic completeness indication per day (e.g., total hours vs. target).

### Phase 2 – Usability & Week View
- Add week view and easy navigation (previous/next period buttons).
- Improve grid usability:
  - Keyboard navigation between cells.
  - Better styling and responsive layout.
- Enhance completeness indicators (e.g., color coding).
- Optional: simple editing of existing entries (update flow).

### Phase 3 – Polish & Advanced Features
- Advanced UX improvements:
  - Copy/paste patterns (e.g., copy hours across a week).
  - Quick templates for common projects/activities.
- More robust error handling and messaging.
- Optional lightweight analytics:
  - Summary of hours per project for a period.
  - Export to CSV for personal records.
- Optional: explore a minimal backend proxy if adoption grows and centralized control is desired.

## Risks, Trade-offs & Open Questions

### Risks
- **Kimai API changes**: If Kimai’s APIs change, the app must be updated accordingly.
- **Token handling**: Storing tokens in browser storage may be sensitive; must align with company security policies.
- **Grid complexity**: Very large numbers of projects/activities could make the grid unwieldy.

### Trade-offs
- **No backend vs. control**:
  - Chosen: no backend for simplicity and low maintenance.
  - Trade-off: less central control over tokens and logging.
- **Focus on month/week views**:
  - Chosen for highest impact on bulk workflows.
  - Trade-off: no custom date ranges or advanced reporting in early phases.

### Open Questions
- What is the **exact rule** for considering a day "complete" (fixed hours per day, or flexible)?
- Are there any **company-specific constraints** on where this tool can be hosted?
- Should the app support **multiple Kimai instances** per user (e.g., different environments)?

These can be refined as you start implementing and dogfooding the MVP within your small user group.
