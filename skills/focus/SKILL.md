---
name: focus
description: >-
  Sets or inspects the active work module in the developer's local gitignored
  .session.json file. Maintains clear task context isolation for solo developers
  and multi-developer teams.
argument-hint: "[module-name]"
---

# /focus [module]

Manages the developer's active work context stored in `.session.json` (gitignored).

---

## Behavior

### Without Arguments (`/focus`)
1. Read `.session.json` (if missing, report that no active session is initialized).
2. Inspect Git state (`git branch --show-current`, `git status --short`).
3. Report concisely:
   - Active module (`activeModule`) and linked specification in `specs/` (if found)
   - Current Git branch
   - Uncommitted changes or pending commits ahead of the base branch
4. Present `ask_question` if helpful (e.g., continue `/craft`, switch module, or start `/scaffold`).

### With Argument (`/focus <module>`)
1. If `docs/REGISTRY.json` exists, verify whether `<module>` is registered (if not found, warn the user and offer to register it or run `/scaffold`).
2. Update `.session.json`:
   ```json
   {
     "developer": "<git-user-or-existing>",
     "activeModule": "<module>",
     "updatedAt": "<iso-8601-timestamp>"
   }
   ```
3. Confirm the context switch, surface the clickable link to `specs/<module>.SPEC.md` (if present), and present `ask_question`:
   - `(Recommended) Proceed to /craft on <module>`
   - `Inspect module details in /matrix`
   - `Pause for now`
