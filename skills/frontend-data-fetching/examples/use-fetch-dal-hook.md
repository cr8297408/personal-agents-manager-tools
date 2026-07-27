### Example 1: Data Access Layer + Fetch Hook
**User Input**:
> "How should I fetch and display the current user's profile, without scattering fetch calls across components?"

**Reasoning**:
1.  Centralize the actual HTTP call and response validation in one DAL function (`getUserProfile`), so every consumer gets the same typed, validated result.
2.  Wrap the DAL call in a small, reusable client-side hook that models `idle | loading | success | error` explicitly, rather than relying on `data === undefined` to mean "loading."
3.  Components consume the hook and render each state explicitly — they never call `fetch` or parse JSON themselves.

**Action/Output**:
```tsx
// src/features/profile/services/profile-service.ts
import { z } from 'zod';

const profileSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
  avatarUrl: z.string().url().nullable(),
});

export type Profile = z.infer<typeof profileSchema>;

export async function getUserProfile(userId: string): Promise<Profile> {
  const res = await fetch(`/api/users/${userId}/profile`);
  if (!res.ok) {
    throw new Error('Unable to load profile');
  }
  const json = await res.json();
  return profileSchema.parse(json);
}
```

```tsx
// src/shared/hooks/use-fetch.ts
import { useEffect, useState } from 'react';

type FetchState<T> =
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; message: string };

export function useFetch<T>(fetcher: () => Promise<T>, deps: unknown[]): FetchState<T> {
  const [state, setState] = useState<FetchState<T>>({ status: 'loading' });

  useEffect(() => {
    let cancelled = false;
    setState({ status: 'loading' });

    fetcher()
      .then((data) => {
        if (!cancelled) setState({ status: 'success', data });
      })
      .catch((err: unknown) => {
        if (!cancelled) {
          setState({
            status: 'error',
            message: err instanceof Error ? err.message : 'Something went wrong',
          });
        }
      });

    return () => {
      cancelled = true;
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, deps);

  return state;
}
```

```tsx
// src/features/profile/components/profile-view.tsx
import { useFetch } from '@/shared/hooks/use-fetch';
import { getUserProfile } from '../services/profile-service';

export function ProfileView({ userId }: { userId: string }) {
  const state = useFetch(() => getUserProfile(userId), [userId]);

  if (state.status === 'loading') return <p>Loading profile…</p>;
  if (state.status === 'error') return <p role="alert">{state.message}</p>;

  const { data: profile } = state;
  // Explicit "empty" case: successfully loaded, but nothing meaningful to show
  if (!profile.name) return <p>This profile hasn't been set up yet.</p>;

  return (
    <div>
      <h2>{profile.name}</h2>
      <p>{profile.email}</p>
    </div>
  );
}
```
