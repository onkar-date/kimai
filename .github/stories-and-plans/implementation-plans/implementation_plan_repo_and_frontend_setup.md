# Kimai Helper Repo & Frontend Scaffold Implementation Plan

## Overview
Set up the Kimai Bulk Submit Helper project repository with a clean structure, root configs, and an empty but working Vite+React (JavaScript) SPA under `frontend/`, aligned with the existing system-design docs.

## Architecture
Root repo hosts shared configuration and documentation. A `frontend/` folder contains a Vite-powered React SPA (JavaScript, ES6+) that currently renders a minimal placeholder view but is wired with npm scripts, ESLint, and Prettier so it can be extended into the full helper app later.

## Implementation Phases

### Phase 1: Repository Layout & Root-Level Configuration
**Files**: `README.md`, `.gitignore`, `.editorconfig`, `.github/stories-and-plans/system-designs/*`
**Test Files**: _None for this phase_

Set up the base repository structure so it cleanly separates docs, future code, and root configuration, and ensure the current system-design docs are discoverable from the root.

- [ ] Confirm/maintain `.github/` structure as per existing system-design and guidelines
- [ ] Add or update root `.gitignore` to cover Node/Vite artifacts and macOS/editor files
- [ ] Add a root `.editorconfig` to enforce consistent whitespace, line endings, and indentation
- [ ] Update root `README.md` to describe high-level structure (docs + upcoming `frontend/` SPA) and link to system-design docs

**Key code changes:**
```markdown
# README.md (high-level sketch)
- Brief project description (Kimai Bulk Submit Helper)
- Sections:
  - Repo structure (/.github, /frontend)
  - How to get started with the frontend once scaffolded
  - Links to system-design docs under .github/stories-and-plans/system-designs/kimai-bulk-submit-helper/
```

Test cases for this phase:
• Verify `.gitignore` successfully ignores `node_modules/`, `dist/`, Vite cache, and OS/editor junk.  
• Open the repo in an editor and confirm `.editorconfig` rules (indent, line endings) are applied.  
• Confirm `README.md` clearly explains where the frontend app will live and how it relates to the system-design docs.

Technical details and Assumptions (if any):
• Keep `.gitignore` aligned with standard Node/Vite patterns while preserving any existing project ignores.  
• `.editorconfig` should match the core-standards guideline (spaces, typical 2-space or 4-space indent; choose one and keep consistent).  
• No code yet in `frontend/` in this phase; this is purely structural and documentation work.

---

### Phase 2: Vite + React (JavaScript) App Scaffold in `frontend/`
**Files**: `frontend/package.json`, `frontend/vite.config.js`, `frontend/index.html`, `frontend/src/main.jsx`, `frontend/src/App.jsx`, `frontend/src/assets/*`
**Test Files**: _None initially; manual run checks for now_

Create a new Vite-based React SPA in `frontend/` using JavaScript (not TypeScript), wired with npm scripts (`dev`, `build`, `preview`) and a minimal placeholder UI so `npm install` + `npm run dev` works out of the box.

- [ ] Initialize `frontend/` as an npm project with Vite + React (JavaScript) dependencies
- [ ] Configure `frontend/vite.config.js` with a simple React/Vite setup and appropriate base path defaults
- [ ] Add `frontend/index.html` that mounts the React app on a root DOM node (e.g., `<div id="root"></div>`)
- [ ] Implement `frontend/src/main.jsx` to create the React root and render `App`
- [ ] Implement a minimal `frontend/src/App.jsx` that renders an empty shell view (e.g., project name + “coming soon”) without any business logic
- [ ] Align import paths, module resolution, and dev server port with defaults (or document any deviation in the root README)

**Key code changes:**
```javascript
// frontend/src/main.jsx (conceptual sketch)
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App.jsx';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);

// frontend/src/App.jsx (minimal placeholder)
export default function App() {
  return (
    <main>
      <h1>Kimai Bulk Submit Helper</h1>
      <p>Frontend scaffold is ready. Implementation coming soon.</p>
    </main>
  );
}
```

Test cases for this phase:
• Run `npm install` inside `frontend/` and ensure dependencies install without errors.  
• Run `npm run dev` and verify the app starts, serves on localhost, and displays the placeholder content in the browser.  
• Run `npm run build` to confirm the production build succeeds and produces a `dist/` folder.

Technical details and Assumptions (if any):
• Use JavaScript template for Vite (`react` + `javascript`) to align with system-design docs.  
• Default dev server port (usually 5173) is acceptable; if changed, document it in `README.md`.  
• No routing, state management, or Kimai API integration in this phase—only the bare shell.

---

### Phase 3: ESLint & Prettier Tooling for the Frontend
**Files**: `frontend/package.json`, `frontend/.eslintrc.cjs` (or `.eslintrc.js`), `frontend/.prettierrc` (or equivalent), `frontend/.eslintignore`, `frontend/.prettierignore`
**Test Files**: _None; rely on `npm run lint` / `npm run format:check`_

Add basic linting and formatting tooling to the `frontend/` app so future implementation follows the core-standards and React/JavaScript guidelines.

- [ ] Add ESLint as a dev dependency in `frontend/package.json` with React + JSX support
- [ ] Create `frontend/.eslintrc` (CJS or JS) configured for modern ES6+, React 18, and browser environment
- [ ] Add a `"lint"` npm script in `frontend/package.json` to run ESLint on `src/`
- [ ] Add Prettier as a dev dependency and create `frontend/.prettierrc` with basic formatting rules (aligned with `.editorconfig`)
- [ ] Add `"format"` and `"format:check"` npm scripts in `frontend/package.json`
- [ ] Configure `.eslintignore` and `.prettierignore` to skip `dist/`, `node_modules/`, and other generated artifacts

**Key code changes:**
```json
// frontend/package.json (scripts section sketch)
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint src --ext .js,.jsx",
    "format": "prettier --write \"src/**/*.{js,jsx,css,md}\"",
    "format:check": "prettier --check \"src/**/*.{js,jsx,css,md}\""
  }
}

// frontend/.eslintrc.cjs (conceptual)
module.exports = {
  env: { browser: true, es2021: true },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
  ],
  parserOptions: { ecmaVersion: 'latest', sourceType: 'module' },
  settings: { react: { version: 'detect' } },
  rules: {
    // Keep rules minimal and aligned with core-standards
  },
};
```

Test cases for this phase:
• Run `npm run lint` in `frontend/` and confirm it passes on the scaffolded code.  
• Introduce a deliberate lint violation locally (e.g., unused variable) and confirm `npm run lint` fails as expected, then fix it.  
• Run `npm run format:check` and `npm run format` to verify Prettier formats files and passes checks.

Technical details and Assumptions (if any):
• Keep ESLint/Prettier configs simple and opinionated but easy to adjust as the project evolves.  
• Ensure ESLint and Prettier configurations do not conflict (e.g., by using `eslint-config-prettier` if needed later).  
• Limit configuration scope to `frontend/` for now; root-level tooling can be added later if/when backend appears.

---

## Technical Considerations
• Dependencies: Vite, React, ReactDOM as runtime dependencies; ESLint, Prettier, and related plugins as dev dependencies in `frontend/`.  
• Edge Cases: Developers using different Node versions—document a recommended Node LTS in `README.md` to reduce surprises.  
• Testing Strategy: Initially rely on manual verification (`npm run dev`, `npm run build`, `npm run lint`); add Jest/Vitest and React Testing Library in a later implementation plan once features exist.  
• Performance: Vite defaults are sufficient for this scaffold; performance tuning comes later with real features.  
• Security: No network calls or token handling yet; Kimai API integration and token storage concerns are deferred to subsequent plans.

## Testing Notes
• Each phase includes its own validation steps (manual commands and checks) rather than automated tests at this stage.  
• When test tooling is introduced in a later phase/plan, mirror standard patterns (React Testing Library + Jest/Vitest) and ensure basic smoke tests for `App` and entrypoint.  
• Keep `npm run lint` and `npm run build` green as a precondition for any future CI setup.

## Success Criteria
- [ ] Root repo clearly documents structure and links to system-design docs.  
- [ ] `frontend/` Vite+React app installs, runs (`npm run dev`), and builds (`npm run build`) successfully with only a minimal placeholder UI.  
- [ ] ESLint and Prettier are configured in `frontend/` with working `lint` and `format` scripts, and the scaffolded code passes both.
