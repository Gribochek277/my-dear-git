> Sanitized mirror of Forgejo `serhii/my-dear-git`. Source code is not published here.
>
> Commit texts: `commits/`. Need the code? Email: sergeyalpatov1@gmail.com
> Source: Forgejo `serhii/my-dear-git` | Synced: 2026-10-16T03:11:03Z

---

# My Dear Git

Desktop Git GUI client built with Electron + React. Feature parity with SourceTree (minus Mercurial), with a built-in MCP server and Worktree Manager.

## Status

- Main branch is hosted in GitLab: `http://100.110.77.11/serhii/my-dear-git`
- CI is configured through `.gitlab-ci.yml`

## Features

- Desktop Git GUI built with Electron + React
- Terminal UI (`my-dear-git tui`) sharing the same stores and Git service
- Changes view with status, staging, diff, and commit workflow
- History view with graph, commit details, cherry-pick, and branch filtering
- Branch, tag, stash, and remote operations from the UI
- Resizable workspace panels
- Built-in MCP server support

## Requirements

- Node.js 22+
- Git installed and available in `PATH`

## Install dependencies

```bash
npm install
```

## Development (hot reload)

```bash
npm run dev
```

Launches Electron with the Vite dev server. Renderer changes apply without restarting.
Electron DevTools open automatically only in this mode.

`npm run dev` now selects the platform-specific dev entry automatically:

- Linux: `npm run dev:linux`
- macOS: `npm run dev:mac`
- Windows: `npm run dev:win`

If `npm run dev` launches the wrong platform build, your `node_modules/electron` runtime was installed for another OS
(for example, Windows Electron copied into Linux). Reinstall dependencies for the current platform:

```bash
rm -rf node_modules/electron
npm install
```

## TUI mode (terminal interface)

My Dear Git also runs in a terminal. It needs **Node.js 22+** (ink 7 is ESM-only)
and an interactive terminal:

```bash
npm ci
npm run build:tui
node dist/tui/cli.mjs tui --repo ~/projects/my-dear-git
# or, after `npm link`:
my-dear-git tui
```

`npm run dev:tui` builds and launches in one step. The installed app runs the
same command: the Electron launcher hands the `tui` subcommand to the bundled
client before it opens a window, so `my-dear-git tui --repo <path>` works from
the packaged AppImage as well (rebuild and reinstall with `make install` to pick
this up in an existing install). The TUI mirrors the GUI's
layout and chrome, is keyboard-first with full mouse support (click,
double-click, wheel, drag-resize), reuses the GUI's Zustand stores and the
shared Git service, and stores its settings in the same `settings.json` the GUI
uses.

It is **monochrome by default** — state reads through weight, glyphs and inverse
video, so it works on any terminal theme. Colour is an opt-in with `--color` or
`MYDEARGIT_TUI_COLOR=1`:

```bash
my-dear-git tui --color
MYDEARGIT_TUI_COLOR=1 my-dear-git tui
```

Full guide, including the layout, keybindings, mouse actions, file locations,
architecture and limitations: [docs/tui.md](docs/tui.md).

## Tests

```bash
npm test                # single run
npm run test:watch      # watch mode
npm run test:coverage   # with coverage report
```

Targeted examples:

```bash
npm test -- --run tests/unit/renderer/Toast.test.tsx
npm test -- --run tests/unit/renderer/repoSync.test.ts
```

## Production build

```bash
npm run build
make build
```

Output: `dist/main/`, `dist/preload/`, `dist/renderer/`, and the terminal bundle
`dist/tui/`.

## Package installer (.exe / .AppImage)

```bash
npm run package
```

Output in `dist/packages/`.

## Final packages by platform

Supported package targets from `electron-builder.yml`:

| Platform | Architectures | Output formats | Command |
|---|---|---|---|
| Windows | `x64` | `nsis` installer (`.exe`) | `npm run package:win` |
| Linux | `x64`, `arm64` | `deb`, `AppImage` | `npm run package:linux` |
| macOS | `x64`, `arm64` | `dmg` | `npm run package:mac` |

Build commands:

```bash
npm run package:win
npm run package:linux
npm run package:mac
make package-linux
make package-linux-appimage
```

Build all configured targets for the current host environment:

```bash
npm run package
```

Notes:

- `npm run build` creates the app bundles in `dist/main/`, `dist/preload/`, `dist/renderer/` and the terminal bundle in `dist/tui/`.
- `npm run package*` first runs `npm run build`, then packages installers/artifacts with `electron-builder`.
- Windows local packaging uses `-c.win.signAndEditExecutable=false` to avoid `rcedit`/`winCodeSign` extraction failures on systems where symlink creation is restricted.
- Cross-building is not guaranteed for every target on every host OS. The most reliable approach is:
  - build Windows packages on Windows
  - build Linux packages on Linux
  - build macOS packages on macOS
- Windows note: if you want full executable resource editing/signing later, enable Windows Developer Mode or run the terminal elevated, then remove the `-c.win.signAndEditExecutable=false` override.
- Final packaged artifacts are written to `dist/packages/`.

## Linux install via Makefile

For Arch/Linux, you can install the built `AppImage` into the system with a desktop entry:

```bash
make install
```

This will:

- build the Linux `AppImage`
- install it into `/opt/my-dear-git/`
- create `/usr/local/bin/my-dear-git`
- install the icon into `/usr/local/share/pixmaps/`
- create `/usr/local/share/applications/my-dear-git.desktop`

Uninstall:

```bash
make uninstall
```

## Type checking

```bash
npm run typecheck
```

Note: if `npm run typecheck` fails, verify whether the issue is in the renderer changes you are working on or in existing `src/main` typing debt.

## GitLab CI

The GitLab pipeline runs these stages:

- install dependencies with `npm ci`
- run renderer and main type checks
- run unit tests
- build the Electron app bundles

Pipeline definition:

```bash
.gitlab-ci.yml
```

## Repository

- GitLab: `http://100.110.77.11/serhii/my-dear-git`
- Default branch: `main`

## Tech stack

| Layer | Technology |
|---|---|
| Shell | Electron 42 |
| UI | React 19 + Tailwind CSS 4 |
| State | Zustand 5 |
| Git | simple-git 3 |
| Build | electron-vite 5 / Vite 7 |
| Tests | Vitest 4 + Testing Library |
| Language | TypeScript 6 |
