---
name: openapi-typescript
description: >
  This skill should be used when the user asks to "generate types from OpenAPI",
  "create API client types", "type-safe fetch", "openapi-typescript",
  "openapi-fetch", "openapi-react-query", "generate TypeScript from swagger",
  "typed API client", or when working with OpenAPI/Swagger schemas to produce
  TypeScript types and type-safe HTTP clients.
---

# OpenAPI TypeScript

Generate zero-runtime-cost TypeScript types from OpenAPI 3.0/3.1 schemas and use them with type-safe fetch clients.

## Ecosystem Overview

| Package | Purpose |
|---------|---------|
| `openapi-typescript` | Generate `.d.ts` types from OpenAPI schemas |
| `openapi-fetch` | Type-safe fetch client using generated types |
| `openapi-react-query` | TanStack Query integration for openapi-fetch |

---

## Type Generation

### CLI

```bash
# Install
npm i -D openapi-typescript typescript

# Generate from local schema
npx openapi-typescript ./api/v1.yaml -o ./src/types/api.d.ts

# Generate from remote schema
npx openapi-typescript https://myapi.dev/openapi.yaml -o ./src/types/api.d.ts
```

### npm script (recommended)

```json
{
  "scripts": {
    "typegen": "openapi-typescript ./api/v1.yaml -o ./src/types/api.d.ts"
  }
}
```

### Programmatic API

```typescript
import openapiTS from "openapi-typescript"

const output = await openapiTS("./api/v1.yaml")
```

---

## Type-Safe Fetch Client

### Setup

```bash
npm i openapi-fetch
npm i -D openapi-typescript typescript
```

### Basic Usage

```typescript
import createClient from "openapi-fetch"
import type { paths } from "./types/api"

const client = createClient<paths>({ baseUrl: "https://myapi.dev/v1/" })

// GET — path params are type-checked
const { data, error } = await client.GET("/blogposts/{post_id}", {
  params: {
    path: { post_id: "123" },
  },
})

// POST — body is type-checked
await client.POST("/blogposts", {
  body: {
    title: "My New Post",
  },
})
```

### Middleware (auth, headers, error handling)

```typescript
const authMiddleware = {
  async onRequest({ request }) {
    request.headers.set("Authorization", `Bearer ${getToken()}`)
    return request
  },
}

client.use(authMiddleware)
```

---

## React Integration (openapi-react-query)

### Setup

```bash
npm i openapi-react-query openapi-fetch
npm i -D openapi-typescript typescript
```

### Client Setup

```typescript
// src/lib/api.ts
import createFetchClient from "openapi-fetch"
import createClient from "openapi-react-query"
import type { paths } from "./types/api"

const fetchClient = createFetchClient<paths>({
  baseUrl: "https://myapi.dev/v1/",
})

export const $api = createClient(fetchClient)
```

### Usage in Components

```tsx
const MyComponent = () => {
  const { data, error, isLoading } = $api.useQuery(
    "get",
    "/blogposts/{post_id}",
    { params: { path: { post_id: 5 } } },
  )

  if (isLoading || !data) return "Loading..."
  if (error) return `Error: ${error.message}`

  return <div>{data.title}</div>
}
```

### queryOptions for Prefetching

```typescript
import { useQuery } from "@tanstack/react-query"
import { $api } from "./lib/api"

const options = $api.queryOptions("get", "/users/{user_id}", {
  params: { path: { user_id: 5 } },
})

// Use in loader, prefetch, or useQuery
const { data } = useQuery(options)
```

---

## Recommended Project Structure

```
src/
├── types/
│   └── api.d.ts          # Generated — do not edit
├── lib/
│   └── api.ts            # Client setup + middleware
└── ...
```

Add `api.d.ts` generation to CI or pre-commit to keep types in sync with the schema.

---

## Quick Decisions

| Scenario | Solution |
|----------|----------|
| Just need types | `openapi-typescript` CLI only |
| Need fetch client | Add `openapi-fetch` |
| React + TanStack Query | Add `openapi-react-query` |
| Auth headers | Middleware on `openapi-fetch` client |
| Multiple API schemas | Generate separate `.d.ts` per schema, separate clients |

---

## Additional Resources

### Reference Files

- **`references/advanced-patterns.md`** — Middleware patterns, error handling, multiple schemas, CI integration

