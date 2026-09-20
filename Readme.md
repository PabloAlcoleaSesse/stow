# Stow

A local-first, Notion-flavored note-taking app with a self-hostable sync backend you actually control. Your notes live as plain files on your disk — sync, when you want it, runs on a server you own, not someone else's cloud.

**Status: early development.** The architecture is planned; the code is just getting started. See [Roadmap](#roadmap) below.

---

## Why this exists

Obsidian gives you full ownership of your notes as local files, but no first-party way to sync them without paying for a closed service. Notion gives you rich blocks, databases, and views, but your data lives entirely on someone else's servers with no export path that preserves structure.

Stow aims to take the best of both without the trade-off:

- **Your files stay yours.** Notes are stored as plain markdown on disk, readable and editable outside the app.
- **Notion-style structure, without the lock-in.** Blocks, inline databases, and table/kanban views over your notes — not just flat text files.
- **Sync you can self-host.** Run your own sync server, on your own infrastructure, for yourself or a team. No dependency on a vendor's cloud.
- **AI on your terms.** Bring your own provider — OpenAI-compatible, Anthropic, or a fully local model — or turn it off entirely. Off by default.
- **Built to be extended.** A stable core and (eventually) a plugin system and integration API, so the ecosystem isn't limited to what one team ships.

## Features

### In the current app (local, v1 scope)
- Local vault — a folder of markdown files you own, no proprietary format
- Block-based editor: paragraphs, headings, lists, checklists, code blocks, quotes, slash commands
- `[[wikilinks]]` and a backlinks panel
- Fast full-text search, entirely local
- Tags and simple frontmatter properties
- A basic database/table view over your notes, filterable by tag or property
- Optional AI actions, configurable per provider, disabled unless you turn them on

### Planned (see [Roadmap](#roadmap))
- Self-hostable sync backend with real-time collaboration (CRDT-based)
- A web app, backed by the same sync server, for access from any browser
- Multi-tenant workspaces with roles (owner / editor / viewer) for teams self-hosting one instance
- A sandboxed plugin system (WASM-based)
- An external integration API so other tools (a calendar app, etc.) can read and write your data
- Richer Notion-style views: kanban, calendar, gallery

## Architecture

{ProjectName} is built as a Rust core with a thin, swappable UI layer:

```
vault-model      shared block/CRDT data types
vault-core       local file storage, SQLite index, search, links
editor-frontend  the block editor UI (TypeScript), shared across desktop and web
tauri-shell      desktop app packaging (Tauri)
server           sync backend: auth, workspaces, CRDT relay, storage (planned)
web-app          browser client backed by `server` (planned)
```

Notes are always plain markdown files on disk — the local SQLite index is a disposable, rebuildable cache, never the source of truth. The sync backend, when you choose to run one, speaks the same block model and merges edits with CRDTs, so offline edits converge cleanly instead of overwriting each other.

Design docs and diagrams live in [`/docs`](./docs) *(add this folder as you formalize the plan)*.

## Tech stack

- **Core & backend:** Rust
- **Desktop shell:** [Tauri](https://tauri.app/)
- **Editor UI:** TypeScript, shared between the desktop app and the future web app
- **Local storage:** plain markdown files + SQLite index
- **Sync backend (planned):** axum, Postgres, CRDT sync (evaluating `yrs` / `automerge`)
- **Plugins (planned):** WASM via `wasmtime`

## Getting started

> The app isn't buildable yet — this section will be filled in once the first skeleton lands (see [Roadmap](#roadmap), Phase 0).

Once available, building from source will look roughly like:

```bash
git clone https://github.com/{you}/{ProjectName}.git
cd {ProjectName}
cargo build
```

Prebuilt binaries for macOS, Windows, and Linux will be published once the app reaches a usable state.

## Roadmap

The current focus is the standalone desktop app — no sync, no web client, no plugins yet — so the core editing experience is solid before anything else is layered on.

- [ ] **Phase 0 — Skeleton:** Tauri shell, open a vault folder, plain text edit and save
- [ ] **Phase 1 — Block editor:** headings, lists, checklists, code, quotes, slash commands
- [ ] **Phase 2 — Index & links:** SQLite index, backlinks panel, full-text search
- [ ] **Phase 3 — Organization:** tags, properties, basic database/table view
- [ ] **Phase 4 — Optional AI:** pluggable AI provider, one inline action
- [ ] **Phase 5 — Polish & first release:** themes, shortcuts, graph view, installers

Sync backend, web app, multi-tenant workspaces, plugin system, and the external integration API are planned for after the desktop app stands on its own. See the design docs for the longer-term architecture.

## Contributing

Not yet open for contributions in a structured way — the core architecture is still being nailed down. Once Phase 0 lands, this section will cover setting up a dev environment, coding conventions, and how to propose changes. Star/watch the repo if you want to follow along.

## License

*To be decided.* Client-side code (editor, desktop app) is expected to use a permissive license (MIT or Apache-2.0); the sync backend is likely to use a copyleft or source-available license (AGPL-3.0 or similar) to prevent unmodified commercial re-hosting. This section will be finalized before the first public release.

## Acknowledgments

Inspired by [Obsidian](https://obsidian.md/)'s local-first file ownership and [Notion](https://www.notion.so/)'s flexible block/database model — built to combine both without requiring a vendor's cloud.
