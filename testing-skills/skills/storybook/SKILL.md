---
name: storybook
description: >
  This skill should be used when the user asks to "write a story", "create stories",
  "Storybook patterns", "interaction test in Storybook", "component documentation",
  "CSF format", or when writing or reviewing Storybook stories.
---

# Storybook Conventions

Pattern enforcement for writing component stories. Stories document component states and enable visual testing through interaction.

## Rule 1: CSF 3 Format with `satisfies`

Every story file follows this structure:

```typescript
import type { Meta, StoryObj } from '@storybook/react'
import { Button } from './Button'

const meta = {
  component: Button,
  title: 'Components/Button',
  tags: ['autodocs'],
} satisfies Meta<typeof Button>

export default meta
type Story = StoryObj<typeof meta>

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Click me',
  },
}
```

- Use `satisfies Meta<typeof Button>` for type checking while preserving inference
- Define `type Story = StoryObj<typeof meta>` once per file
- `tags: ['autodocs']` for automatic documentation generation

## Rule 2: Args-Driven Stories

Define component states through `args`, not through custom `render` functions.

```typescript
// Good: args-driven
export const Disabled: Story = {
  args: {
    disabled: true,
    children: 'Disabled Button',
  },
}

// Only use render when composition requires it
export const WithIcon: Story = {
  args: { children: 'Save' },
  render: (args) => (
    <Button {...args}>
      <SaveIcon /> {args.children}
    </Button>
  ),
}
```

- Each story represents one visual state
- Use args for all configurable props — enables controls panel interaction
- Use `render` only when the story needs JSX composition beyond props

## Rule 3: Interaction Tests with Play Functions

Test user behavior directly in stories using `play` functions:

```typescript
import { expect, userEvent, within } from '@storybook/test'

export const FilledForm: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement)

    await userEvent.type(canvas.getByLabelText('Email'), 'user@example.com')
    await userEvent.type(canvas.getByLabelText('Password'), 'secret123')
    await userEvent.click(canvas.getByRole('button', { name: 'Sign in' }))

    await expect(canvas.getByText('Welcome')).toBeInTheDocument()
  },
}

export const ValidationError: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement)

    await userEvent.click(canvas.getByRole('button', { name: 'Sign in' }))

    await expect(canvas.getByText('Email is required')).toBeVisible()
  },
}
```

- Write interaction tests for components with user input (forms, toggles, dropdowns)
- Use `within(canvasElement)` for scoped queries
- Query by role and label, same conventions as Testing Library
- Run with `test-storybook` in CI

## Rule 4: Story Organization

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   └── Button.stories.tsx
```

Story titles follow a hierarchy:

```typescript
// Atomic components
title: 'Components/Button'
title: 'Components/Input'

// Composed patterns
title: 'Patterns/FormField'
title: 'Patterns/DataTable'

// Full pages
title: 'Pages/Dashboard'
```

## Rule 5: Decorators for Context

Apply context providers and layout wrappers through decorators, not inline in stories.

```typescript
// preview.ts — global decorators
const preview: Preview = {
  decorators: [
    (Story) => (
      <ThemeProvider>
        <Story />
      </ThemeProvider>
    ),
  ],
}

// Component-level decorator (in meta)
const meta = {
  component: Modal,
  decorators: [
    (Story) => (
      <div style={{ minHeight: 400 }}>
        <Story />
      </div>
    ),
  ],
} satisfies Meta<typeof Modal>
```

- Global decorators in `preview.ts` for theme, i18n, router providers
- Component-level decorators in `meta` for layout context
- Story-level decorators only when a specific story needs a unique wrapper

## Rule 6: ArgTypes for Controls

Configure controls for interactive documentation:

```typescript
const meta = {
  component: Button,
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'outline'],
    },
    size: {
      control: 'radio',
      options: ['sm', 'md', 'lg'],
    },
    onClick: { action: 'clicked' },
  },
} satisfies Meta<typeof Button>
```

- Define `argTypes` in `meta` for controls shared across all stories
- Use `action` for callback props to log interactions in the Actions panel
- Set `table.disable: true` for internal props that shouldn't appear in docs
