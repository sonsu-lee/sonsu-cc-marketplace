# Advanced Patterns

## Middleware Patterns

### Authentication

```typescript
import createClient, { type Middleware } from "openapi-fetch"
import type { paths } from "./types/api"

const authMiddleware: Middleware = {
  async onRequest({ request }) {
    const token = getAccessToken()
    request.headers.set("Authorization", `Bearer ${token}`)
    return request
  },
}

const client = createClient<paths>({ baseUrl: "https://myapi.dev/v1/" })
client.use(authMiddleware)
```

### Error Logging

```typescript
const errorLogger: Middleware = {
  async onResponse({ response }) {
    if (!response.ok) {
      console.error(`API ${response.status}: ${response.url}`)
    }
    return response
  },
  async onError({ error }) {
    console.error("Fetch failed:", error)
    return new Error("Network request failed", { cause: error })
  },
}
```

### Chaining Multiple Middleware

```typescript
client.use(authMiddleware)
client.use(errorLogger)
// Middleware runs in registration order
```

---

## Multiple API Schemas

When consuming multiple APIs, generate separate type files and create separate clients:

```bash
npx openapi-typescript ./api/users.yaml -o ./src/types/users-api.d.ts
npx openapi-typescript ./api/billing.yaml -o ./src/types/billing-api.d.ts
```

```typescript
import createClient from "openapi-fetch"
import type { paths as UserPaths } from "./types/users-api"
import type { paths as BillingPaths } from "./types/billing-api"

const usersClient = createClient<UserPaths>({ baseUrl: "https://users.myapi.dev/" })
const billingClient = createClient<BillingPaths>({ baseUrl: "https://billing.myapi.dev/" })
```

---

## CI Integration

### Generate Types in CI

```yaml
# .github/workflows/typegen.yml
- name: Generate API types
  run: npx openapi-typescript ./api/v1.yaml -o ./src/types/api.d.ts

- name: Check for uncommitted type changes
  run: git diff --exit-code src/types/api.d.ts
```

### Pre-commit Hook

```json
{
  "lint-staged": {
    "api/*.yaml": "npx openapi-typescript ./api/v1.yaml -o ./src/types/api.d.ts && git add src/types/api.d.ts"
  }
}
```

---

## Error Handling Patterns

### Discriminated Response

```typescript
const { data, error, response } = await client.GET("/users/{id}", {
  params: { path: { id: "123" } },
})

if (error) {
  // error is typed based on 4XX/5XX response schemas
  console.error(error.message)
  return
}

// data is typed based on 2XX response schema
console.log(data.name)
```

### With openapi-react-query

```tsx
const { data, error, isLoading } = $api.useQuery("get", "/users/{id}", {
  params: { path: { id: 5 } },
})

// error and data are properly typed from the schema
```

---

## Type Helpers

### Extracting Request/Response Types

```typescript
import type { paths, components } from "./types/api"

// Response type for a specific endpoint
type UserResponse = paths["/users/{id}"]["get"]["responses"]["200"]["content"]["application/json"]

// Component schema type
type User = components["schemas"]["User"]

// Request body type
type CreateUserBody = paths["/users"]["post"]["requestBody"]["content"]["application/json"]
```

---

## tsconfig Recommendations

```json
{
  "compilerOptions": {
    "noUncheckedIndexedAccess": true
  }
}
```

`noUncheckedIndexedAccess` ensures optional parameters in the generated types are properly handled as potentially undefined, catching missing checks at compile time.
