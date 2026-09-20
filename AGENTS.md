# AGENTS.md

Instructions for AI coding agents (Claude Code, Codex, Copilot Workspace, etc.) working in this repository. Human contributors should read [CONTRIBUTING.md](./CONTRIBUTING.md) instead — this file is scoped to automated/agentic work.

## Project summary

Stow is a local-first, Notion-flavored note-taking app with a self-hostable sync backend, built in Rust with a Tauri desktop shell and a TypeScript editor frontend. Full context: [README.md](./README.md).

**Current phase:** desktop app only (see Roadmap in README). Do not add sync-server, web-app, multi-tenant, or plugin-system code unless a task explicitly asks for it — those are deliberately out of scope right now, and scope creep here has already been flagged as a project risk once. If a task seems to require touching one of those areas, stop and ask rather than assuming it's now in scope.

## Repo structure

```
vault-model/       shared block/CRDT data types (Rust crate, no external deps beyond serde etc.)
vault-core/         local file storage, SQLite index, search, link/tag extraction (Rust crate)
editor-frontend/    block editor UI (TypeScript) — shared by desktop app and future web app
tauri-shell/        desktop app packaging, Tauri commands bridging frontend <-> vault-core
docs/               architecture docs and diagrams
```
*(Update this tree as the repo actually takes shape — this reflects the planned layout, not necessarily what exists yet.)*

## Build, test, lint commands

> Fill these in as the toolchain is set up. Until then, agents should check for a `Cargo.toml` / `package.json` / `justfile` at the repo root and infer the real commands from there rather than guessing.

Expected shape once scaffolded:
- Rust crates: `cargo build`, `cargo test`, `cargo clippy --all-targets -- -D warnings`, `cargo fmt --check`
- Frontend: `npm install`, `npm run build`, `npm run lint`, `npm run typecheck`
- Full app: `cargo tauri dev` / `cargo tauri build`

Agents should always run the relevant build/test/lint commands for any code they touch before considering a task done, and should not mark a task complete if a command fails or doesn't exist yet — surface that instead of skipping it silently.

## Code conventions

**Rust**
- Standard `rustfmt` formatting, no custom config unless one is added to the repo.
- `clippy` clean — don't suppress lints with `#[allow(...)]` without a comment explaining why.
- Favor explicit error types (`thiserror`) over `unwrap()`/`panic!()` outside of tests and `main`.
- Keep `vault-model` free of I/O — it's pure data types and logic, used by both client and (eventually) server. Don't add file-system or network code there.
- The vault folder on disk is the source of truth; the SQLite index in `vault-core` is a derived, disposable cache. Any change to indexing logic must not risk data loss to the underlying markdown files — when in doubt, write to files first, index second.

**TypeScript / editor-frontend**
- Match whatever linter/formatter config exists in the repo (ESLint/Prettier) once added — don't introduce a second one.
- Keep the editor frontend free of Tauri-specific APIs where possible; it's meant to run in both the desktop webview and, eventually, a plain browser. Isolate platform-specific calls behind a small adapter rather than calling `window.__TAURI__` throughout the codebase.

**Commits**
- Conventional commit style preferred: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`.
- Keep commits scoped to one logical change; don't bundle unrelated refactors with feature work.

## What agents should and shouldn't do autonomously

**Fine to do without asking:**
- Implement a task as scoped, including writing tests for it
- Fix lint/type errors introduced by your own changes
- Update docs/comments that describe code you changed

**Ask before doing:**
- Adding a new external dependency (crate or npm package) — note what it's for and why an existing dependency doesn't cover it
- Changing the on-disk file format or SQLite schema — these have migration implications
- Touching anything under a future `server/` or `web-app/` directory before those phases start
- Broad refactors that touch many files, when the task only asked for a narrow fix

**Never do:**
- Commit secrets, API keys, or `.env` files
- Disable tests to make a build pass rather than fixing the underlying issue
- Introduce telemetry, analytics, or network calls the user didn't ask for — this project's whole premise is local-first and user-controlled data, so silent network activity is a hard no

## Testing expectations

- New logic in `vault-core` (parsing, indexing, link extraction) should have unit tests alongside it.
- Anything touching the markdown-file-to-index pipeline should include a test that verifies re-indexing is idempotent and non-destructive to the source files.
- UI changes in `editor-frontend` should at minimum not break existing component tests once a test setup exists; add tests for new interactive behavior (slash commands, block operations) where practical.

## When unsure

Prefer asking a clarifying question over guessing on: scope boundaries (see "Current phase" above), data format/schema changes, and anything that would add a new runtime dependency users need to install. Getting these wrong is expensive to unwind later; getting a smaller implementation detail wrong is cheap to fix in review.
