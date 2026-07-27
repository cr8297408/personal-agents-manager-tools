### Example 2: Layout/Route-based RBAC
**User Input**:
> "Make sure only Admins can access the /admin section."

**Reasoning**:
Perimeter checks (from Example 1) only confirm the user is *authenticated*. Verifying *roles* (authorization) usually requires a database lookup, which is best performed at the layout/parent-route level that owns the protected feature — not in an edge/middleware layer that often can't reach the database, and not duplicated in every leaf component.

**Action/Output** (framework-agnostic principle):
> "Fetch the current user (with role) in the component/loader that owns the `/admin` section, and redirect away if the role doesn't match — before any admin-only content renders."

### Framework-specific implementation

**Next.js (App Router)** — checked in the route group's `layout.tsx`:
```typescript
// src/app/(admin)/layout.tsx
import { redirect } from 'next/navigation'
import { getCurrentUser } from '@/lib/auth' // DAL function

export default async function AdminLayout({
  children,
}: {
  children: React.ReactNode
}) {
  const user = await getCurrentUser()

  if (!user || user.role !== 'ADMIN') {
    redirect('/dashboard') // Or 404/403 page
  }

  return (
    <div className="admin-layout">
      {children}
    </div>
  )
}
```

**Remix** — checked in the parent route's `loader`:
```typescript
// app/routes/admin.tsx
import { redirect, type LoaderFunctionArgs } from '@remix-run/node';
import { getCurrentUser } from '@/lib/auth.server';

export async function loader({ request }: LoaderFunctionArgs) {
  const user = await getCurrentUser(request);
  if (!user || user.role !== 'ADMIN') {
    throw redirect('/dashboard');
  }
  return { user };
}

export default function AdminLayout() {
  // ... render <Outlet /> for nested admin routes ...
}
```

**Vite / React Router (SPA)** — checked in a role-aware guard component; the backend API must still independently enforce the same rule on every admin-only request:
```typescript
// src/features/admin/role-guard.tsx
import { Navigate, Outlet } from 'react-router-dom';
import { useAuth } from '@/lib/auth';

export function RequireAdmin() {
  const { user } = useAuth();
  if (!user || user.role !== 'ADMIN') return <Navigate to="/dashboard" replace />;
  return <Outlet />;
}
```
