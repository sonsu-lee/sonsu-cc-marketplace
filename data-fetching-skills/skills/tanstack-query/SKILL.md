---
name: tanstack-query
description: >
  This skill should be used when the user asks about "TanStack Query", "React Query",
  "useQuery", "useMutation", "query keys", "query invalidation", "caching strategy",
  "optimistic updates", "prefetching", or when writing or reviewing data fetching code
  in Client Components.
---

# TanStack Query Conventions

Pattern enforcement for server state management in Client Components. These conventions define query key structure, mutation patterns, and caching strategy.

## Rule 1: Query Key Factory

Centralize query keys per domain entity. Never inline key arrays.

```typescript
// src/queries/todo.keys.ts
export const todoKeys = {
  all: ['todos'] as const,
  lists: () => [...todoKeys.all, 'list'] as const,
  list: (filters: TodoFilters) => [...todoKeys.lists(), filters] as const,
  details: () => [...todoKeys.all, 'detail'] as const,
  detail: (id: string) => [...todoKeys.details(), id] as const,
}

// Usage
useQuery({ queryKey: todoKeys.detail(id), queryFn: ... })
queryClient.invalidateQueries({ queryKey: todoKeys.lists() })
```

- One key factory per entity (users, todos, posts, etc.)
- Hierarchical keys enable scoped invalidation
- Export from a dedicated `*.keys.ts` file

## Rule 2: Custom Query Hooks

Wrap every `useQuery` and `useMutation` in a custom hook. Never use raw TanStack Query hooks in components.

```typescript
// src/queries/useTodos.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import { todoKeys } from './todo.keys'

export function useTodos(filters: TodoFilters) {
  return useQuery({
    queryKey: todoKeys.list(filters),
    queryFn: () => api.getTodos(filters),
  })
}

export function useTodo(id: string) {
  return useQuery({
    queryKey: todoKeys.detail(id),
    queryFn: () => api.getTodo(id),
    enabled: !!id,
  })
}

export function useCreateTodo() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: api.createTodo,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: todoKeys.lists() })
    },
  })
}
```

```tsx
// Component — clean and declarative
function TodoList() {
  const { data: todos, isLoading } = useTodos({ status: 'active' })
  const createTodo = useCreateTodo()
  // ...
}
```

- Query logic lives in `src/queries/` (or `src/hooks/queries/`)
- Components only consume custom hooks
- Query functions call an API client, not `fetch` directly

## Rule 3: Mutation → Invalidation Flow

After every mutation, invalidate relevant queries. Never manually `setQueryData` as the primary strategy.

```typescript
export function useUpdateTodo() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: api.updateTodo,
    onSuccess: (data, variables) => {
      // Invalidate list (refetches from server)
      queryClient.invalidateQueries({ queryKey: todoKeys.lists() })
      // Optionally update detail cache directly
      queryClient.setQueryData(todoKeys.detail(variables.id), data)
    },
  })
}
```

- `onSuccess` → `invalidateQueries` is the default pattern
- Use `setQueryData` only to avoid a redundant refetch when the mutation response contains the full updated entity
- Never use `setQueryData` alone without invalidation

## Rule 4: Optimistic Updates

Use optimistic updates for interactions that need instant feedback:

```typescript
export function useToggleTodo() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: api.toggleTodo,
    onMutate: async (todoId) => {
      await queryClient.cancelQueries({ queryKey: todoKeys.lists() })
      const previous = queryClient.getQueryData(todoKeys.list({ status: 'active' }))

      queryClient.setQueryData(todoKeys.list({ status: 'active' }), (old) =>
        old?.map(t => t.id === todoId ? { ...t, done: !t.done } : t)
      )

      return { previous }
    },
    onError: (_err, _vars, context) => {
      queryClient.setQueryData(todoKeys.list({ status: 'active' }), context?.previous)
    },
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: todoKeys.lists() })
    },
  })
}
```

- Always implement rollback in `onError`
- Always invalidate in `onSettled` (ensures server truth wins)
- Cancel outgoing queries in `onMutate` to avoid race conditions

## Rule 5: Default Configuration

```tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60,       // 1 minute
      gcTime: 1000 * 60 * 5,      // 5 minutes
      retry: 1,
      refetchOnWindowFocus: false,
    },
  },
})
```

- Set `staleTime` globally — `0` (default) causes too many refetches
- `refetchOnWindowFocus: false` unless real-time freshness is critical
- Override per query only when specific freshness requirements differ

## Rule 6: Suspense Mode

Prefer `useSuspenseQuery` with React Suspense boundaries:

```tsx
import { useSuspenseQuery } from '@tanstack/react-query'

function UserProfile({ userId }: { userId: string }) {
  const { data } = useSuspenseQuery({
    queryKey: userKeys.detail(userId),
    queryFn: () => api.getUser(userId),
  })

  // data is guaranteed — no loading/error handling needed here
  return <div>{data.name}</div>
}

// Parent handles loading boundary
<Suspense fallback={<Skeleton />}>
  <UserProfile userId={id} />
</Suspense>
```

- Use `useSuspenseQuery` for data that blocks rendering
- Place `<Suspense>` boundaries in parent layouts/pages
- Use regular `useQuery` when loading states should be inline

## Rule 7: When Not to Use TanStack Query

| Scenario | Use Instead |
|----------|-------------|
| Server Component data fetching | Direct `async/await` |
| Form mutations in Next.js | Server Actions |
| URL state (filters, pagination) | Search params |
| Global client state | `useState` / `useReducer` / Context |

TanStack Query is for server state in Client Components. If the component can be a Server Component, fetch data there instead.
