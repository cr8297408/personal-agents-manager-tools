---
name: frontend-performance
description: >
  Specialized agent for auditing and optimizing React-based frontend applications (Next.js, Remix, Vite, React Router, or plain React) for Core Web Vitals, SEO, and performance. Focuses on metadata, asset optimization (images/fonts), bundle size, and rendering boundaries.
  Trigger: "Optimize frontend performance", "Improve Core Web Vitals", "Lighthouse check", "Reduce bundle size", "SEO metadata", "Image and font optimization".
version: 2.0.0
author: github.com/cr8297408
license: Apache-2.0
tags:
  - react
  - frontend
  - optimization
  - seo
  - performance
  - nextjs
  - remix
allowed_tools:
  - run_command
  - view_file
  - write_to_file
---

# Frontend Performance Expert

## 1. Overview
This skill focuses on maximizing the performance and discoverability of React-based frontend applications, independent of the underlying framework. It covers correct SEO metadata, image and font optimization, and strategies for reducing client-side bundle size (e.g., code-splitting, lazy loading, bundle analysis). Framework-specific APIs (Next.js `next/image`/`next/font`/Metadata API, Remix `meta`/loaders, Vite lazy imports) are applied as the concrete implementation of these framework-agnostic principles.

## 2. Prerequisites & Context
*   **Required Tools**:
    *   `run_command`: To build the project and run analysis tools (e.g., a bundle analyzer for the project's bundler).
    *   `view_file`: To inspect code for unoptimized patterns.
*   **Environment**: Any modern React-based frontend stack (production build preferred for analysis).
*   **Input**:
    *   Specific performance bottleneck (if known).
    *   Target URL or route to optimize.

## 3. Workflow
1.  **Analyze Metadata**: Check page/route entry points for proper title, description, and Open Graph metadata.
2.  **Audit Assets**: Scan for raw `<img>` tags that should use responsive image techniques (`srcset`/`sizes`, modern formats, lazy loading), and font imports that could cause layout shift or render-blocking requests.
3.  **Review Client Boundaries**: Identify client-interactive code that is unnecessarily large, ships to the browser unnecessarily, or could be split/deferred.
4.  **Implement Optimizations**: Apply fixes (convert tags, add lazy loading, improve metadata).
5.  **Build & Verify**: Run a build to ensure no errors and check bundle size reports.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **Always** set explicit SEO metadata (title, description, Open Graph) per page/route using whatever metadata mechanism the framework provides. Never manually hand-roll `<meta>` tags in the document head unless the framework offers no alternative.
-   [ ] **Rule 2**: **Always** serve responsive, lazy-loaded, appropriately-sized/format images. Prefer a framework-provided image component/loader over raw `<img>` when one exists.
-   [ ] **Rule 3**: **Always** load web fonts in a way that avoids Cumulative Layout Shift (CLS) and render-blocking: use `font-display: swap` (or equivalent), preload critical fonts, and subset where possible.
-   [ ] **Rule 4**: **Minimize** the amount of interactive/client-side JavaScript shipped to the browser. Push interactivity as far down the component tree as possible (leaf-component pattern) rather than making large ancestor components client-interactive.
-   [ ] **Rule 5**: Use dynamic/lazy imports (code-splitting) for heavy components that are not critical for the initial paint.

### Optimization Checklist
-   [ ] Title & Description on every page.
-   [ ] Open Graph images and tags.
-   [ ] Responsive images with correct sizing hints.
-   [ ] Optimized, non-blocking font loading with no layout shift.
-   [ ] Static generation / prerendering for routes that can be built ahead of time, where the framework supports it.

## Framework-specific notes

### Next.js (App Router)
- Use the Metadata API (static `metadata` export or `generateMetadata`) instead of manual `<meta>` tags.
- Use `next/image` for images — it handles lazy loading, sizing, and format conversion automatically.
- Use `next/font` (Google Fonts or local) to prevent CLS and optimize font loading.
- Minimize `"use client"` boundaries; keep Server Components as the default and push `"use client"` down the tree.
- Use `dynamic()` imports for heavy client components not critical to the initial paint.
- Use `generateStaticParams` for dynamic routes that can be statically generated (SSG).

### Remix
- Set metadata via the route's `meta` export (`meta: MetaFunction`), returning title/description/Open Graph tags per route.
- Images: use standard responsive `<img srcset sizes loading="lazy">`, or a dedicated image CDN/transform service, since there is no built-in `next/image` equivalent.
- Fonts: self-host with `font-display: swap` and `<link rel="preload">` for critical fonts, or use a fonts package (e.g. Fontsource).
- Defer non-critical data with `defer()` in loaders and stream it with `<Await>` to avoid blocking the initial render.
- Use `React.lazy`/dynamic `import()` for heavy client-only widgets.

### Vite / React Router
- Set metadata by managing `<title>`/`<meta>` tags directly (e.g. via a small head-management utility or manually per route component), since there is no framework-level metadata API.
- Use responsive `<img srcset sizes loading="lazy">`, or run images through a build-time image optimization plugin (e.g. `vite-imagetools`).
- Self-host fonts with `font-display: swap` and preload hints; avoid third-party render-blocking font requests.
- Rely on `React.lazy` + `Suspense` and route-based code-splitting (React Router's lazy route modules) to keep the initial bundle small.
- Use the bundler's built-in bundle analyzer (e.g. `rollup-plugin-visualizer` for Vite) to audit bundle size.

## 5. Examples

### Example 1: SEO Setup
See [examples/seo-setup.md](examples/seo-setup.md).

### Example 2: Font Optimization
See [examples/font-optimization.md](examples/font-optimization.md).
