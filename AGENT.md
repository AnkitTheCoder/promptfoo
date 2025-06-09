# AGENT.md

## Build/Test Commands
- `npm run build` - Build TypeScript to dist/ with assets
- `npm run lint` - ESLint on src/ (max 0 warnings)
- `npm test` - Run Jest tests (use `npm test -- --coverage --randomize`)
- `npx jest path/to/test-file` - Run specific test file
- `npm run dev` - Start development server and app
- `npm run tsc` - TypeScript compile check
- `npm run db:generate` - Generate Drizzle migrations

## Architecture
- **Core**: LLM evaluation toolkit with CLI (`promptfoo`/`pf`)
- **Structure**: `src/` (core logic), `test/` (Jest tests), `src/app/` (React frontend), `site/` (docs)
- **Database**: SQLite with Drizzle ORM (`src/database/`)
- **Providers**: 35+ LLM providers in `src/providers/` (OpenAI, Anthropic, etc.)
- **Red Team**: Security testing in `src/redteam/`
- **Workspaces**: Root package + `src/app` + `site`

## Code Style
- **TypeScript**: Strict mode, CommonJS modules, Node.js >=18
- **Imports**: Use `@trivago/prettier-plugin-sort-imports` ordering
- **Format**: Prettier (single quotes, 100 char width, trailing commas)
- **Linting**: ESLint with TypeScript, Jest, and Unicorn plugins
- **Testing**: Jest with `describe`/`it` blocks, mock minimally, always use `--coverage --randomize`
- **Conventions**: Prefer `const`, object shorthand, curly braces required, consistent type imports
