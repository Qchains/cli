# AGENTS.md

## Cursor Cloud specific instructions

This is the **npm CLI** monorepo (v11). It is a pure Node.js project with zero external service dependencies — no databases, Docker, or background services needed.

### Project structure

- Root package: the `npm` CLI itself (`bin/npm-cli.js`, `bin/npx-cli.js`, `lib/commands/*`)
- `workspaces/*`: library packages (`@npmcli/arborist`, `@npmcli/config`, `libnpm*`, etc.)
- `docs/`: documentation (markdown → man pages)
- `smoke-tests/`: end-to-end smoke tests
- `mock-registry/`, `mock-globals/`: test utilities

### Running the dev CLI

Use `node . <command>` from the repo root to run the development version of npm (not the system `npm`). For npx: `node . exec -- <package>`.

### Dependency setup

Run `node scripts/resetdeps.js` to install/reset all dependencies. This removes `node_modules`, restores bundled deps from git, runs `npm install`, and rebuilds.

### Testing

- **Root tests**: `node . run test` (runs `tap` then `posttest` → lint)
- **All workspace tests**: `node . run test --workspaces --include-workspace-root --if-present`
- **Single workspace**: `node . run test -w <workspace-name>`
- **Single test file**: `cd workspaces/<name> && npx tap test/<file>.js`
- **Update snapshots**: `TAP_SNAPSHOT=1 node . run test`

Tests use `@npmcli/mock-registry` (nock-based HTTP mocking) — no real registry calls are made.

### Linting

- `node . run eslint` — runs ESLint (code quality check)
- `node . run lint` — runs ESLint + `template-oss-check` (repo metadata check)

**Note**: `template-oss-check` (the `postlint` hook) may fail in forks because it validates `repository.url` in workspace `package.json` files against the git remote. This is expected — the ESLint check itself (`node . run eslint`) is the meaningful code quality gate.

### Engines

Requires Node.js `^20.17.0 || >=22.9.0`. The VM comes with a compatible version pre-installed.
