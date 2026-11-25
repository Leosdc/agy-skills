---
name: checkpoint
description: >-
  Safely closes a work cycle on an ephemeral branch. Performs anti-secret
  pre-flight, rebases on origin/dev, generates a formal Markdown checkpoint plan
  in specs/checkpoints/, logs a technical journal comment on a GitHub Issue,
  creates atomic Conventional Commits, pushes to origin, and guides to /dispatch.
---

# /checkpoint

Safely packages, audits, documents, and pushes progress on the active module following **Plan-Validate-Execute**.

---

## Procedure

### 1. Plan (Read-Only & Markdown Plan Generation)
1. Read `.session.json` to identify `activeModule` and `developer`.
2. Inspect Git state (`git status --short`, `git diff --stat`, current branch).
3. **Anti-Skip Security & Visibility Pre-Flight:**
   - Scan staged/unstaged/untracked files for accidental secrets (`API_KEY`, private keys, tokens, passwords). If any secret is found, **ABORT IMMEDIATELY**.
   - Check repository visibility (`gh repo view --json isPrivate,visibility` or `.session.json`). If **public** (or unknown, unless opted in via root `AGENTS.md`), confirm `specs/`, `specs/checkpoints/`, and `docs/REGISTRY.json` are in `.gitignore` and **EXCLUDE** them from atomic commits (keeping `.PLAN.md` strictly local).
   - If security-sensitive files (auth, server/API, CSP/headers, database rules) were modified and `/shield` has not yet been executed in this session, pause and present `ask_question`:
     - `(Recommended) Run /shield first for a strict security and compliance audit`
     - `Proceed directly with the /checkpoint plan`
     - `Cancel checkpoint`
4. Inspect `README.md` (if it maintains a PR status table) and check whether any PR listed as `🔵 In Review` / `🔵 Em Revisão` has already been merged on GitHub (`gh pr view`), proposing synchronization to `🟣 Merged`.
5. Check GitHub for an open issue with label `module:<activeModule>` (or `modulo:<activeModule>`). If none exists, plan to create the label and open the tracking issue.
6. **Generate Mandatory Markdown Plan:**
   - Create `specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md` containing:
     - **Metadata:** Date, Developer, Active Module (`SPEC-XXX`), Current Branch.
     - **Changed & New Files:** Table listing every modified/untracked file and its scope.
     - **Anti-Secrets & `/shield` Audit:** Explicit confirmation of `0` leaks.
     - **README / PR Sync Status:** Verification summary.
     - **GitHub Issue & Journal:** Issue number (or planned creation) and exact technical journal comment body.
     - **Proposed Atomic Commits:** Conventional Commit messages with exact file groupings.
     - **Git Operations:** Exact rebase, commit, and push sequence.
7. Share a clickable link to `specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md`.

### 2. Validate
Request explicit developer approval via `ask_question`:
- `(Recommended) Approve and execute specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md`
- `Modify the plan before proceeding`
- `Cancel checkpoint`

### 3. Execute
1. Rebase cleanly onto the integration branch (default `origin/dev`, or configured base in `AGENTS.md`):
   ```bash
   git fetch origin
   git rebase --autostash origin/dev
   ```
2. Create the GitHub Issue (if not yet open) and post the technical journal comment via `gh issue comment` using `--body-file` (UTF-8 without BOM).
3. Create the approved atomic Conventional Commits (including `specs/checkpoints/YYYYMMDD_<activeModule>.PLAN.md` in the docs/spec slice **only in private repositories**; in public repositories, keep `specs/` and `docs/REGISTRY.json` strictly local and gitignored).
4. Push the branch:
   ```bash
   git push -u origin HEAD
   ```
5. **Mandatory Next-Step Prompt (`ask_question`):**
   - `(Recommended) Proceed to /dispatch to open the Pull Request to dev`
   - `Continue developing on this module before opening a PR`
   - `Pause the session for now`

---

## Golden Rule: Safe Shell Encoding (PowerShell & Cross-Platform)
In PowerShell, backtick (`` ` ``) is the escape character! Passing inline Markdown with backticks inside double-quoted strings corrupts sequences like `` `v ``, `` `f ``, `` `n ``, and `` `t `` on GitHub.

**Mandatory Protocol:**
- **NEVER** pass multi-line Markdown inline in double quotes to `gh`.
- **ALWAYS** write issue bodies and comments to a temporary UTF-8 (no BOM) file and pass `--body-file <path>`.
- In PowerShell scripts, use single-quoted here-strings (`@' ... '@`) when building Markdown literals.
