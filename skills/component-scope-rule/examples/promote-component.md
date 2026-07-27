### Example 1: Promoting a Component
**User Input**:
> "I built a `UserAvatar` in the dashboard feature's profile page, but now I need it in the top navigation bar (which is global layout)."

**Reasoning**:
1.  Current location: `src/features/dashboard/profile/components/user-avatar.tsx`.
2.  New Usage: `Global Layout`.
3.  Count: Used in Dashboard + Global Layout = 2 contexts.
4.  Decision: PROMOTE to `shared`.

**Action/Output**:
> "Since `UserAvatar` is now needed in both the Dashboard and the Global Layout, it violates the local scope rule. Move it to `src/shared/components/ui/user-avatar.tsx`."

```bash
mkdir -p src/shared/components/ui
mv src/features/dashboard/profile/components/user-avatar.tsx src/shared/components/ui/
# Don't forget to update imports!
```

Next.js App Router equivalent source path: `src/app/(dashboard)/profile/_components/user-avatar.tsx`.
