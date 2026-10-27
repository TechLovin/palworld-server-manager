# Commit Summaries

One entry per commit, newest first.

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
