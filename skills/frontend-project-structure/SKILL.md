---
name: frontend-project-structure
description: >
  Specialized agent for setting up React-based frontend projects (Next.js, Remix, Vite + React Router) and enforcing a Business-Oriented Modular Architecture (Screaming Architecture). Handles project scaffolding, feature/domain folder structure, path aliases, and file conventions to ensure code is organized by business domain rather than technical type.
  Trigger: "Setup frontend project", "Project structure", "Scaffold React app", "project architecture", "Business modules", "Feature-based folder structure".
version: 2.0.0
author: github.com/cr8297408
license: Apache-2.0
tags:
  - react
  - frontend
  - architecture
  - scaffolding
  - modularity
  - nextjs
  - remix
allowed_tools:
  - run_command
  - write_to_file
  - list_dir
---

# Frontend Project Structure Specialist

## 1. Overview
This skill specializes in initializing and structuring React-based frontend applications using **Business-Oriented Modular Architecture**. It enforces a strict organization where code is grouped by **Business Domain** (Modules/Features) rather than technical type. This ensures the project structure "screams" its intent, making it easier to scale and maintain — regardless of whether the underlying framework is Next.js, Remix, or Vite + React Router.

## 2. Prerequisites & Context
*   **Required Tools**:
    *   `run_command`: To scaffold the base project (e.g. `npx create-next-app`, `npx create-remix`, `npm create vite`) and install dependencies.
    *   `write_to_file`: To create directory structures and configuration files.
    *   `list_dir`: To verify the current project state.
*   **Environment**: Node.js 18.17+ environment.
*   **Input**:
    *   Project name.
    *   List of Business Concepts (e.g., Checkout, Authentication, Inventory).

## 3. Workflow
1.  **Analyze Request**: Identify the core **Business Modules** (features/domains) of the application.
2.  **Scaffold Base Project**: Run the framework's official scaffolding command with strict flags (TypeScript, linting).
3.  **Implement Modular Architecture**:
    *   Create a `src/features/<module-name>` folder for each business domain.
    *   **Enforce Module Isolation**: All components, hooks, and data-access logic related to a module MUST live inside that module's directory.
    *   Wire up the framework's routing layer (route files, route config, or file-based routes) as thin entry points that import from the corresponding feature folder.
4.  **Configure Aliases**: Update the TypeScript/bundler config to support module-based imports (e.g. `@/features/*`, `@/shared/*`).
5.  **Clean & Verify**: Remove technical groupings (like a single global `components/` folder holding feature-specific components) and enforce business grouping.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **Structure by Business Module**: The top-level feature folders MUST represent Business Domains (e.g., `billing`, `user-management`), not technical layers (e.g. not `components/`, `hooks/`, `services/` as top-level siblings).
-   [ ] **Rule 2**: **Colocation is King**: All files related to a specific business module (UI, logic, state) MUST be colocated within that module's folder.
-   [ ] **Rule 3**: **Module Boundaries**: Use the framework's routing convention (route groups, file-based routes, or a central route config) to define module boundaries without polluting the URL structure.
-   [ ] **Rule 4**: **Strict Isolation**: A module should NOT import private internal details of another module. Shared logic must go to a `shared/` layer (see the `component-scope-rule` skill for the promotion criteria).
-   [ ] **Rule 5**: **Screaming Architecture**: A developer looking at the file tree should instantly understand what the business does, not just which frontend framework it uses.

### Formatting Guidelines
-   **File Naming**: Use kebab-case.
-   **Directory Structure**: `src/features/<business-module>/<sub-feature>/...`
-   **Code Style**: Functional components, TypeScript.

## Framework-specific notes

The business-module folder (`src/features/<module>/...`) is the same in every stack. What differs is how the framework's router connects to it.

### Next.js (App Router)
- Use Route Groups `(module-name)` under `src/app` to define module boundaries without affecting the URL.
- Private, module-scoped folders are prefixed with `_` (e.g. `_components`, `_hooks`, `_actions`) so the App Router excludes them from routing.
- Strict root files per route: `layout.tsx`, `page.tsx`, `loading.tsx`, `error.tsx`, `not-found.tsx`.
- API routes live under `src/app/api/<resource>/route.ts` and call into the owning module's server logic.

### Remix
- Routes are file-based under `app/routes/` (flat-file or nested folder convention depending on Remix version). Keep route files thin: they wire up `loader`/`action`/`meta`/default export and delegate real logic to `app/features/<module>/`.
- Shared logic across routes of the same module still lives in `app/features/<module>/{components,hooks,services}`.
- Layout nesting is expressed through Remix's parent/child route hierarchy rather than route groups.

### Vite / React Router
- There's no file-based routing convention out of the box; define routes centrally (e.g. `src/routes.tsx` or `src/app/router.tsx`) and point each route to a feature's top-level page component.
- Feature folders under `src/features/<module>/{components,hooks,services}` are imported by the route config, not auto-discovered.
- Use `React.lazy()` combined with React Router's lazy route modules to code-split each module's route.

## 5. Examples

### Example 1: New E-commerce Project
See [examples/new-project.md](examples/new-project.md).

### Example 2: Restructuring an Existing App
See [examples/restructuring.md](examples/restructuring.md).
