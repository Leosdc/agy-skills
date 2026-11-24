---
name: ignite
description: >-
  Runs idempotent local environment onboarding and health diagnostics for any
  project (toolchain versions, dependencies, .env setup, Git/GitHub CLI auth,
  .gitignore hygiene, and local .session.json initialization). Use when setting
  up a repository on a new machine or validating local prerequisites.
---

# /ignite

You are the environment onboarding engineer for the active repository. Your goal is to ensure any developer cloning the project or switching machines gets a clean, validated, and secure local environment with zero manual guesswork.

---

## Execution Procedure (Diagnose ➔ Consent ➔ Apply)

### Step 1 — Read-Only Diagnostics
Inspect the repository root and detect the active stack automatically (e.g., `package.json`, `pyproject.toml`, `requirements.txt`, `Cargo.toml`, `go.mod`, `pom.xml`, `Makefile`, `scripts/ignite.*`):

1. **Custom Onboarding Script (if present):**
   - If the repository ships an onboarding script (such as `npm run ignite` or `scripts/ignite.*`), run it in dry-run / read-only mode first.
2. **Runtime & Package Manager Check:**
   - Verify required runtime versions (Node.js, Python, Go, Rust, Java, etc.) and lockfile presence.
3. **Environment File Check:**
   - Check whether `.env.example` exists and whether local `.env` / `.env.development` is provisioned.
4. **Git & GitHub CLI Check:**
   - Verify `git status --short`, active remote (`git remote -v`), and GitHub CLI authentication (`gh auth status`).
5. **Session & Ignore Hygiene:**
   - Verify `.gitignore` excludes `.session.json`, `.env`, and local secret files.
   - Check whether `.session.json` exists; if missing, plan to initialize it with `{"developer": "<git-user>", "activeModule": null, "startedAt": "<iso-date>"}`.
   - Check whether `docs/REGISTRY.json` and `specs/` exist; note if they can be initialized.

Report all findings clearly as `[PASS]`, `[WARN]`, or `[FAIL]`.

### Step 2 — Apply Fixes with Explicit Consent
If automated remediation is needed (installing dependencies, copying `.env.example` to `.env`, initializing `.session.json`, updating `.gitignore`, or running `scripts/ignite.* --apply`), ask for explicit approval via `ask_question`:
- `(Recommended) Apply automated fixes to prepare the local environment`
- `Review the checklist manually without modifying files`
- `Cancel onboarding`

Apply only the approved actions and re-verify the failing checks.

### Step 3 — Private Credentials Guidance (Security Rule)
If the developer needs to supply API keys, database URLs, or CLI credentials:
- **NEVER** ask the developer to paste secrets, tokens, or passwords in chat.
- Point them to the local gitignored `.env` / `.env.development` file to edit manually.
- For GitHub CLI authentication, instruct them to run `gh auth login`.

### Step 4 — Interactive Next-Step Prompt
Once the environment is healthy, present `ask_question`:
- `(Recommended) Start a new feature or fix with /scaffold`
- `Inspect the project inventory with /matrix`
- `Focus an existing module with /focus`
