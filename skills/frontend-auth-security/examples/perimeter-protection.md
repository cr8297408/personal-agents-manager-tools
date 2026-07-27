### Example 1: Perimeter Route Protection
**User Input**:
> "Protect the /dashboard routes so only logged in users can see them. Redirect others to /login."

**Reasoning**:
Authentication (is this request/user logged in?) should be rejected as early as possible — before any protected UI starts rendering or any protected data starts loading. The concrete mechanism depends on the framework, but the principle (check the session as close to the perimeter as the framework allows) is the same everywhere.

**Action/Output** (framework-agnostic principle):
> "Add a check that runs before the dashboard's protected content renders: read the session cookie/token, and if missing or invalid, redirect to `/login`."

### Framework-specific implementation

**Next.js (App Router)** — `middleware.ts` runs before the request reaches Server Components:
```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  const sessionToken = request.cookies.get('session_token')?.value

  const isDashboard = request.nextUrl.pathname.startsWith('/dashboard')

  if (isDashboard && !sessionToken) {
    return NextResponse.redirect(new URL('/login', request.url))
  }

  return NextResponse.next()
}

export const config = {
  matcher: ['/dashboard/:path*'],
}
```

**Remix** — the parent route's `loader` performs the check and redirects:
```typescript
// app/routes/dashboard.tsx
import { redirect, type LoaderFunctionArgs } from '@remix-run/node';
import { getSession } from '@/lib/session';

export async function loader({ request }: LoaderFunctionArgs) {
  const session = await getSession(request.headers.get('Cookie'));
  if (!session.has('userId')) {
    throw redirect('/login');
  }
  return null;
}
```

**Vite / React Router (SPA)** — a route guard component (or a data-router `loader`) checks client-side auth state; this is a UX affordance, not the real security boundary — the API must still reject unauthenticated requests independently:
```typescript
// src/features/dashboard/route-guard.tsx
import { Navigate, Outlet } from 'react-router-dom';
import { useAuth } from '@/lib/auth';

export function RequireAuth() {
  const { isAuthenticated } = useAuth();
  if (!isAuthenticated) return <Navigate to="/login" replace />;
  return <Outlet />;
}
```
