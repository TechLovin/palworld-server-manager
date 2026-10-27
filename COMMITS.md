# Commit Summaries

One entry per commit, newest first.

## Upgrade electron-builder 25 → 26 — 2026-10-27

- `electron-builder` 25.1.8 → 26.18.0. `npm audit` now reports 0 vulnerabilities.
- Config: none of 26's breaking changes apply (signing options moved, macOS notarization and publishing changes), and the schema validates as-is.
- Packaging fix: 26 skips a `node_modules` folder at the top of any copied folder, including `extraResources`. The bundled server therefore shipped without its dependencies (`Cannot find module 'next'`). Added a second `extraResources` entry that copies `dist-standalone/node_modules` → `app/node_modules`.
- 26 also no longer hits the winCodeSign symlink error, so `npm run dist:win` works on this machine without Developer Mode or admin rights.
- Verified: built the NSIS installer and portable exe (icon and version info embedded). The packaged app tree and installer payload match `dist-standalone` (3,177 files). Launched the portable exe in isolation: server, background tasks, preload/IPC and portable data folder all work. The installer itself was not run.

## Upgrade Electron 37 → 45 — 2026-10-27

- `electron` 37.10.3 → 45.1.0 (Chromium 156, Node 24.22.1).
- `electron/main.js`:
  - Dropped `openAsHidden` / `wasOpenedAsHidden`, which Electron 44 removed. They only ever worked on macOS 12 and older, and `--hidden` still handles start-to-tray on Windows and Linux.
  - The folder and zip pickers now remember the last folder used during the session. Since Electron 43 they would otherwise always open in Downloads.
- `preload.js`: no change needed. It only uses `electron` and `process.platform`, both still available in the sandboxed preload.
- Install note: since Electron 42, `npm install` no longer downloads the Electron binary. `npm run dev` fetches it on first run, or run `npx install-electron --no`.
- Vulnerabilities: 14 → 12 (what remains is electron-builder and its dependencies).
- Verified: packaged with `electron-builder --dir` and launched the desktop app with isolated data. The window, the HttpOnly admin cookie, the preload bridge, all IPC getters, the server restart via port change, and clean quit all work.

## Upgrade Next.js 14 → 16 and React 18 → 19 — 2026-10-27

- `next` 14.2.35 → 16.4.3, `react`/`react-dom` 18.3.1 → 19.3.1. This also brings in a fixed `postcss`.
- Route handlers: Next 15 made route `params` async. All 74 handlers that use them (45 files) now do `const params = await ctx.params;`.
- `next.config.js`: `experimental.serverComponentsExternalPackages` → `serverExternalPackages`, and `outputFileTracingExcludes` is now top-level. Removed the `eslint` option, which Next 16 dropped.
- `middleware.js` → `proxy.js` (Next 16 rename). The logic is unchanged.
- Scripts: `dev:next` and `build:next` pass `--webpack` to keep the custom webpack config (Next 16 defaults to Turbopack). Removed `lint`: `next lint` is gone in 16 and ESLint was never installed.
- React 19: one callback ref (`app/remote/page.jsx`) now uses a block body. No other affected APIs are used.
- Vulnerabilities: 16 → 14. No Next.js/PostCSS findings remain.
- Verified: clean build. Ran the packaged standalone server under Electron's Node and checked every page, the proxy redirect, the async-params API routes, and the client UI in a browser with no console errors.

## Upgrade adm-zip to 0.6.1 and guard manual zip extraction — 2026-10-27

- `adm-zip` 0.5.18 → 0.6.1. No API changes affect our usage (all 10 call sites checked).
- New 0.6 behavior: a zip with duplicate entry names is rejected when opened, and `extractAllTo` (backup restore, save import, SteamCMD) refuses to write through symlinks in the target folder.
- The mod, PalSchema and UE4SS imports unpack files with their own code rather than `extractAllTo`, so the 0.6.1 fixes didn't cover them. They now use a new `safeEntryPath` (`lib/paths.js`), which rejects entries like `../../x.dll` that would write outside the target folder.
- Vulnerabilities: 17 → 16.
- Verified: smoke test of each adm-zip call pattern and a crafted escaping UE4SS zip; `npm run build:next` succeeds.

## Apply non-breaking npm audit fixes — 2026-10-27

- Ran `npm audit fix` (no `--force`): only `package-lock.json` changed; 16 indirect dependency updates (e.g. `shell-quote`, `undici`, `axios`, `js-yaml`).
- Vulnerabilities: 33 → 17 (2 critical, 15 high).
- Remaining fixes all need major upgrades and are deferred: `next` 14→16, `electron` 37→45, `electron-builder` 25→26, `adm-zip` 0.5→0.6, `postcss` (via Next).
- Verified: `npm run build:next` succeeds.
