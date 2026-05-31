# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A Claude Code automation system for solo founders, organized into **6 role-based folders** that replace a full team. Each folder is a self-contained workflow domain with its own slash commands, hooks, and tooling.

| Folder | Role It Replaces | Primary Command |
|--------|-----------------|-----------------|
| `content/` | Content manager | `claude "/trends"` |
| `code/` | Senior developer | `claude "Build [feature]"` |
| `deploy/` | DevOps engineer | `npx vercel deploy --prod` |
| `research/` | Market analyst | `claude "Research [topic]"` |
| `design/` | Product designer | `claude "/new-series"` |
| `automate/` | Operations manager | Hooks in `settings.json` |

## Stack

- **Language:** TypeScript
- **Deployment:** Vercel (`npx vercel deploy --prod --yes`)
- **Database:** Supabase with Row Level Security (RLS)
- **Testing:** Playwright (E2E), Vitest (unit)
- **Linting/Formatting:** ESLint + Prettier (auto-run via hooks)

## Key Commands

```bash
# Run E2E tests
npx playwright test

# Run unit tests
npx vitest run

# Deploy to production
npx vercel deploy --prod --yes

# Check deployment logs
npx vercel inspect <url> --logs

# Manage environment variables
npx vercel env ls production
```

## Slash Commands

```bash
claude "/trends"                          # Pull trending topics before creating content
claude "/new-series"                      # Generate a new carousel series
claude "/soundeffects --video <file.mp4>" # Add sound effects to a video
```

## Commit Convention

Every commit uses a sequential ID prefix: `NM-001`, `NM-002`, etc. Never skip or reuse an ID. Always check the latest commit to determine the next number before committing.

## Automate Folder — Hooks

The `automate/` folder manages hooks defined in `settings.json`. The current hook configuration runs ESLint and Prettier before every file write, and Vitest after every tool use:

```json
{
  "hooks": {
    "PreToolUse": [
      { "tool": "Write", "cmd": "eslint --fix $FILE" },
      { "tool": "Bash", "cmd": "prettier --write" }
    ],
    "PostToolUse": [
      { "cmd": "vitest run" }
    ]
  }
}
```

Do not bypass these hooks. If a hook fails, fix the underlying lint/test issue rather than skipping.

## Design Folder — Key Details

- Brand logos are stored as SVG paths in a registry (47+ logos)
- Carousel exports target **8640×10800px** (8× resolution for Instagram)
- Preview all logos locally: `open http://localhost:8000/logo-preview`
- Colors, typography, and design tokens are maintained in code — not in Figma

## Content Folder — Workflow

Run `/trends` to validate a topic before building a carousel or reel. Batch-produce carousels from a topic bank using `/new-series`. The goal is 10 pieces of content per day with under 30 minutes of manual effort.

## Research Folder — When to Use

Always run a research prompt before starting a new feature, content topic, or product decision. Sources include Google Trends, Hacker News, Reddit, and competitor websites. Decisions should be driven by real data signals, not assumptions.

## MCP Servers

The `automate/` folder configures MCP server connections for browser testing and screenshots. Check `automate/` for the active MCP server list before adding new external tool integrations.
