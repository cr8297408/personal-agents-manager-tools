---
name: frontend-data-fetching
description: >
  Framework-agnostic guidance for handling data fetching, caching, and mutations in modern React applications. Covers client vs server fetching strategies, the Data Access Layer (DAL) pattern, type-safe data access, cache/revalidation concepts, and rigorous loading/error/empty/success state handling.
  Trigger: "Fetch data", "Handle loading state", "Data access layer", "Cache strategy", "Mutation pattern", "Data fetching hook".
version: 1.0.0
author: github.com/cr8297408
license: Apache-2.0
tags:
  - frontend
  - react
  - data-fetching
  - dal
  - caching
allowed_tools:
  - write_to_file
  - view_file
---

# Frontend Data Fetching Expert

## 1. Overview
This skill handles the implementation of robust, type-safe data flows in any React-based frontend, independent of the framework or fetching library. It champions the **Data Access Layer (DAL)** pattern for reads, a consistent mutation pattern for writes, and treats loading/error/empty/success states as first-class, explicitly modeled outcomes rather than afterthoughts. For Next.js-specific implementation (Server Actions, `server-only`, React Server Components), see the **nextjs-data-flow** skill.

## 2. Prerequisites & Context
*   **Required Tools**:
    *   `write_to_file`: To generate data-access modules, hooks, and mutation functions.
    *   `view_file`: To inspect existing fetching code for anti-patterns.
*   **Environment**: Any React-based frontend (with or without server rendering), any fetching library (native `fetch`, TanStack Query, SWR, or framework-native loaders).
*   **Input**:
    *   Data model (e.g., User, Product).
    *   Desired operation (Read, Create, Update, Delete).
    *   Whether the fetch happens server-side (SSR/loader/RSC) or client-side.

## 3. Workflow
1.  **Decide Fetch Location**: Determine whether the data should be fetched server-side (before the page/route renders — better for SEO, avoids client waterfalls, keeps secrets off the client) or client-side (needed for user-triggered, highly interactive, or frequently-changing data).
2.  **Centralize in a Data Access Layer (DAL)**: Write a single, reusable, typed function per resource that performs the fetch/query. UI code calls the DAL function — it never talks to the API/DB/query client directly.
3.  **Validate at the Boundary**: Parse and validate untrusted input/output (form data, API responses) with a schema library (e.g. Zod) at the DAL boundary, not scattered through components.
4.  **Define Cache Behavior**: Decide the cache key, staleness window, and invalidation trigger for each piece of data (see Caching & Revalidation below).
5.  **Implement Mutations**: Write a single mutation function per write operation that validates input, performs the write, handles errors, and triggers cache invalidation/refetch of affected reads.
6.  **Model All UI States**: Every fetch/mutation consumer must explicitly handle `loading`, `error`, `empty`, and `success` — never assume the happy path or treat `undefined` as an implicit loading signal.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **Data Access Layer**: Centralize all reads for a given resource behind one typed function (e.g. `getUserProfile()`), never call fetch/DB/query clients directly from UI components.
-   [ ] **Rule 2**: **Validate at the Boundary**: Validate all external input (form data, query params) and, where trust is uncertain, response shapes, using a schema library before the data enters application logic.
-   [ ] **Rule 3**: **Explicit State Modeling**: Represent fetch/mutation status as an explicit discriminated union or status flag (`idle | loading | success | error`) plus a distinct **empty** case for "loaded successfully but there's nothing to show" — do not conflate `undefined`/`null` with "still loading."
-   [ ] **Rule 4**: **Never Expose Raw Errors**: Catch errors at the DAL/mutation boundary and surface user-friendly messages; never leak raw stack traces or backend error internals to the UI.
-   [ ] **Rule 5**: **Invalidate After Mutation**: Every mutation must invalidate or refetch the reads it affects — a write that doesn't reconcile the cache produces stale UI.
-   [ ] **Rule 6**: **Server Fetch by Default (when available)**: If the framework supports fetching before render (SSR, loaders, RSC), prefer it for initial page data; reserve client-side fetching for data that must update after user interaction or on an interval.

### Standard DAL + Mutation Shape
```typescript
// Read: single source of truth for a resource
async function getResource(id: string): Promise<Resource> { /* ... */ }

// Mutation: validates, writes, and reports outcome explicitly
type MutationState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; message: string };
```

## Framework-specific notes

### Next.js (App Router)
For Server Actions, `server-only` DAL enforcement, and React Server Component fetching patterns, use the dedicated **nextjs-data-flow** skill — it covers the same DAL/validation/mutation principles with Next-specific mechanics (`'use server'`, `revalidatePath`, `redirect`).

### Remix
Use route `loader`s for reads and `action`s for mutations — both run server-side and are Remix's equivalent of a DAL entry point plus mutation function. Access loader data via `useLoaderData()` and action results via `useActionData()`/`useFetcher()`. Use `defer()` for non-critical data you want to stream in after the initial render, and revalidate affected loaders automatically (Remix re-runs loaders on the current route after an `action` completes).

### SPA stacks (Vite, CRA) with TanStack Query / SWR
Wrap the DAL function in `useQuery` (TanStack Query) or `useSWR` (SWR) for client-side reads, using a stable query key per resource+params. Use `useMutation` for writes, and call `queryClient.invalidateQueries(key)` (or SWR's `mutate(key)`) on success to reconcile the cache — this is the client-side equivalent of Rule 5 above. Both libraries expose `isLoading`/`isError`/`data` states that map directly onto the explicit state model in Rule 3.

## 5. Examples

### Example 1: Data Access Layer + Fetch Hook
See [examples/use-fetch-dal-hook.md](examples/use-fetch-dal-hook.md).

### Example 2: Mutation with Explicit States
See [examples/mutation-with-states.md](examples/mutation-with-states.md).
