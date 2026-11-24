# Operational Guidelines — Spec-Driven Development (`agy-skills`)

These guidelines establish a deterministic, auditable, and stack-agnostic **Spec-Driven Development** workflow for solo developers and engineering teams using **Google Antigravity**.

> **Project Overrides:** If the target repository has its own root `AGENTS.md` defining custom branch names, reviewers, test commands, or UI/design standards, those project-specific declarations take precedence over the defaults below.

---

## Language & Tone
- Mirror the language configured in the project's root `AGENTS.md`, or reply in the user's active conversation language (e.g., English, Português do Brasil).
- Code identifiers, technical terms, and Conventional Commit prefixes (`feat`, `fix`, `docs`, `refactor`, `chore`, `test`) follow standard engineering conventions.

---

## Branch Strategy & Approval Governance (Configurable Defaults)
- **Integration Branch (`dev`)**: Continuous integration and team testing branch. Always the most up-to-date branch.
- **Production Branch (`main`)**: Protected, stable production branch. Direct pushes are forbidden.
- **Release Approver**: Only the designated Project Lead / Release Owner (defined in the project's `AGENTS.md`) approves and merges releases from `dev` into `main`.
- **Ephemeral Working Branches**: `feat/<name>`, `fix/<name>`, `refactor/<name>`, `chore/<name>` branch off `origin/dev` and are deleted after merge.
- *(Single-branch repositories using only `main` may override the integration branch to `main` in their root `AGENTS.md`.)*

---

## The 8 Antigravity Workflow Skills
1. `/ignite`: Prepares the local environment and validates toolchains, dependencies, `.env`, and CLI auth idempotently.
2. `/scaffold <type>/<name>`: Creates a new module/feature/fix with canonical `specs/<name>.SPEC.md`, registry entry, and ephemeral branch.
3. `/focus [module]`: Sets or inspects the active work context in local `.session.json`.
4. `/craft [context]`: Interactive senior-engineer coding loop on the active module with automated validation.
5. `/shield`: Passive and on-demand security, input sanitization, authz, and privacy (LGPD/GDPR) guardrail.
6. `/checkpoint`: Rebases on `origin/dev`, runs secret scanning, creates a Markdown checkpoint plan, logs a GitHub Issue journal, commits atomically, and pushes.
7. `/dispatch [dev | main | sync]`: Opens Pull Requests to `dev` (features/fixes) or `main` (releases), keeps `README.md` and `CHANGELOG.md` synchronized, and closes resolved issues + cleans session on `/dispatch sync`.
8. `/matrix [sync | schema <target>]`: Queries and validates the canonical project inventory (`docs/REGISTRY.json`).

---

## Canonical Governance Sequence & Anti-Skip Interactive Prompts

Development follows a deterministic pipeline:

```text
1. /scaffold ➔ 2. /craft ➔ 3. /shield ➔ 4. /checkpoint ➔ 5. /dispatch ➔ 6. Merge to dev ➔ 7. /dispatch sync
```

### Mandatory Orchestration Rules
1. **Interactive Next-Step Prompts (`ask_question`)**:
   - Every completed pipeline stage MUST present an interactive prompt to the developer, with the first option being `(Recommended) <next_canonical_step>`.
2. **Anti-Skip Guardrails**:
   - If the developer invokes a stage out of order (e.g., `/checkpoint` without `/shield` on security-sensitive files, `/dispatch` with uncommitted changes, or `/craft` without an active module in `.session.json`), the agent MUST NOT execute blindly or fail harshly. It must pause and present an `ask_question` recommending the prerequisite step.
3. **Full Issue Lifecycle (Open & Close)**:
   - Because GitHub does not automatically close issues referenced with `Closes #N` when merging into non-default branches such as `dev`, every issue created by `/checkpoint` MUST be closed by `/dispatch sync` via `gh issue close <id>`. Never leave zombie open issues.
4. **Session Hygiene**:
   - Upon `/dispatch sync`, reset `activeModule` to `null` in `.session.json` to keep the workspace clean for the next task.

---

## Conventional Commits
Use strict Conventional Commits with scope:
- `feat(auth): add OAuth2 PKCE token refresh`
- `fix(api): prevent race condition on concurrent webhook retries`
- `refactor(ui): decouple modal state from router store`
- `docs(specs): add SPEC-001 for billing webhooks`

---

## Code Architecture & Quality Standards
- **Single Responsibility (SRP)**: Keep functions focused and concise (~40 lines heuristic).
- **Explicit Error Handling**: Never swallow errors with empty `catch` blocks; return structured, fail-safe fallbacks.
- **Types & Contracts**: Document public APIs and critical data structures with TypeScript types, JSDoc, or language-idiomatic type hints.
- **Design System & Theme Compliance**: Whenever the project defines official UI components, design tokens, or themes (light/dark/custom), inspect and reuse them strictly — never introduce hardcoded colors or ad-hoc widgets that break contrast or visual consistency.
- **Automated Verification**: Run the project's test suite, linter, typecheck, and build before closing `/craft`.

---

## Non-Negotiable Security & Privacy Laws
1. **Secrets Hygiene**: Never hardcode API keys, tokens, passwords, or private keys in committed files. Keep `.env*` and `.session.json` in `.gitignore`.
2. **Injection & XSS Prevention**: Never render untrusted user/AI input via raw HTML sinks (`innerHTML`, `dangerouslySetInnerHTML`, unescaped templates) without strict sanitization (`DOMPurify` / framework escaping) or `.textContent`. Use parameterized queries for databases.
3. **Access Control (AuthN / AuthZ)**: Enforce authentication and resource ownership / RBAC checks server-side (or via database security rules) on every protected mutation and read.
4. **Data Minimization (LGPD / GDPR)**: Collect and expose only the minimum data strictly required; never leak internal UIDs, secrets, or PII in client logs, UI dumps, or telemetry.
