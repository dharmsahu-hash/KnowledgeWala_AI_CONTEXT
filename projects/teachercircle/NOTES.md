# TeacherCircle — notes

Newest first. Facts that the code and `CONTEXT.md` do not already record.

## 2026-10-01 — Windows workstation setup

- Owner's GitHub account: `dharmsahu-hash`. The GitHub CLI is installed at `C:\Program Files\GitHub CLI\gh.exe` and signed in with `repo` scope. Git uses it as its credential helper.
- Workspace root: `D:\projects\Claude_AI_Project`. The owner wants all AI work kept there in readable subfolders. Repos are cloned under `repos\`: `repos\teachercircle` and `repos\KnowledgeWala_AI_CONTEXT`. Both set the repo-local git identity to `dharmsahu-hash <331318054+dharmsahu-hash@users.noreply.github.com>`. The machine's global identity is `dknitk`.
- The README's run path (`/Users/dharmendrakumar/Desktop/...`) is from the owner's Mac. On Windows, use the clone path above. The shell scripts in `db/` need Git Bash or WSL.
- Node 12.14.1 was uninstalled. Node 22.23.2 is installed (winget `OpenJS.NodeJS.22`). On Node 22, `npm ci`, `npx tsc --noEmit` and `npm run build` pass.
- **`test:unit` needs Node 24, not 22.** The tests call `t.mock.module(url, { exports: {...} })`. Node 22 only knows the older `namedExports` option, so 19 of 90 tests fail on 22 with "does not provide an export named …". All 90 pass on Node 24.21.0 (checked with `npx -p node@24`). The Dockerfile uses `node:20-alpine`, which only matters for running the app, not the tests.
- **`test:system` fails on Windows.** `tests/system/run.mjs` line 91 calls `spawn("npx", ...)`, which gives `ENOENT` on Windows because the command is `npx.cmd`. It needs `shell: true`, or `npx.cmd` when `process.platform === "win32"`. This is a portability bug in the test runner, not in the app.
- Docker Desktop is not yet confirmed on this machine. `test:system:real` and local dev need it.
- Initial review: the repo is at `bc24a7d` on `main` (PR #4, the signup confirmation redirect fix). No CI workflow exists (`.github/` is absent). This matches the open gap in `CONTEXT.md`.
