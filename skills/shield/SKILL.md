---
name: shield
description: >-
  Active and passive security, privacy, and compliance audit guardrail. Inspects
  diffs for leaked secrets, XSS/injection sinks, broken authentication or
  authorization rules, CSP/header regressions, and LGPD/GDPR data minimization
  violations before committing or dispatching code.
---

# /shield — Security & Privacy Audit Guardrail

Enforces non-negotiable security and privacy standards across every code change. Activate automatically when modifying APIs, auth flows, HTTP headers/CSP, database rules, or user data handling, and explicitly between `/craft` and `/checkpoint`.

---

## Audit Checklist (The 4 Core Laws + Hardening)

Inspect `git status --short` and `git diff` across all modified and untracked files:

1. **Law 1 — Secrets, Credentials & Repository Visibility Hygiene:**
   - Scan for hardcoded API keys, passwords, private keys, JWTs, OAuth secrets, or connection strings.
   - Verify private backend secrets are NEVER exposed in client bundles or public env prefixes (`VITE_*`, `NEXT_PUBLIC_*`, `REACT_APP_*`).
   - Confirm `.session.json`, `.env`, and `.env.*` (except `.env.example`) are covered by `.gitignore` (`git check-ignore`).
   - **Public Repository Guard:** Check `gh repo view --json isPrivate,visibility` (or `.session.json`). If the repository is **public** (or visibility is unknown, unless opted in via root `AGENTS.md`), verify via `git check-ignore` that `specs/` and `docs/REGISTRY.json` are ignored and **NOT** staged/untracked for commit.
   - **Action on violation:** Immediate rejection; block commit until remediated.

2. **Law 2 — Input Sanitization, Anti-XSS & Injection Defense:**
   - Verify no untrusted input (user fields, query params, DB records, AI responses) flows into raw HTML sinks (`innerHTML`, `outerHTML`, `document.write`, `dangerouslySetInnerHTML`) without strict sanitization (`DOMPurify.sanitize()`) or safe `.textContent`.
   - Verify SQL/NoSQL queries and shell commands are parameterized and safe from injection.
   - If HTTP security headers or CSP were modified, verify least privilege (e.g., avoid `'unsafe-eval'`, avoid widening `script-src` unnecessarily, restrict `worker-src`/`connect-src` tightly).

3. **Law 3 — Authentication & Authorization (AuthN / AuthZ):**
   - Verify protected endpoints, server actions, and database security rules enforce active authentication.
   - Verify resource ownership (`userId == auth.uid`) or RBAC checks are enforced server-side.

4. **Law 4 — Privacy & Data Minimization (LGPD / GDPR):**
   - Verify only strictly necessary personal data is collected, stored, or logged.
   - Verify internal UIDs, session tokens, private emails, and sensitive payloads are never leaked to public DOM, client logs, or external telemetry.

---

## Post-Audit Report & Interactive Prompt
Present a concise structured audit table (each Law with ✅ Pass / ⚠️ Warning / ❌ Fail and concrete evidence) and an overall verdict.

Then present `ask_question`:
- `(Recommended) Proceed to /checkpoint to generate the Markdown plan and commit changes` *(when approved)*
- `Return to /craft to remediate findings or refactor code`
- `Pause for now`
