# sonsu-cc-marketplace

Personal collection of Claude Code plugins — skills, commands, and MCP servers.

## Install

```bash
npx skills add sonsu-lee/sonsu-cc-marketplace
```

Install a specific skill:

```bash
npx skills add sonsu-lee/sonsu-cc-marketplace --skill react
```

### External Skills

Additional recommended skills from other repositories:

```bash
# Vercel React & Next.js performance rules (57 rules across 8 categories)
npx skills add vercel-labs/agent-skills --skill react-best-practices

# Karpathy-style coding guidelines (reduce common LLM mistakes)
npx skills add forrestchang/andrej-karpathy-skills

# Playwright CLI + SKILL — official Microsoft, token-efficient E2E testing
npx skills add microsoft/playwright-cli
```

## Structure

```
skills/       # Agent skills (pattern enforcement, workflows)
commands/     # Slash commands
mcp/          # MCP server configurations
```

## Skills

### Pattern Enforcement

| Skill | Description |
|-------|-------------|
| **react** | React 18/19 conventions — Server Components, hooks, composition, state management |
| **typescript** | TypeScript conventions — strict mode, discriminated unions, no enums/namespaces |
| **nextjs** | Next.js App Router — file structure, Server Actions, caching, middleware |
| **tanstack-query** | TanStack Query — query key factories, custom hooks, mutation flow |
| **vitest** | Vitest — behavior-based testing, MSW, Testing Library |
| **storybook** | Storybook — CSF 3, args-driven stories, interaction tests |
| **accessibility** | Web accessibility — semantic HTML, keyboard patterns, ARIA, React Aria |
| **openapi-typescript** | OpenAPI type generation — openapi-typescript, openapi-fetch patterns |

### Workflow

| Skill | Description |
|-------|-------------|
| **technical-writing** | Structured workflow for technical articles with source citations |
| **translation-refinement** | Document translation with recursive naturalness review |

## MCP Servers

Reference configurations in `mcp/`. Copy into your project's `.mcp.json` or use `claude mcp add`.

| Server | Package | Description |
|--------|---------|-------------|
| **react-aria** | `@react-aria/mcp` | React Aria component docs and patterns |
| **next-devtools** | `next-devtools-mcp` | Next.js dev server errors, logs, metadata (requires Next.js 16+) |
| **storybook** | `@storybook/addon-mcp` | Component discovery and story generation (per-project, requires running Storybook) |

### Quick Setup

```bash
# React Aria
claude mcp add react-aria npx @react-aria/mcp@latest

# Next.js DevTools
claude mcp add next-devtools npx -y next-devtools-mcp@latest
```

> **Note:** Storybook MCP runs inside the Storybook dev server (`localhost:6006/mcp`). Install `@storybook/addon-mcp` in projects that use Storybook — see [addon docs](https://storybook.js.org/addons/@storybook/addon-mcp).
