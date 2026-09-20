# Contributing to Stow

Thanks for your interest in contributing. This project is in early development, so the process below is intentionally lightweight — expect it to firm up as the codebase and community grow.

## Before you start

Because the core architecture is still being established, please **open an issue or discussion before starting significant work**, especially for anything that isn't a small, obvious fix. This avoids duplicated effort and mismatched expectations about a part of the design that might still be in flux.

The project is deliberately scoped right now to **the standalone desktop app only** — no sync backend, no web app, no multi-tenancy, no plugin system yet (see the Roadmap in [README.md](./README.md)). Contributions in those areas will likely be deferred or declined until the relevant phase starts, not because they're unwelcome, but because building them before the core app is solid is a known risk for this project.

## Ways to contribute

- **Bug reports** — even for the earliest builds, these are valuable. Include your OS, what you did, what you expected, and what happened.
- **Small fixes** — typos, clippy/lint warnings, obvious bugs — feel free to open a PR directly without an issue first.
- **Features on the current roadmap phase** — check open issues/the project board (once set up) for what's actively being worked on before starting something large.
- **Documentation** — README clarity, code comments, architecture docs — always welcome.
- **Design/architecture feedback** — opened as a GitHub Discussion, especially useful before the sync/CRDT layer is implemented.

## Development setup

> This section will be filled in once the initial project skeleton lands. In the meantime:

Prerequisites (expected):
- Rust (stable toolchain, via [rustup](https://rustup.rs/))
- Node.js (for the editor frontend)
- [Tauri prerequisites](https://tauri.app/start/prerequisites/) for your OS

Once the skeleton exists:
```bash
git clone https://github.com/your-username/stow.git
cd stow
cargo build
```

## Making a change

1. Fork the repo and create a branch off `main`: `git checkout -b your-change-name`
2. Make your change, keeping commits scoped and using [conventional commit](https://www.conventionalcommits.org/) prefixes where reasonable (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`)
3. Run formatting, lint, and tests locally before opening a PR:
   ```bash
   cargo fmt
   cargo clippy --all-targets -- -D warnings
   cargo test
   ```
   (Frontend equivalents will be documented here once that tooling is set up.)
4. Open a PR against `main` with a clear description of *what* changed and *why*. Link the issue it addresses if there is one.
5. Be responsive to review feedback — this is a small project early on, so review turnaround may be slower than on larger repos; please be patient.

## Code style

- **Rust:** standard `rustfmt` + `clippy`-clean. Prefer explicit error handling (`Result`, `thiserror`) over `unwrap()`/`panic!()` outside tests.
- **TypeScript:** match the repo's ESLint/Prettier config once added.
- Keep the editor frontend platform-agnostic where possible — it's meant to work in both the desktop shell and a future web client, so avoid hardcoding Tauri-only APIs directly into shared components.
- The vault (plain markdown files on disk) is always the source of truth. Any change touching the local index or file I/O should preserve that — the index must be safely rebuildable from files at any time, with zero risk to the files themselves.

## Commit / PR expectations

- One logical change per PR where reasonable — easier to review, easier to revert if needed.
- Add or update tests for behavior you change, where a test setup exists for that area.
- Update relevant docs (README, code comments, `/docs`) in the same PR as the code change, not as a follow-up.

## Reporting bugs / requesting features

Please use GitHub Issues. For bugs, include:
- OS and version
- Steps to reproduce
- Expected vs. actual behavior
- Logs or screenshots if relevant

For feature requests, check the Roadmap in the README first — if it's already planned for a later phase, a comment on the relevant tracking issue is more useful than a new one.

## Using AI coding agents

If you use an AI coding assistant (Claude Code, Copilot, etc.) to help with a contribution, that's fine — please review its output yourself before opening a PR, same as you would your own code. See [AGENTS.md](./AGENTS.md) for the conventions we ask agentic tools to follow in this repo; human-authored PRs should meet the same bar.

## Code of conduct

Be respectful, assume good faith, and keep disagreements about code and architecture — not people. A formal Code of Conduct document will be added as the community grows; until then, this paragraph is the standard.

## License

By contributing, you agree that your contributions will be licensed under the project's license once finalized (see the License section in [README.md](./README.md)). If a Contributor License Agreement becomes necessary, it will be added here with advance notice.

## Questions

Open a GitHub Discussion, or comment on a relevant issue. There's no chat/Discord yet — this section will be updated if that changes.
