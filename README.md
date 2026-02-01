# sonsu-cc-marketplace

Personal collection of Claude Code plugins — frontend pattern enforcement, testing, accessibility, data fetching, and workflow skills.

## Install

```bash
claude plugins install sonsu-lee/sonsu-cc-marketplace
```

Install a specific plugin:

```bash
claude plugins install sonsu-lee/sonsu-cc-marketplace/frontend-skills
claude plugins install sonsu-lee/sonsu-cc-marketplace/testing-skills
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

# Manus-style persistent markdown planning (context recovery across sessions)
claude plugins install OthmanAdi/planning-with-files
```

## Structure

```
sonsu-cc-marketplace/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace manifest
├── frontend-skills/              # Plugin
│   ├── .claude-plugin/plugin.json
│   └── skills/ (react, nextjs, typescript)
├── data-fetching-skills/         # Plugin
│   ├── .claude-plugin/plugin.json
│   └── skills/ (tanstack-query, openapi-typescript)
├── testing-skills/               # Plugin
│   ├── .claude-plugin/plugin.json
│   └── skills/ (vitest, storybook)
├── accessibility-skills/         # Plugin
│   ├── .claude-plugin/plugin.json
│   └── skills/ (accessibility)
├── workflow-skills/              # Plugin
│   ├── .claude-plugin/plugin.json
│   └── skills/ (technical-writing, translation-refinement)
└── mcp/                          # MCP reference configs
```

## Plugins

| Plugin | Skills | Description |
|--------|--------|-------------|
| **frontend-skills** | react, nextjs, typescript | React 18/19, Next.js App Router, TypeScript strict conventions |
| **data-fetching-skills** | tanstack-query, openapi-typescript | TanStack Query patterns, openapi-typescript + openapi-fetch |
| **testing-skills** | vitest, storybook | Vitest behavior-based testing, Storybook CSF 3 |
| **accessibility-skills** | accessibility | Semantic HTML, keyboard patterns, ARIA, React Aria |
| **workflow-skills** | technical-writing, translation-refinement | Technical articles, document translation |

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
claude mcp add next-devtools -- npx -y next-devtools-mcp@latest

# Storybook (requires running Storybook dev server, per-project scope)
claude mcp add storybook-mcp --transport http http://localhost:6006/mcp --scope project
```

> **Note:** Storybook MCP requires `@storybook/addon-mcp` installed in your project and a running Storybook dev server. See [addon docs](https://storybook.js.org/addons/@storybook/addon-mcp).
