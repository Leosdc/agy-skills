---
name: matrix
description: >-
  Queries, validates, and synchronizes the project's canonical inventory in
  docs/REGISTRY.json (modules, services, packages, schemas, and specifications).
  Use to explore system architecture, check module status, or detect unregistered
  code.
argument-hint: "[sync | schema <domain-or-entity>]"
---

# /matrix [command]

Queries and maintains `docs/REGISTRY.json` — the project's machine-readable **Source of Truth** for modules, services, packages, data schemas, and specifications.

---

## Commands

### Without Arguments (`/matrix`)
1. Read `docs/REGISTRY.json` (if missing, offer to initialize it from the standard template).
2. Render a clean summary table grouped by domain (`modules`, `services`, `packages`, `data`, etc.), showing:
   - ID & Name
   - Operational status (`🟢 prod-active`, `🟡 in-progress`, `🟣 dev`, `🔴 deprecated`)
   - Entrypoint path (clickable link)
   - Linked specification (`specs/<id>.SPEC.md`, clickable link)

### `/matrix sync`
1. Scan the source directories declared under each domain's `path` in `docs/REGISTRY.json` and all files in `specs/*.SPEC.md`.
2. Report:
   - Source modules/services not yet registered in `docs/REGISTRY.json`
   - Registry entries pointing to missing entrypoints or missing spec files
   - Orphan specs not linked in the registry
3. Ask via `ask_question` whether to update `docs/REGISTRY.json` to reconcile drift.

### `/matrix schema <target>`
1. Inspect the data/database domain in `docs/REGISTRY.json` and the project's schema files (e.g., Prisma, SQL migrations, Zod, JSON Schema, Firestore rules/indexes, Protobuf, OpenAPI).
2. Summarize fields, constraints, indexes, and authorization boundaries for `<target>`.
