### Example 2: Font Optimization
**User Input**:
> "I want to use Inter font but it's causing layout shift."

**Reasoning**:
Layout shift from web fonts is almost always caused by loading fonts via a blocking CSS `@import` or a late-discovered `<link>` with no `font-display` strategy, so the browser renders with a fallback font and then swaps/reflows once the webfont arrives. The fix is framework-agnostic: self-host (or preload) the font and use `font-display: swap`, ideally through whatever font-loading mechanism the framework provides so it can inline the right `@font-face` and preload hints automatically.

**Action/Output** (framework-agnostic principle):
> "Stop loading the font via a render-blocking `@import`. Self-host the font file, add `font-display: swap`, and preload the critical weight so the browser reserves layout space before the font loads."

### Framework-specific implementation

**Next.js (App Router)** — use `next/font/google`, which self-hosts and preloads automatically:
```typescript
import { Inter } from 'next/font/google';

const inter = Inter({ subsets: ['latin'] });

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body className={inter.className}>{children}</body>
    </html>
  );
}
```

**Remix** — self-host via a package like Fontsource, or a local `@font-face`, and preload the critical weight:
```typescript
// app/root.tsx
import interWoff2 from '@fontsource/inter/files/inter-latin-400-normal.woff2';
import type { LinksFunction } from '@remix-run/node';

export const links: LinksFunction = () => [
  { rel: 'preload', href: interWoff2, as: 'font', type: 'font/woff2', crossOrigin: 'anonymous' },
];
```
```css
/* app/styles/fonts.css */
@font-face {
  font-family: 'Inter';
  src: url('@fontsource/inter/files/inter-latin-400-normal.woff2') format('woff2');
  font-display: swap;
}
```

**Vite / React Router** — same self-hosting approach, wired through Vite's asset pipeline:
```css
/* src/styles/fonts.css */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-latin-400.woff2') format('woff2');
  font-display: swap;
}
```
```html
<!-- index.html -->
<link rel="preload" href="/fonts/inter-latin-400.woff2" as="font" type="font/woff2" crossorigin>
```
