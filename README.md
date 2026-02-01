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
