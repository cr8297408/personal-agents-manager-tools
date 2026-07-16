### Example 2: Restructuring to Business Modules
**User Input**:
> "Move my dashboard pages into a proper feature-module structure. It's getting messy."

**Reasoning**:
1.  **Analyze**: "Dashboard" is often a technical term, but in business terms, it might be the **Backoffice** or **Account Management** module. Let's assume it's a "Vendor Portal" business capability.
2.  **Modules**: Create a `vendor-portal` feature module.
3.  **Scope Rule Application**: Locate all "shared" components that are actually only used by the dashboard (e.g., `DashboardSidebar`, `StatsCard`) and **move them INSIDE** the new module. They are NOT shared; they are scoped to this business module (see the `component-scope-rule` skill).

**Action/Output** (framework-agnostic):
```bash
# 1. Create the Business Module container
mkdir -p src/features/vendor-portal/dashboard

# 2. Move pages into the module
mv src/pages/dashboard/* src/features/vendor-portal/dashboard/

# 3. ENFORCE SCOPE RULE: Move "feature-shared" components into the module
# (Assuming they were incorrectly in src/shared/components)
mkdir -p src/features/vendor-portal/components
mv src/shared/components/dashboard-sidebar.tsx src/features/vendor-portal/components/
```

### Framework-specific rendition

**Next.js (App Router)** — the module boundary is a route group, and locality is enforced via `_`-prefixed private folders:
```bash
mkdir -p src/app/\(vendor-portal\)/dashboard
mv src/app/dashboard/* src/app/\(vendor-portal\)/dashboard/
mkdir -p src/app/\(vendor-portal\)/_components
mv src/shared/components/dashboard-sidebar.tsx src/app/\(vendor-portal\)/_components/
```

**Remix** — route files stay under `app/routes/`, but the components/logic they import move into a dedicated feature folder:
```bash
mkdir -p app/features/vendor-portal/components
mv app/shared/components/dashboard-sidebar.tsx app/features/vendor-portal/components/
# app/routes/dashboard.tsx now imports from app/features/vendor-portal/components
```

**Vite / React Router** — same move as the agnostic example; update the central route config's imports to point at the new feature folder path.
