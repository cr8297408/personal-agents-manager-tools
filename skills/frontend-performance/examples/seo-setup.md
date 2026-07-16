### Example 1: SEO Setup
**User Input**:
> "Make sure my blog posts are SEO friendly."

**Reasoning**:
Dynamic blog posts need dynamic metadata (title, description, Open Graph) computed from the post content, generated per-route rather than hardcoded once.

**Action/Output** (framework-agnostic principle):
> "Generate title, description, and Open Graph tags from the fetched post data at the point where the route is rendered, not statically. Every dynamic detail page needs its own metadata block."

### Framework-specific implementation

**Next.js (App Router)** — use the Metadata API:
```typescript
import { Metadata } from 'next';

type Props = {
  params: { slug: string }
};

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const post = await getPost(params.slug);

  return {
    title: post.title,
    description: post.summary,
    openGraph: {
      images: [post.coverImage],
    },
  };
}

export default function BlogPost({ params }: Props) {
  // ... render ...
}
```

**Remix** — use the route's `meta` export:
```typescript
import type { MetaFunction, LoaderFunctionArgs } from '@remix-run/node';
import { useLoaderData } from '@remix-run/react';

export async function loader({ params }: LoaderFunctionArgs) {
  const post = await getPost(params.slug!);
  return { post };
}

export const meta: MetaFunction<typeof loader> = ({ data }) => {
  if (!data) return [];
  const { post } = data;
  return [
    { title: post.title },
    { name: 'description', content: post.summary },
    { property: 'og:image', content: post.coverImage },
  ];
};

export default function BlogPost() {
  const { post } = useLoaderData<typeof loader>();
  // ... render ...
}
```

**Vite / React Router (SPA)** — manage `document.title` and meta tags in the route component (or via a small head-management hook), since there is no framework-level metadata API:
```typescript
import { useEffect } from 'react';
import { useLoaderData } from 'react-router-dom';

export default function BlogPost() {
  const post = useLoaderData() as Post;

  useEffect(() => {
    document.title = post.title;
  }, [post.title]);

  // ... render ...
}
```
