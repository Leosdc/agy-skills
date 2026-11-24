---
trigger: always_on
description: >-
  Non-negotiable security and privacy rules covering secrets hygiene, XSS and
  injection prevention, authentication/authorization, and LGPD/GDPR compliance.
---

# Mandatory Security & Privacy Rules

1. **Secrets & Credentials Protection**:
   - NEVER commit API keys, passwords, private keys, service account JSONs, or bearer tokens into source code or documentation.
   - Keep secret keys strictly on the backend/server runtime; never expose private credentials in client bundles or public environment prefixes (e.g., `VITE_*`, `NEXT_PUBLIC_*`, `REACT_APP_*`).
   - Ensure `.env`, `.env.local`, `.env.development`, and `.session.json` are always listed in `.gitignore`.

2. **Injection & XSS Prevention**:
   - NEVER write untrusted user data, database text, or AI-generated content directly to raw HTML sinks (`innerHTML`, `outerHTML`, `document.write`, `dangerouslySetInnerHTML`) without strict sanitization (e.g., `DOMPurify.sanitize()`) or safe text nodes (`.textContent`).
   - Always use parameterized queries / prepared statements / validated ORM builders to prevent SQL/NoSQL injection.
   - Apply least-privilege security headers (CSP, CORS, rate limiting) on HTTP servers.

3. **Authentication & Authorization (AuthN / AuthZ)**:
   - Require active authentication for protected routes, APIs, and database rules.
   - Enforce ownership (`userId === auth.uid`) or role-based access control (RBAC) server-side on every read/write operation.

4. **Privacy & Data Minimization (LGPD / GDPR)**:
   - Collect, store, and transmit only the minimum personal data strictly required for the feature.
   - Never expose sensitive identifiers (internal UIDs, tokens, private emails, session IDs) in public UI, client logs, or third-party telemetry.
