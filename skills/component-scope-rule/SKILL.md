---
name: component-scope-rule
description: >
  Specialized agent for determining the optimal placement of components, hooks, and logic in any React-based frontend project using the "Scope Rule". It prevents tech debt by strictly enforcing modularity: local by default, shared only when necessary.
  Trigger: "Where to put component", "Structure components", "Scope rule", "Shared vs Local", "Move component", "Feature-local vs shared".
version: 2.0.0
author: github.com/cr8297408
license: Apache-2.0
tags:
  - react
  - frontend
  - architecture
  - refactoring
  - scope-rule
allowed_tools:
  - list_dir
  - grep_search
  - move_file
---

# Component Scope Rule Architect

## 1. Overview
This skill implements the **Scope Rule** for React-based frontend architecture, a non-negotiable principle for maintaining scalable codebases regardless of the framework (Next.js, Remix, Vite + React Router, or plain React). It analyzes usage patterns to decide whether a component, hook, or utility should be **Local** (private to a feature) or **Shared** (available globally). It ruthlessly prevents "shared folder dumping" (premature abstraction).

## 2. Prerequisites & Context
*   **Required Tools**:
    *   `grep_search`: To count the number of times a component is imported across different features.
    *   `list_dir`: To explore current component locations.
    *   `move_file`: (Conceptual) To recommend moving files based on the rule.
*   **Environment**: Any React-based frontend project with a feature/domain-oriented folder structure (Next.js App Router, Remix, Vite + React Router, or plain React + bundler).
*   **Input**:
    *   Name of the file/component in question.
    *   (Optional) Usage context (e.g., "I want to use the `ProductCard` in the Cart page too").

## 3. Workflow
1.  **Identify Artifact**: Pinpoint the specific component, hook, or type being discussed.
2.  **Analyze Usage**:
    *   Use `grep_search` to find all imports of this artifact throughout the project's feature/route source tree.
    *   Count distinct **Features** (or route groups / domains) consuming it.
3.  **Apply Logic**:
    *   **Usage Count = 1**: The artifact MUST reside in that feature's private component/hook folder.
    *   **Usage Count ≥ 2**: The artifact MUST be moved to the project's shared/common layer (e.g. `src/shared/components`).
4.  **Recommend Action**: Provide exact move commands or refactoring instructions.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **1 Feature = Local**. If a component is used in only one feature (even if multiple times within that feature), it **must** stay in that feature's private folder.
-   [ ] **Rule 2**: **2+ Features = Shared**. As soon as a second, distinct feature imports a component, it **must** be promoted to the shared layer.
-   [ ] **Rule 3**: **Never** create a generic "Common" or "Utils" catch-all folder inside a feature. Use scoped names like `components/`, `hooks/`, or `utils.ts` local to that feature.
-   [ ] **Rule 4**: When promoting to shared, **always** ensure the component is decoupled from feature-specific logic (e.g., it shouldn't import from another feature's internals, an auth-specific hook, etc.).
-   [ ] **Rule 5**: Start Local. When creating a new component, **always** put it in the local feature folder first. Only move it later if needed.

### Logic Matrix
| Usage Count | Location | Example Path |
| :--- | :--- | :--- |
| 1 Feature | Local (feature-private) | `src/features/shop/components/product-card.tsx` |
| 2+ Features | Shared (global) | `src/shared/components/product-card.tsx` |
| 0 Features | Delete | (Ideally) |

## Framework-specific notes

The Scope Rule itself is framework-agnostic — only the folder conventions that express "local" vs "shared" change.

### Next.js (App Router)
Local components live inside a route group's private folder (folders prefixed with `_` are excluded from routing): `src/app/(shop)/shop/_components/product-card.tsx`. Promoting to shared moves the file to `src/shared/components/`.

### Remix
Route modules live under `app/routes/`. Feature-local components/hooks are colocated next to the domain logic that owns them (e.g. `app/features/shop/components/product-card.tsx`) and imported by the thin route file. Promoting to shared means moving the file to `app/shared/components/` (or your project's equivalent shared package).

### Vite / React Router
Feature folders live under `src/features/<feature>/components/`. Since there's no built-in "private folder" routing convention, enforce locality by code review / import-boundary lint rules (e.g. `eslint-plugin-boundaries`) rather than folder-based routing exclusion. Promotion target is the same `src/shared/components/`.

## 5. Examples

### Example 1: Promoting a Component
See [examples/promote-component.md](examples/promote-component.md).

### Example 2: Premature Optimization
See [examples/keep-local.md](examples/keep-local.md).
