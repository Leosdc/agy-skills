---
name: craft
description: >-
  Interactive senior-engineer implementation mode focused on the active module
  in .session.json. Follows the module's canonical SPEC.md, enforces Clean Code,
  design system/theme compliance, automated tests/build validation, and guides
  the next pipeline step via interactive prompts.
argument-hint: "[context or task instructions]"
---

# /craft [context]

Operates as a senior software engineer implementing or fixing the active module recorded in `.session.json`.

---

## Operating Rules

### 1. Anti-Skip Pre-Flight Check
- Read `.session.json`. If `activeModule` is `null` or `.session.json` does not exist, **DO NOT code blindly**. Present `ask_question`:
  - `(Recommended) Run /scaffold to create a canonical specification and ephemeral branch first`
  - `Run /focus to select an existing registered module`
  - `Proceed in freeform mode without tracking a module`
- Read `specs/<activeModule>.SPEC.md` (if present) and the repository's root `AGENTS.md` to align with all contracts and project conventions.

### 2. Engineering Quality, Security & UI Standards
- **Single Responsibility (SRP):** Keep functions focused and concise (~40 lines heuristic).
- **Safe Error Handling:** Never use silent empty `catch` blocks; guard async workers, I/O, and network calls with explicit timeouts and fail-safe degradation.
- **Anti-XSS & Injection:** Never inject untrusted user, database, or AI data into raw HTML (`innerHTML`, `dangerouslySetInnerHTML`) without strict sanitization (`DOMPurify.sanitize()`) or `.textContent`. Parameterize all database queries.
- **Typed Interfaces:** Provide clear TypeScript types, JSDoc annotations, or language-native type hints.
- **Design System & Multi-Theme Compliance (for UI tasks):** Inspect the project's existing design tokens, CSS variables, themes (light/dark/custom), and canonical UI components before adding new styles or elements. Never introduce arbitrary hardcoded colors or duplicate ad-hoc widgets.

### 3. Automated Validation
Before ending a `/craft` round, verify your changes using the repository's real toolchain:
- Run unit/integration tests covering new logic and edge cases.
- Run linter / typechecker (`tsc --noEmit`, `eslint`, `ruff`, `cargo check`, `go test`, etc., as configured in the repo).
- Run the production build (`npm run build` or stack equivalent) and, when touching server/API/CSP configs, verify live HTTP responses/headers against the built output.
- Update the acceptance checklist in `specs/<activeModule>.SPEC.md` to reflect verified criteria.
- Keep `git status --short` clean of unrelated build noise.

### 4. Interactive Post-Implementation Prompt
Upon completing the implementation and validations, summarize changes and test results concisely, then present `ask_question`:
- `(Recommended) Run /shield for security and compliance audit`
- `Proceed directly to /checkpoint to save progress with atomic commits`
- `Continue iterating with /craft on this module`
