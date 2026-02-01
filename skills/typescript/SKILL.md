---
name: typescript
description: >
  This skill should be used when the user asks to "define types", "create interface",
  "type vs interface", "TypeScript best practices", "strict mode", "no enums",
  "no namespaces", "TypeScript conventions", or when writing or reviewing TypeScript code.
---

# TypeScript Conventions

Pattern enforcement for TypeScript usage. These conventions define how to write type-safe code, which language features to use, and which to avoid.

## Rule 1: `interface` for Objects, `type` for Everything Else

```typescript
// interface — objects, props, public API contracts
interface User {
  id: string
  name: string
  email: string
}

interface ButtonProps {
  variant: 'primary' | 'secondary'
  children: React.ReactNode
  onClick?: () => void
}

// type — unions, intersections, primitives, computed types
type Status = 'idle' | 'loading' | 'success' | 'error'
type ID = string | number
type UserWithRole = User & { role: Role }
type Nullable<T> = T | null
```

**When it could be either**, use `interface`. Switch to `type` when:
- Defining a union or intersection
- Using mapped types, conditional types, or template literals
- Aliasing primitives or tuples
- Declaration merging is explicitly unwanted

## Rule 2: No Runtime TypeScript Syntax

Avoid TypeScript features that emit runtime code. These break compatibility with tools that strip types (esbuild, swc, Bun, Node.js `--strip-types`).

### Banned

| Feature | Replacement |
|---------|-------------|
| `enum` | Union type or `as const` object |
| `namespace` | ES modules (`import`/`export`) |
| `class` parameter properties | Explicit field declarations |
| `import ... = require()` | ES `import` |
| Legacy decorators | TC39 Stage 3 decorators or avoid |

```typescript
// Banned: enum
enum Direction { Up, Down }

// Use instead: union type
type Direction = 'up' | 'down'

// Or: const object (when runtime values needed)
const Direction = { Up: 'up', Down: 'down' } as const
type Direction = typeof Direction[keyof typeof Direction]

// Banned: namespace
namespace Utils { export function parse() {} }

// Use instead: module
export function parse() {}
```

### Why

Node.js 22+ supports TypeScript via `--strip-types` (powered by Amaro/swc), which only strips type annotations — it does not transform runtime syntax. `enum` and `namespace` require code generation, making them incompatible with type-stripping runtimes. The same applies to esbuild's `--loader=ts` and Bun's native TypeScript support.

## Rule 3: Strict Mode Always

Every `tsconfig.json` must include:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true
  }
}
```

- `strict: true` enables `strictNullChecks`, `noImplicitAny`, `strictFunctionTypes`, and more
- `noUncheckedIndexedAccess` forces handling of potentially `undefined` index access
- Never use `@ts-ignore` — use `@ts-expect-error` with a comment explaining why, and only as a last resort

## Rule 4: Inference Over Annotation

Let TypeScript infer types when the inference is obvious and correct. Annotate when it clarifies intent.

```typescript
// Inference is sufficient — no annotation needed
const count = 0
const name = 'hello'
const users = [{ id: 1, name: 'Alice' }]
const doubled = numbers.map(n => n * 2)

// Annotate: function signatures (public API)
function getUser(id: string): Promise<User | null> {
  return db.findOne('users', { id })
}

// Annotate: complex or non-obvious types
const config: AppConfig = loadConfig()
const ref = useRef<HTMLDivElement>(null)
```

## Rule 5: Discriminated Unions for State

```typescript
// Preferred: discriminated union
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error }

// Switch narrows correctly
function render(state: AsyncState<User>) {
  switch (state.status) {
    case 'success': return <Profile user={state.data} />
    case 'error': return <Error message={state.error.message} />
    case 'loading': return <Spinner />
    case 'idle': return null
  }
}
```

Prefer discriminated unions over optional fields or boolean flags for representing states.

## Rule 6: Narrowing and Type Guards

- Use `typeof`, `instanceof`, and `in` for built-in narrowing
- Write custom type guards (`value is Type`) for domain-specific checks
- Never use `as` to cast types unless interfacing with an untyped boundary (e.g., JSON parsing, third-party lib). Even then, prefer `unknown` + validation (Zod, Valibot) over blind casting.

```typescript
// Validation at system boundary
const parsed: unknown = JSON.parse(raw)
const user = userSchema.parse(parsed) // Zod validates and types
```

## Rule 7: Utility Type Patterns

Use built-in utility types over hand-written equivalents:

| Need | Use |
|------|-----|
| Optional all props | `Partial<T>` |
| Required all props | `Required<T>` |
| Pick subset | `Pick<T, 'a' \| 'b'>` |
| Exclude props | `Omit<T, 'a'>` |
| String-keyed map | `Record<string, T>` |
| Unwrap promise | `Awaited<T>` |
| Function return type | `ReturnType<typeof fn>` |

Use `satisfies` to validate a value matches a type while preserving narrower inference:

```typescript
const routes = {
  home: '/',
  about: '/about',
} satisfies Record<string, string>
// routes.home is '/' (not string)
```
