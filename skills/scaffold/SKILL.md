---
name: scaffold
description: >-
  Creates the full Spec-Driven Development scaffolding for a new module,
  feature, service, or bug fix in any project. Generates a canonical
  specification in specs/<name>.SPEC.md, updates docs/REGISTRY.json, creates an
  ephemeral branch off origin/dev (or configured base), and focuses .session.json.
argument-hint: <type>/<name> [briefing]
---

# /scaffold <type>/<name> [briefing]

Bootstraps a new feature, module, service, component, or bug fix using **Spec-Driven Development** with Plan-Validate-Execute governance.

Accepted `<type>` values (adapt to the target repository's layout defined in `AGENTS.md` or `docs/REGISTRY.json`):
- `feat` / `module` / `feature`: New application module or user-facing feature (branch `feat/<name>`)
- `fix`: Bug fix or hardening task (branch `fix/<name>`)
- `service` / `backend` / `api`: New backend route, service, or worker (branch `feat/<name>`)
- `ui` / `component`: New UI view or component (branch `feat/<name>`)
- `refactor` / `chore`: Structural refactoring or tooling work (branch `refactor/<name>` or `chore/<name>`)

*(If the user omits `<type>/`, e.g., `/scaffold fix-login-timeout` or `/scaffold billing-webhooks`, infer the branch prefix cleanly as `fix/<slug>` or `feat/<slug>`.)*

---

## Mandatory Specification Location
Every engineering specification MUST be saved in the canonical directory:
👉 **`specs/<name>.SPEC.md`**

> **NEVER** scatter standalone `SPEC.md` files in the project root or isolated subfolders without placing the primary specification in `specs/`. The `specs/` directory is the unified *Source of Truth* for engineering contracts.
> **Public Repositories:** Check `gh repo view --json isPrivate,visibility` (or `repoVisibility` in `.session.json`). If the repository is **public** (or visibility is unknown), `specs/` and `docs/REGISTRY.json` MUST be added to `.gitignore` and kept **strictly local** (never committed to remote Git, unless explicitly opted in via the project's root `AGENTS.md`).

---

## Plan-Validate-Execute Procedure

### 1. Plan (Read-Only)
1. Parse `<type>`, `<name>`, and any optional `[briefing]` from the user's request.
2. Determine the project's base integration branch from root `AGENTS.md` (default: `origin/dev`; fallback to `origin/main` if the repo has no `dev` branch).
3. **Anti-Skip Pre-Flight (Open Session Check):**
   - Inspect `.session.json`. If `activeModule` is non-null (an unfinished cycle exists), pause and present `ask_question`:
     - `(Recommended) Run /dispatch sync to synchronize and close the previous module first`
     - `Override active session and start the new /scaffold`
     - `Cancel operation`
4. Inspect `docs/REGISTRY.json` (if present) to verify `<name>` does not already exist and determine the next sequential `SPEC-XXX` number from `specs/*.SPEC.md`.
5. Inspect Git state (`git fetch origin`, `git status --short`).
6. Draft the concise scaffolding plan listing:
   - **Canonical Spec:** `specs/<name>.SPEC.md` (`SPEC-XXX`)
   - **Planned Implementation Targets:** files/folders to create or modify during `/craft`
   - **Catalog Update:** entry in `docs/REGISTRY.json` linked via `"spec": "specs/<name>.SPEC.md"` (initializing `docs/REGISTRY.json` from a minimal schema if the repo does not have one yet)
   - **Ephemeral Branch:** `<prefix>/<name>` created from `<base-branch>`
   - **Session Focus:** automatic `/focus <name>` in `.session.json`

### 2. Validate
Present the plan clearly and request explicit confirmation via `ask_question` before modifying files or branches:
- `(Recommended) Confirm and execute /scaffold <name>`
- `Adjust scope, spec name, or branch name first`
- `Cancel operation`

### 3. Execute
1. **Create and checkout the ephemeral branch:**
   ```bash
   git checkout -b <prefix>/<name> <base-branch>
   ```
2. **Create `specs/<name>.SPEC.md`** containing:
   - Formal metadata (Status `🟡 in-progress`, Type, Affected Files/Modules, Date, Author).
   - `## 1. Executive Summary & Context`
   - `## 2. Functional & Non-Functional Requirements`
   - `## 3. Solution Architecture & Contracts` (APIs, schemas, events, data flow / Mermaid diagram)
   - `## 4. Security, Sanitization & Privacy (LGPD / GDPR)`
   - `## 5. Acceptance Criteria & Test Plan`
3. **Create initial boilerplate** (only if creating a brand-new module/file, with fail-safe error handling and clean types; skip boilerplate creation for targeted bug fixes on existing files).
4. **Register the new entry in `docs/REGISTRY.json`** with `"status": "🟡 in-progress"` and `"spec": "specs/<name>.SPEC.md"`.
5. **Chain `/focus <name>` automatically** by updating `.session.json`.
6. **Interactive Next-Step Prompt (`ask_question`):**
   - `(Recommended) Proceed to /craft to begin implementing the specification`
   - `Review and refine the generated specification in specs/`
   - `Pause for now`
