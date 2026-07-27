### Example 1: New E-commerce Project (Business Modules)
**User Input**:
> "Create a new frontend app called 'shop-pro' with authentication and a product catalog."

**Reasoning**:
1.  **Identify Business Domains**: The request implies two distinct business capabilities: **Identity Management** (Auth) and **Catalogue Management** (Products), plus a **Dashboard** (account/profile management).
2.  **Module Structure**: Each business domain gets its own top-level feature folder: `auth`, `dashboard`, `shop`.
3.  **Scope Rule**: All components specific to auth (like login forms) MUST go inside `features/auth`. All components specific to products (like product cards) MUST go inside `features/shop`. Only components used by 2+ features are promoted to `shared/` (see the `component-scope-rule` skill).

**Action/Output** (framework-agnostic structure):
```text
src/
  features/
    auth/                           # Identity management module
      login/
        page.tsx                    # /login route entry
        components/                 # Local: login-specific components
          login-form.tsx
      register/
        page.tsx                    # /register route entry
        components/                 # Local: register-specific components
          register-form.tsx
      components/                   # Local: shared within auth module
        social-login.tsx            # Used by both login and register
      hooks/
        use-auth.ts
      services/                     # Data access / mutations for this module
        auth-service.ts
      types.ts
      utils.ts
    dashboard/                      # Backoffice / account management module
      dashboard/
        page.tsx
        components/
          stats-card.tsx
          dashboard-grid.tsx
      profile/
        page.tsx
        components/
          profile-form.tsx
          avatar-upload.tsx
      hooks/
        use-dashboard.ts
        use-profile.ts
      services/
        profile-service.ts
      types.ts
    shop/                           # Catalogue / commerce module
      shop/
        page.tsx
        components/
          product-list.tsx
          product-filter.tsx
      cart/
        page.tsx
        components/
          cart-item.tsx
          cart-summary.tsx
      wishlist/
        page.tsx
        components/
          wishlist-item.tsx
          wishlist-grid.tsx
      hooks/
        use-products.ts
        use-cart.ts
        use-wishlist.ts
      services/
        product-service.ts
        cart-service.ts
        wishlist-service.ts
      types.ts
  shared/                           # ONLY for 2+ feature usage
    components/
      ui/                           # Reusable UI components
        button.tsx
        card.tsx
        input.tsx
      product-card.tsx              # Used across shop, cart, wishlist
      cart-widget.tsx                # Used in multiple features
    hooks/
      use-local-storage.ts
      use-debounce.ts
    types/
      api.ts
  lib/                               # Utilities and configuration
    auth-client.ts
    api-client.ts
    utils.ts
    validations.ts                   # Zod schemas
  styles/
    components.css
```

### Framework-specific rendition

**Next.js (App Router)** — the same modules expressed as route groups, with private (`_`-prefixed) folders and root-level route files:
```bash
npx create-next-app@latest shop-pro --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-npm
```
```text
src/
  app/
    (auth)/
      login/
        page.tsx
        _components/
          login-form.tsx
      register/
        page.tsx
        _components/
          register-form.tsx
      _components/
        social-login.tsx
      _hooks/
        use-auth.ts
      _actions/
        auth-actions.ts
      layout.tsx
    (dashboard)/
      dashboard/
        page.tsx
        loading.tsx
        error.tsx
        _components/
          stats-card.tsx
      profile/
        page.tsx
        _components/
          profile-form.tsx
      layout.tsx
    (shop)/
      shop/
        page.tsx
        _components/
          product-list.tsx
      cart/
        page.tsx
      layout.tsx
    api/
      auth/route.ts
      products/route.ts
    page.tsx
    layout.tsx
    loading.tsx
    error.tsx
    not-found.tsx
  shared/
    components/ui/
```

**Remix** — the same modules expressed as flat/nested route files under `app/routes/` that import from `app/features/<module>/`:
```text
app/
  routes/
    login.tsx                # imports from app/features/auth
    register.tsx
    dashboard._index.tsx     # imports from app/features/dashboard
    dashboard.profile.tsx
    shop._index.tsx          # imports from app/features/shop
    shop.cart.tsx
  features/
    auth/{components,hooks,services}
    dashboard/{components,hooks,services}
    shop/{components,hooks,services}
  shared/components/ui/
```

**Vite / React Router** — same `src/features/<module>` layout as the agnostic tree above, wired through a central route config:
```typescript
// src/routes.tsx
import { lazy } from 'react';
const ShopPage = lazy(() => import('@/features/shop/shop/page'));
const CartPage = lazy(() => import('@/features/shop/cart/page'));
// ...
```
