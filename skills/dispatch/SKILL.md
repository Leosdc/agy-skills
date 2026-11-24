---
name: dispatch
description: >-
  Opens or updates Pull Requests to dev (features/fixes) or main (stable
  releases), synchronizes README.md PR timelines and CHANGELOG.md, and runs
  post-merge synchronization (/dispatch sync) that pulls dev, closes resolved
  GitHub issues, updates PR status badges, and resets .session.json.
argument-hint: "[dev | main | sync]"
---

# /dispatch [target | sync]

Manages Pull Request delivery, release promotion, documentation synchronization, automatic issue closure, and post-merge workspace hygiene.

---

## Deterministic Mode Selection Algorithm

`/dispatch` selects its mode strictly from the current Git branch and explicit arguments (configurable via root `AGENTS.md` if the repository uses custom branch names):

1. **Ephemeral branch (`feat/*`, `fix/*`, `refactor/*`, `chore/*`)** ➔ **Mode 1: Feature / Fix ➔ `dev`** *(or `main` in single-branch repos)*
2. **Integration branch (`dev`) without `sync`** ➔ **Mode 2: Production Release ➔ `main` (ALWAYS)**
3. **Explicit `/dispatch sync`** ➔ **Mode 3: Post-Merge Sync, Issue Closure & Session Cleanup**

> **INVARIANT RULE:** When invoked as `/dispatch` on `dev`, NEVER silently divert to Mode 3. Always enter Mode 2 (Release to `main`) and fold any pending README PR status sync into the release commit.

---

## Mode 1: Feature / Fix ➔ `dev`

1. **Anti-Skip Pre-Flight (Read-Only):**
   - Confirm current branch is ephemeral (not `dev` or `main`).
   - If working tree is dirty (`git status --short` non-empty), pause and present `ask_question`:
     - `(Recommended) Run /checkpoint first to audit and create atomic commits`
     - `Cancel PR operation`
   - If branch has no commits ahead of `origin/dev`, pause and present `ask_question`:
     - `(Recommended) Implement changes with /craft and save with /checkpoint first`
     - `Cancel PR operation`
   - Check for an existing open PR: `gh pr list --head <branch> --state open`.

2. **README PR Table Sync Check:**
   - If `README.md` tracks recent PRs and any row still says `🔵 In Review` / `🔵 Em Revisão`, verify its real state (`gh pr view <num> --json state -q .state`) and plan to update merged rows to `🟣 Merged`.

3. **Mandatory Confirmation (`ask_question`):**
   Present the PR title, target branch (`dev`), linked issue (`Closes #N`), and planned documentation updates (`CHANGELOG.md`, `README.md`), and wait for explicit approval before editing or opening the PR.

4. **Documentation Synchronization (when files exist in repo):**
   - Add the entry under `## [Unreleased / Dev]` in `CHANGELOG.md` (and any mirrored changelog path configured in `AGENTS.md`).
   - If `README.md` maintains a recent PR table / Mermaid timeline, insert the new PR at the top of the table (`🔵 In Review`) and at the end of the timeline, maintaining a sliding window of **at most 10 recent PRs**.
   - Commit and push docs on the working branch:
     ```bash
     git add README.md CHANGELOG.md
     git commit -m "docs: register <module> PR in README and CHANGELOG"
     git push origin HEAD
     ```

5. **Open Pull Request:**
   - Fill `.github/PULL_REQUEST_TEMPLATE.md` (if present) including `Closes #<issue>` and reviewers from `AGENTS.md`.
   - Write the PR body to a temporary UTF-8 (no BOM) file and execute:
     ```bash
     gh pr create --base dev --title "<type>(<scope>): <summary>" --body-file <tempFile>
     ```
   - Return the created PR URL.

6. **Interactive Post-PR Prompt (`ask_question`):**
   - `(Recommended) Wait for review/merge and run /dispatch sync`
   - `Switch back to dev to start another module`
   - `End session for now`

---

## Mode 2: Production Release ➔ `main` (From `dev`)

1. **Pre-Flight:**
   - Confirm current branch is `dev` and synced with `origin/dev`.
   - Ensure a Release tracking issue exists on GitHub (or plan to create it).

2. **Mandatory Release Lead Confirmation (`ask_question`):**
   - Present proposed SemVer (`vX.Y.Z`), consolidated changelog highlights, and target `dev` ➔ `main`.

3. **Release Preparation Commit on `dev`:**
   - Consolidate `## [Unreleased / Dev]` into `## [vX.Y.Z] - YYYY-MM-DD` in `CHANGELOG.md`.
   - Bump project version in the package manifest (`package.json`, `pyproject.toml`, `Cargo.toml`, etc., if applicable) and mark completed modules in `docs/REGISTRY.json` as `🟢 prod-active`.
   - Update `README.md` release badges/cards/timeline (preserving the 10-PR sliding window).
   - Commit (`chore(release): prepare vX.Y.Z in README, CHANGELOG, and manifest`) and push to `origin/dev`.

4. **Open Release PR (`dev` ➔ `main`):**
   - Open via `gh pr create --base main --head dev --title "release(vX.Y.Z): promote dev to production (main)" --body-file <tempFile>` with `Closes #<release-issue>`.
   - **NEVER** merge directly into `main`; only the designated Release Lead approves and merges.

---

## Mode 3: Post-Merge Sync & Issue Closure (`/dispatch sync`)

1. **Sync Integration Branch & Prune Local Ephemeral Branch:**
   - Checkout `dev` (`git checkout dev`) and pull (`git pull origin dev`).
   - Delete the merged local ephemeral branch if safe (`git branch -d <branch>`).

2. **Automatic Closure of Resolved GitHub Issues:**
   - Because GitHub does not auto-close `Closes #N` issues when merging into `dev`:
     - Inspect recent merged PRs (`gh pr list --state merged --limit 5 --json number,body,title`).
     - Extract referenced issue IDs (`Closes #N` / `Fixes #N`).
     - For each issue still `OPEN` (`gh issue view <id> --json state -q .state`), close it via:
       ```bash
       gh issue close <id> --comment "Closed automatically via /dispatch sync after PR merge to dev."
       ```

3. **README Status Sync:**
   - Replace any `🔵 In Review` / `🔵 Em Revisão` rows corresponding to merged PRs in `README.md` with `🟣 Merged` and ensure the `dev` branch badge is accurate.

4. **Session Hygiene (`.session.json`):**
   - Reset `"activeModule": null` in `.session.json`.

5. **Commit & Push Sync (if `README.md` changed):**
   ```bash
   git add README.md
   git commit -m "docs: sync merged PR statuses and close resolved issues"
   git push origin dev
   ```

6. **Closing Prompt (`ask_question`):**
   - `(Recommended) Start a new development cycle with /scaffold`
   - `Inspect project inventory with /matrix`
   - `Pause for now`

---

## Golden Rule: Safe Shell Encoding (PowerShell & Cross-Platform)
- **NEVER** pass multi-line Markdown inline in double quotes to `gh pr create --body "..."`.
- **ALWAYS** save the PR body to a temporary UTF-8 (no BOM) file and pass `--body-file <tempFile>`.
