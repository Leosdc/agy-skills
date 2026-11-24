# <Project Name> — Operational Guidelines for Antigravity Agents

> Copy this template to your repository root as `AGENTS.md` and customize the placeholders below.

## Team & Ownership
- **Project Lead / Release Approver:** `<Lead Name>` (`@lead-handle`)
- **Engineers / Reviewers:** `<Dev 2>` (`@dev2`), `<Dev 3>` (`@dev3`) *(or solo developer)*

## Language
- Responses, specifications, and documentation language: **English** *(or change to Português do Brasil / your team's language)*.
- Code identifiers and Conventional Commits remain in standard English technical conventions.

## Tech Stack & Validation Commands
- **Stack:** `<e.g., TypeScript, React, Node.js, PostgreSQL>`
- **Install:** `<e.g., npm ci>`
- **Dev Server:** `<e.g., npm run dev>`
- **Tests:** `<e.g., npm test>`
- **Lint / Typecheck:** `<e.g., npm run lint && npm run typecheck>`
- **Build:** `<e.g., npm run build>`

## Branch Strategy & Approval Governance
- **`dev`**: Continuous integration branch (always the most up-to-date). *(Change to `main` if using a single-branch GitHub Flow.)*
- **`main`**: Protected stable production branch.
- **Release Approver**: Only `@lead-handle` approves and merges releases from `dev` into `main`.
- **Ephemeral Branches**: `feat/<name>`, `fix/<name>`, `refactor/<name>` branch from `origin/dev` and are deleted on merge.

## Spec-Driven Development Pipeline (`agy-skills`)
`1. /scaffold` ➔ `2. /craft` ➔ `3. /shield` ➔ `4. /checkpoint` ➔ `5. /dispatch` ➔ `6. Merge to dev` ➔ `7. /dispatch sync`

- All specifications live in `specs/<name>.SPEC.md`.
- All modules/services are cataloged in `docs/REGISTRY.json`.
- Active developer session lives in `.session.json` (gitignored).

## UI / Design System Rules *(optional — remove for non-UI projects)*
- **Official Themes:** `<e.g., light, dark>` using CSS variables in `<path/to/themes.css>`.
- **Canonical Components:** Reuse shared components from `<path/to/components>` — avoid hardcoded dark/light colors or duplicate ad-hoc widgets.
