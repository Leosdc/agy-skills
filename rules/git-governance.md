# Git Governance & GitHub Flow

1. **Two Permanent Branches (Configurable Default)**:
   - `dev`: Continuous integration branch. Always the most up-to-date branch where completed features and fixes converge.
   - `main`: Protected production branch. Direct pushes are strictly forbidden.
   - *(Projects that operate on a single permanent `main` branch may override this in their root `AGENTS.md`.)*

2. **Production Approval Hierarchy**:
   - All changes land in `dev` first via Pull Request.
   - Only the designated Project Lead / Release Owner approves and merges release PRs from `dev` into `main`.
   - The AI agent MUST NEVER push directly to or merge directly into `main`.

3. **Ephemeral Branch Lifecycle**:
   - Working branches (`feat/<name>`, `fix/<name>`, `refactor/<name>`, `chore/<name>`) always branch from `origin/dev` and are deleted after merge.
   - Always rebase against `origin/dev` before opening or updating a PR to maintain a clean, linear history.

4. **Conventional Commits**:
   - Strict format: `<type>(<scope>): <concise description>`.
   - Examples: `feat(billing): add webhook signature verification`, `fix(ui): prevent modal scroll lock leak`.

5. **Safe Shell Encoding for GitHub CLI (`gh`)**:
   - Never pass multi-line Markdown with backticks inline inside double-quoted shell strings (especially in PowerShell, where `` ` `` is the escape character).
   - Always write issue bodies, comments, and PR descriptions to a temporary UTF-8 (no BOM) file and pass `--body-file <path>`.
