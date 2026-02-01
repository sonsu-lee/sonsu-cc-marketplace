---
name: react
description: >
  This skill should be used when the user asks to "create a React component",
  "write a hook", "refactor component", "React best practices",
  "Server Components", "Client Components", "component composition",
  "React performance", or when writing or reviewing React code.
---

# React Conventions

Pattern enforcement for React 18/19 components. These conventions define how to structure components, manage state, and compose UI in this project.

## Rule 1: Server Components by Default

Every component is a Server Component unless it needs browser interactivity. Only add `'use client'` when the component uses:

- Event handlers (`onClick`, `onChange`, `onSubmit`)
- State or lifecycle hooks (`useState`, `useEffect`, `useReducer`)
- Browser-only APIs (`window`, `document`, `localStorage`)
- Custom hooks that depend on state or effects

```tsx
// Server Component (default) — no directive needed
async function UserList() {
  const users = await db.query('SELECT * FROM users')
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>
}

// Client Component — only when interactivity required
'use client'
function Counter() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}
```

**Push `'use client'` as far down the tree as possible.** A page that fetches data should remain a Server Component; only the interactive leaf (button, form, dropdown) should be a Client Component.

## Rule 2: Component Structure

```tsx
// 1. Directive (if needed)
'use client'

// 2. Imports
import { useState } from 'react'
import { Button } from '@/components/ui/button'

// 3. Types (colocated)
interface UserCardProps {
  user: User
  onSelect: (id: string) => void
}

// 4. Component (named export)
export function UserCard({ user, onSelect }: UserCardProps) {
  return (...)
}
```

- **Named exports** for all components — no `export default`.
- **Props as interface** — named `{ComponentName}Props`.
- **One component per file** as the primary export. Small helper components within the same file are acceptable when tightly coupled.

## Rule 3: Hooks Conventions

**Custom hooks** extract reusable logic. Name with `use` prefix and return typed values.

```tsx
function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState(value)
  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay)
    return () => clearTimeout(timer)
  }, [value, delay])
  return debounced
}
```

**Effect rules:**
- Always include a cleanup function when subscribing to external resources
- Dependencies must be exhaustive — never suppress the linter
- If an effect runs on every render (no deps array), it's likely wrong

**Memoization rules:**
- Do not reach for `useMemo`/`useCallback` by default
- Add memoization only when: a child is wrapped in `memo()`, passing to a dependency array, or profiling shows a measurable perf issue
- Prefer `useTransition` for deferring expensive renders over manual memoization

## Rule 4: Composition Over Configuration

Prefer component composition over prop-driven configuration.

```tsx
// Preferred: composition
<Card>
  <CardHeader>
    <CardTitle>Settings</CardTitle>
  </CardHeader>
  <CardContent>...</CardContent>
</Card>

// Avoid: prop soup
<Card title="Settings" headerVariant="large" showBorder contentPadding="lg" />
```

- Use `children` as the primary composition mechanism
- Use render props or slots for flexible content injection
- Avoid boolean prop flags that control rendering — split into separate components instead

## Rule 5: State Management

| Scope | Solution |
|-------|----------|
| Local component | `useState` |
| Complex local logic | `useReducer` |
| Shared between siblings | Lift state to nearest common parent |
| Subtree-wide | React Context (for low-frequency updates only) |
| Server data | TanStack Query (see tanstack-query skill) |
| URL state | Search params / route params |

- Never use Context for high-frequency updates (typing, scrolling, animations)
- Derive state from existing state instead of syncing with `useEffect`
- Avoid `useEffect` for data fetching in Client Components — use TanStack Query or Server Components

## Rule 6: Event Handling and Forms

```tsx
// Server Actions for form mutations (preferred in Next.js)
async function createPost(formData: FormData) {
  'use server'
  await db.insert('posts', { title: formData.get('title') })
  revalidatePath('/posts')
}

<form action={createPost}>
  <input name="title" />
  <button type="submit">Create</button>
</form>
```

- Use `useActionState` (React 19) for form state with server actions
- Use `useOptimistic` for optimistic UI on mutations
- Client-side forms: use controlled components with `onChange` or uncontrolled with `useRef`

## Rule 7: Key and List Rendering

- Always use stable, unique IDs as keys — never array index (unless list is static and never reordered)
- Extract list item rendering into a separate component when the item has its own state or event handlers
- Use `useMemo` on filtered/sorted lists only when profiling shows it's needed
