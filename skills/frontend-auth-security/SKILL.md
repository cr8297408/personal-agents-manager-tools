---
name: frontend-auth-security
description: >
  Specialized agent for implementing Authentication, Authorization (RBAC), and Security best practices in React-based frontend applications (Next.js, Remix, Vite + React Router). Focuses on session handling, perimeter route protection, fine-grained role checks, and safe data mutation patterns.
  Trigger: "Setup auth", "Protect route", "Configure route protection", "User roles", "Security check", "RBAC", "Session handling".
version: 2.0.0
author: github.com/cr8297408
license: Apache-2.0
tags:
  - react
  - frontend
  - auth
  - security
  - rbac
  - nextjs
  - remix
allowed_tools:
  - view_file
  - write_to_file
  - run_command
---

# Frontend Auth & Security Specialist

## 1. Overview
This skill specializes in securing React-based frontend applications, independent of framework. It handles Authentication flows (session or token based), Authorization (Role-Based Access Control) enforced at the appropriate layer, and strict separation of public/private data so secrets and sensitive logic never leak to the client bundle. Framework-specific mechanisms (Next.js middleware/Server Components, Remix loaders, SPA route guards) are the concrete implementation of these principles.

## 2. Prerequisites & Context
*   **Required Tools**:
    *   `view_file`: To audit existing security implementation.
    *   `write_to_file`: To create route-protection/auth logic.
*   **Environment**: Any modern React-based frontend stack, with or without server-side rendering.
*   **Input**:
    *   Auth provider details (e.g., Google, Email).
    *   Access requirements (e.g., "Admin only").

## 3. Workflow
1.  **Define Strategy**: Choose session-based (cookies) vs token-based (JWT/bearer). Cookie-based sessions are generally preferred whenever the app has a server that can set `HttpOnly` cookies.
2.  **Implement Perimeter Protection**: Add the earliest possible check that rejects/redirects unauthenticated requests before protected UI renders (middleware, root/parent loader, or a route guard, depending on the framework).
3.  **Implement RBAC**: Enforce role/permission checks close to the protected feature (a layout, a parent route, or a guarded section), not only at the perimeter.
4.  **Secure Mutations**: Ensure every mutation/API call re-validates authentication AND authorization server-side — never trust client-supplied user/role data.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **Perimeter Filtering**. Reject/redirect unauthenticated access as early as possible in the request or render lifecycle (e.g., "Must be logged in to see /dashboard").
-   [ ] **Rule 2**: **RBAC Close to the Resource**. Perform role checks (e.g., "Must be Admin") at the layout/parent-route level that owns the protected feature, not scattered across individual components.
-   [ ] **Rule 3**: **Server-Side Mutation Security**. NEVER trust the client. Always validate authentication AND authorization at the start of every server-side mutation/endpoint handler, regardless of what the client already checked.
-   [ ] **Rule 4**: **HttpOnly Cookies**. Store session tokens in `HttpOnly`, `Secure`, `SameSite=Lax/Strict` cookies whenever a server is available to set them. Never store session/auth tokens in `localStorage`/`sessionStorage`.
-   [ ] **Rule 5**: **Server-Only Boundary**. Keep code that handles sensitive data or secrets (API keys, DB clients) out of any module that can be bundled into client-side JavaScript — use the framework's server-only enforcement mechanism where one exists.
-   [ ] **Rule 6 (SPA-specific)**: In a client-only (SPA) app, a client-side route guard is a **UX convenience, not a security boundary**. The backend API is the only real enforcement point — it must independently re-check auth/authz on every request.

### Formatting Guidelines
-   **Auth Logic**: `src/lib/auth.ts` or `src/lib/session.ts` (or the framework-idiomatic equivalent).

## Framework-specific notes

### Next.js (App Router)
- Use `middleware.ts` for coarse-grained perimeter protection (session cookie present/absent) before the request reaches Server Components.
- Perform RBAC checks in the `layout.tsx` of the feature route group that owns the protected section (e.g. `(admin)/layout.tsx`), since role lookups typically require a DB call that Edge middleware often can't make.
- Server Actions must re-validate auth/authz at the top of the function body — never rely on the caller having passed through middleware.
- Import `server-only` in any file that handles sensitive data or secrets, so an accidental client import fails at build time.

### Remix
- Perform the perimeter check in a parent/root `loader` (e.g. `app/root.tsx` or a layout route's loader), redirecting via `redirect()` if there's no valid session.
- RBAC checks belong in the `loader` of the parent route that owns the protected feature (e.g. `app/routes/admin.tsx`), redirecting non-admins before the child routes render.
- `action` functions (Remix's mutation entry point) must independently re-check auth/authz — the same rule as Server Actions in Next.js.
- Session storage typically uses Remix's `createCookieSessionStorage` with `httpOnly: true, secure: true, sameSite: 'lax'`.

### Vite / React Router (SPA)
- Since there's no server rendering by default, the perimeter check is a client-side route guard component (or a data-router `loader` if using React Router's data APIs) that redirects unauthenticated users — this is a UX affordance only.
- The real authorization boundary is the backend API: every request must carry a token/cookie the API independently validates, and the API must enforce roles/permissions server-side regardless of what the SPA's UI allowed the user to click.
- Prefer an `HttpOnly` cookie set by the backend over storing a JWT in `localStorage`; if a bearer token is unavoidable, keep its lifetime short and never expose it to third-party scripts.

## 5. Examples

### Example 1: Perimeter Route Protection
See [examples/perimeter-protection.md](examples/perimeter-protection.md).

### Example 2: Layout/Route-based RBAC
See [examples/rbac-layout.md](examples/rbac-layout.md).
