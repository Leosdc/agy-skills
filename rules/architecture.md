# Architecture & Code Quality Standards

1. **Modularity & Clear Boundaries**:
   - Organize features into well-scoped modules, packages, or services following the repository's conventions documented in `docs/REGISTRY.json` and root `AGENTS.md`.
   - Avoid tight coupling across unrelated domains; communicate through explicit contracts, interfaces, or event buses.

2. **Clean Code & Maintainability**:
   - **Single Responsibility Principle (SRP)**: Keep functions small and cohesive (heuristic: ~40 lines max).
   - **Clarity over cleverness**: Use descriptive names without cryptic abbreviations.
   - **Explicit Error Handling**: Handle network, I/O, and runtime failures explicitly with clear logs and fail-safe degradation — never silence exceptions with empty `catch` blocks.
   - **Typed Contracts**: Annotate public functions, schemas, and domain models using the project's native typing mechanism (TypeScript, JSDoc, Python type hints, Go/Rust types, etc.).

3. **UI, Themes & Canonical Components** *(when applicable)*:
   - Inspect existing design tokens, CSS variables, and shared component libraries before creating new UI controls.
   - Verify visual contrast and layout across all official project themes (e.g., light, dark, custom themes) and responsive breakpoints.

4. **Automated Validation**:
   - Before completing `/craft` or opening a PR, run the project's configured checks (unit/integration tests, linter, typechecker, and production build).
