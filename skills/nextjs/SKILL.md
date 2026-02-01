---
name: nextjs
description: >
  This skill should be used when the user asks about "Next.js", "App Router",
  "Server Actions", "route handlers", "middleware", "generateMetadata",
  "data fetching in Next.js", "caching strategy", "Next.js conventions",
  or when writing or reviewing Next.js application code.
---

# Next.js Conventions

Pattern enforcement for Next.js App Router applications. These conventions define routing structure, data fetching strategy, and rendering boundaries.

## Rule 1: App Router File Conventions

```
app/
├── layout.tsx          # Root layout (required)
├── page.tsx            # / route
├── loading.tsx         # Suspense fallback for page
├── error.tsx           # Error boundary ('use client' required)
├── not-found.tsx       # 404
├── dashboard/
│   ├── layout.tsx      # Nested layout
│   ├── page.tsx        # /dashboard
│   └── [id]/
│       └── page.tsx    # /dashboard/:id
└── api/
    └── webhooks/
        └── route.ts    # API route handler
```

- Every route segment is a directory with `page.tsx`
- `layout.tsx` wraps child routes and preserves state across navigation
- `loading.tsx` creates automatic Suspense boundaries
- `error.tsx` must be a Client Component (`'use client'`)

## Rule 2: Server Components for Data, Client Components for Interaction

This mirrors the react skill's Rule 1, applied to Next.js:

```tsx
// Server Component — fetches data directly
async function ProductPage({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params
  const product = await getProduct(id)
  return (
    <div>
      <h1>{product.name}</h1>
      <AddToCartButton productId={id} />  {/* Client Component leaf */}
    </div>
  )
}
```

- Pages and layouts are Server Components by default
- Push `'use client'` to leaf components (buttons, forms, interactive widgets)
- Never put `'use client'` on `layout.tsx` or `page.tsx` — extract interactivity into child components

## Rule 3: Server Actions for Mutations

Use Server Actions for all data mutations. No client-side API calls for write operations.

```tsx
// app/actions.ts
'use server'

import { revalidatePath } from 'next/cache'

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string
  await db.insert('posts', { title })
  revalidatePath('/posts')
}

export async function deletePost(id: string) {
  await db.delete('posts', { id })
  revalidatePath('/posts')
}
```

```tsx
// In a Client Component
'use client'
import { createPost } from '@/app/actions'

export function CreatePostForm() {
  return (
    <form action={createPost}>
      <input name="title" required />
      <button type="submit">Create</button>
    </form>
  )
}
```

- Declare with `'use server'` at the top of the file or inside a function
- Always call `revalidatePath` or `revalidateTag` after mutations
- Use `useActionState` for progressive enhancement with loading/error states
- Use `useOptimistic` for instant UI feedback

## Rule 4: Route Handlers for External APIs

Use `app/api/*/route.ts` only for webhook endpoints, OAuth callbacks, or external service integrations — not for internal data fetching.

```tsx
// app/api/webhooks/stripe/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  const payload = await request.json()
  // Verify webhook signature
  // Process event
  return NextResponse.json({ received: true })
}
```

- Internal data: fetch directly in Server Components
- Internal mutations: Server Actions
- External integrations: Route Handlers

## Rule 5: Metadata and SEO

```tsx
// Static metadata
export const metadata: Metadata = {
  title: 'Dashboard',
  description: 'User dashboard',
}

// Dynamic metadata
export async function generateMetadata({ params }): Promise<Metadata> {
  const product = await getProduct(params.id)
  return {
    title: product.name,
    description: product.description,
    openGraph: { images: [product.image] },
  }
}
```

- Every page must define `metadata` or `generateMetadata`
- Root layout sets default metadata, pages override
- Use `generateStaticParams` for static generation of dynamic routes

## Rule 6: Caching and Revalidation

| Pattern | When |
|---------|------|
| `revalidatePath('/path')` | After mutation (Server Action) |
| `revalidateTag('tag')` | After mutation affecting tagged data |
| `export const revalidate = 60` | Time-based (ISR) |
| `export const dynamic = 'force-dynamic'` | Always fresh (no cache) |

Default strategy:
- Static pages: build-time generation with ISR via `revalidate`
- Dynamic pages: `revalidatePath`/`revalidateTag` after mutations
- Use `force-dynamic` only when data must be real-time on every request

## Rule 7: Middleware for Cross-Cutting Concerns

```tsx
// middleware.ts (project root)
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  const token = request.cookies.get('session')
  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
  return NextResponse.next()
}

export const config = {
  matcher: ['/dashboard/:path*'],
}
```

- Use for auth redirects, geo-routing, and header modification
- Keep middleware lightweight — it runs on every matched request
- Define `matcher` to scope execution
