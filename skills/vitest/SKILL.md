---
name: vitest
description: >
  This skill should be used when the user asks to "write tests", "add unit tests",
  "test this component", "test this function", "mocking strategy", "MSW",
  "behavior-based testing", "test patterns", or when writing or reviewing tests.
---

# Vitest Conventions

Pattern enforcement for unit and integration testing. Tests describe behavior, not implementation.

## Rule 1: Test Behavior, Not Implementation

Name tests by what the user or system observes, not by internal method names.

```typescript
// Good: describes observable behavior
it('displays error message when form submitted with empty email', async () => {
  render(<LoginForm />)
  await userEvent.click(screen.getByRole('button', { name: 'Sign in' }))
  expect(screen.getByText('Email is required')).toBeVisible()
})

// Bad: tests implementation details
it('calls setError when validateEmail returns false', () => { ... })
```

- **Arrange → Act → Assert (AAA)** structure in every test
- One assertion per logical behavior (multiple `expect` calls are fine when asserting one outcome)
- Test names answer: "What should happen when...?"

## Rule 2: Prefer Real Dependencies Over Mocks

```
Real code > Fake implementations > Spies > vi.mock
```

- Use real implementations whenever possible
- Use MSW for HTTP API mocking (intercepts at the network level, not the module level)
- Use `vi.fn()` for callbacks passed as props
- Use `vi.spyOn()` when verifying a specific call without replacing behavior
- **Avoid `vi.mock()` for module mocking** — it couples tests to import paths and internal module structure. Exceptions: browser APIs, timers, or third-party modules with side effects.

### MSW for API Mocking

```typescript
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer(
  http.get('/api/users', () => {
    return HttpResponse.json([
      { id: '1', name: 'Alice' },
      { id: '2', name: 'Bob' },
    ])
  }),
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

it('renders user list from API', async () => {
  render(<UserList />)
  expect(await screen.findByText('Alice')).toBeVisible()
  expect(screen.getByText('Bob')).toBeVisible()
})

// Override for error case
it('shows error when API fails', async () => {
  server.use(
    http.get('/api/users', () => {
      return new HttpResponse(null, { status: 500 })
    }),
  )
  render(<UserList />)
  expect(await screen.findByText(/error/i)).toBeVisible()
})
```

## Rule 3: Testing Library Conventions

For React component tests, use `@testing-library/react`:

- **Query by role** (`getByRole`) as the primary selector — matches how assistive tech sees the page
- **Query by text** (`getByText`) for visible content
- **Query by label** (`getByLabelText`) for form inputs
- **Avoid `getByTestId`** — use only when no semantic query is possible
- **Use `userEvent` over `fireEvent`** — `userEvent` simulates real user interactions (focus, keyboard, pointer)

```typescript
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'

it('toggles dropdown on click', async () => {
  render(<Dropdown options={['A', 'B', 'C']} />)

  await userEvent.click(screen.getByRole('button', { name: 'Select' }))

  expect(screen.getByRole('listbox')).toBeVisible()
  expect(screen.getAllByRole('option')).toHaveLength(3)
})
```

## Rule 4: Test File Organization

```
src/
├── components/
│   ├── UserCard.tsx
│   └── UserCard.test.tsx      # colocated test
├── hooks/
│   ├── useDebounce.ts
│   └── useDebounce.test.ts
└── utils/
    ├── format.ts
    └── format.test.ts
```

- Colocate test files next to source files
- Name pattern: `{source}.test.{ts,tsx}`
- Use `describe` blocks to group related behaviors, not to mirror class structure

## Rule 5: Setup and Cleanup

```typescript
// Global test setup (vitest.setup.ts)
import '@testing-library/jest-dom/vitest'

// Per-test cleanup is automatic with Testing Library
// Use beforeEach only for shared state setup

beforeEach(() => {
  vi.clearAllMocks()
})
```

- **Do not share state between tests** — each test must set up its own context
- Use `beforeEach` for mock reset, not for building shared fixtures
- Prefer factory functions over shared `let` variables for test data

```typescript
// Good: factory function
function createUser(overrides?: Partial<User>): User {
  return { id: '1', name: 'Alice', email: 'alice@test.com', ...overrides }
}

it('displays user name', () => {
  render(<UserCard user={createUser({ name: 'Bob' })} />)
  expect(screen.getByText('Bob')).toBeVisible()
})
```

## Rule 6: Async Testing

- Use `findBy*` queries for async content (they wait with retry)
- Use `waitFor` only when no `findBy*` query exists for the assertion
- Never use arbitrary `setTimeout` or `sleep` in tests

```typescript
// Good: findBy waits automatically
expect(await screen.findByText('Loaded')).toBeVisible()

// Good: waitFor for non-query assertions
await waitFor(() => {
  expect(onSubmit).toHaveBeenCalledWith({ name: 'Alice' })
})
```

## Rule 7: Snapshot Testing — Sparingly

- Avoid snapshot tests for UI components — they are brittle and provide low signal
- Use inline snapshots only for testing serializable data transformations

```typescript
// Acceptable: data transformation
it('formats API response', () => {
  expect(formatUser(rawData)).toMatchInlineSnapshot(`
    {
      "fullName": "Alice Smith",
      "initials": "AS",
    }
  `)
})
```
