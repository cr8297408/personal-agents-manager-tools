### Example 1: New E-commerce Project (Business Modules)
**User Input**:
> "Create a new Next.js app called 'shop-pro' with authentication and a product catalog."

**Reasoning**:
1.  **Identify Business Domains**: The request implies two distinct business capabilities: **Identity Management** (Auth) and **Catalogue Management** (Products).
2.  **App Structure**: We will use Route Groups to define these modules: `(identity)` and `(catalogue)`.
3.  **Scope Rule**: All components specific to auth (like login forms) MUST go inside `(identity)`. All components specific to products (like product cards) MUST go inside `(catalogue)`.

**Action/Output**:
```bash
npx create-next-app@latest shop-pro --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-npm
```

*Followed by strict file structure creation:*
```text
src/
  app/
    (identity)/              <-- Business Module: Identity
      login/
        page.tsx
        _components/         <-- Scope Rule: Login UI is private to Identity
          login-form.tsx
      register/
        page.tsx
    (catalogue)/             <-- Business Module: Catalogue
      products/
        page.tsx
        _components/         <-- Scope Rule: Product UI is private to Catalogue
          product-card.tsx
          price-tag.tsx
    layout.tsx               <-- Root Layout
    page.tsx                 <-- Marketing Landing Page
```
