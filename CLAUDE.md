# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Nuxt 4 (v4.5.2) application using Vue 3.5 with the Composition API. The project uses Bun as the package manager (bun.lock present).

## Commands

```bash
bun install       # Install dependencies
bun run dev      # Start dev server on http://localhost:3000
bun run build    # Build for production
bun run preview  # Preview production build locally
bun run generate # Generate static site
```

## Architecture

### Directory Structure (Nuxt 4)

Nuxt 4 uses `app/` as the application root (not root-level `pages/`, `components/`):

- `app/` - Main application directory
  - `app.vue` - Root component (currently uses `<NuxtWelcome />`)
  - `assets/` - Processed assets (Vite/webpack)
  - `components/` - Auto-imported Vue components
  - `composables/` - Shared composition functions
  - `layouts/` - Page wrapper components
  - `middleware/` - Route middleware
  - `pages/` - File-based routing
  - `plugins/` - Vue plugins
  - `utils/` - Shared utilities
- `server/` - Nitro server code (API routes, server middleware)
- `public/` - Static files (served at root)
- `nuxt.config.ts` - Main configuration

### Key Nuxt 4 Concepts

- **Auto-imports**: Components, composables, utils are auto-imported - no explicit imports needed
- **File-based routing**: Pages in `app/pages/` auto-generate routes
- **Server routes**: API routes go in `server/api/`
- **Shared code**: Code shared between client/server goes in `shared/`

### Configuration

- `nuxt.config.ts` uses `compatibilityDate: '2025-07-15'` and has devtools enabled
- TypeScript is configured (multiple tsconfig files in `.nuxt/`)

## Documentation

Extensive Vietnamese documentation exists in `nuxt_docs/`:

- `beginner-document/` - Beginner-friendly guide with Vue 3 prerequisites
- `genuine-document/` - Official-style Nuxt documentation (getting-started, guide, API, concepts)

Key docs to reference:
- [nuxt_docs/beginner-document/README.md](nuxt_docs/beginner-document/README.md) - Learning path
- [nuxt_docs/genuine-document/guide/4-directory-structure.md](nuxt_docs/genuine-document/guide/4-directory-structure.md) - Directory structure
- [nuxt_docs/beginner-document/02-core-concepts/01-auto-imports.md](nuxt_docs/beginner-document/02-core-concepts/01-auto-imports.md) - Auto-imports
