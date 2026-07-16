### Example 2: Mutation with Explicit States
**User Input**:
> "I need a form that subscribes a user to the newsletter, and I want clear loading/error/success feedback."

**Reasoning**:
1.  Validate the email with a schema before sending it anywhere.
2.  Centralize the write in one mutation function that returns an explicit success/error result rather than throwing past the UI boundary.
3.  Model the UI state as `idle | loading | success | error` so the form can render the correct feedback at every step, and invalidate/refetch anything the mutation affects (e.g. a subscriber count) on success.

**Action/Output**:
```tsx
// src/features/newsletter/services/newsletter-service.ts
import { z } from 'zod';

const subscribeSchema = z.object({
  email: z.string().email(),
});

export async function subscribeToNewsletter(email: string): Promise<void> {
  const result = subscribeSchema.safeParse({ email });
  if (!result.success) {
    throw new Error('Invalid email address');
  }

  const res = await fetch('/api/newsletter/subscribe', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(result.data),
  });

  if (!res.ok) {
    throw new Error('Failed to subscribe');
  }
}
```

```tsx
// src/shared/hooks/use-mutation.ts
import { useCallback, useState } from 'react';

type MutationState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success' }
  | { status: 'error'; message: string };

export function useMutation<Args extends unknown[]>(
  mutationFn: (...args: Args) => Promise<void>,
) {
  const [state, setState] = useState<MutationState>({ status: 'idle' });

  const mutate = useCallback(
    async (...args: Args) => {
      setState({ status: 'loading' });
      try {
        await mutationFn(...args);
        setState({ status: 'success' });
      } catch (err: unknown) {
        setState({
          status: 'error',
          message: err instanceof Error ? err.message : 'Something went wrong',
        });
      }
    },
    [mutationFn],
  );

  return { state, mutate };
}
```

```tsx
// src/features/newsletter/components/subscribe-form.tsx
import { useMutation } from '@/shared/hooks/use-mutation';
import { subscribeToNewsletter } from '../services/newsletter-service';

export function SubscribeForm() {
  const { state, mutate } = useMutation(subscribeToNewsletter);

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        const email = new FormData(e.currentTarget).get('email') as string;
        mutate(email);
      }}
    >
      <input name="email" type="email" required disabled={state.status === 'loading'} />
      <button type="submit" disabled={state.status === 'loading'}>
        {state.status === 'loading' ? 'Subscribing…' : 'Subscribe'}
      </button>

      {state.status === 'success' && <p>Subscribed! Check your inbox.</p>}
      {state.status === 'error' && <p role="alert">{state.message}</p>}
    </form>
  );
}
```
