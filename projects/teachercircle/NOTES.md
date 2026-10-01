# TeacherCircle — notes

Newest first. Facts that the code and `CONTEXT.md` do not already record.

## 2026-10-01 — Error sanitizing and CI shipped to stage

- `e71d0a6`: routes return `publicErrorMessage(err, fallback)` (lib/db.ts) instead of raw PostgREST text; `GoTrueError` in lib/gotrue.ts. docs/05 findings #1 and #11 are closed.
- `8f2eda3`: `.github/workflows/ci.yml` runs all three test tiers on push/PR to `stage` and `main`. First run is green (about 51 s checks, 2 m 46 s real stack). Pushing workflow files needs the gh token's `workflow` scope, which was added on this date.
- The owner merges through a "Stage ---> Main" PR (#7 at this time).

## 2026-10-01 — Running the full stack locally on Windows

- Node is now **24.19.0** (`OpenJS.NodeJS.LTS`); Node 22 was replaced. All tiers pass on Windows: unit 90/90, system 105/105, real 35/35. Commit `7d4f1fe` on `stage` made the system runners Windows-safe and made Tier 3 clear `signup:`/`login:` rate-limit rows before each signup/login (otherwise the 7th signup returns 429 and the runner crashes).
- Branch flow: work is pushed to `stage`; the owner merges `stage` → `main` manually. Vercel builds a preview for every `stage` push (GitHub status context `Vercel`).
- Docker Desktop here is 20.10.11 with Compose 2.2.1, which rejects the top-level `name:` in `docker-compose.yml`. Workaround, kept out of git through `.git/info/exclude`: `docker-compose.local.yml` = the same file without `name:`. Export `COMPOSE_FILE=docker-compose.local.yml COMPOSE_PROJECT_NAME=teachercircle` so `db/*.sh` and `test:system:real` use it. Updating Docker Desktop removes the need for this.
- `quay.io/minio/minio` now returns "unauthorized" (no longer publicly pullable). MinIO is unused, so start the services explicitly: `docker compose up -d --build postgres postgrest gotrue meilisearch redis app`.
- Local `.env` has generated secrets and is gitignored. The compose Postgres image is `supabase/postgres:15.8.1.060`, not `postgres:16-alpine` as `CONTEXT.md` says.

## 2026-10-01 — Windows workstation setup

- Owner's GitHub account: `dharmsahu-hash`. The GitHub CLI is installed at `C:\Program Files\GitHub CLI\gh.exe` and signed in with `repo` scope. Git uses it as its credential helper.
- Workspace root: `D:\projects\Claude_AI_Project`. The owner wants all AI work kept there in readable subfolders. Repos are cloned under `repos\`: `repos\teachercircle` and `repos\KnowledgeWala_AI_CONTEXT`. Both set the repo-local git identity to `dharmsahu-hash <331318054+dharmsahu-hash@users.noreply.github.com>`. The machine's global identity is `dknitk`.
- The README's run path (`/Users/dharmendrakumar/Desktop/...`) is from the owner's Mac. On Windows, use the clone path above. The shell scripts in `db/` need Git Bash or WSL.
- Node 12.14.1 was uninstalled. Node 22.23.2 is installed (winget `OpenJS.NodeJS.22`). On Node 22, `npm ci`, `npx tsc --noEmit` and `npm run build` pass.
- **`test:unit` needs Node 24, not 22.** The tests call `t.mock.module(url, { exports: {...} })`. Node 22 only knows the older `namedExports` option, so 19 of 90 tests fail on 22 with "does not provide an export named …". All 90 pass on Node 24.21.0 (checked with `npx -p node@24`). The Dockerfile uses `node:20-alpine`, which only matters for running the app, not the tests.
- **`test:system` fails on Windows.** `tests/system/run.mjs` line 91 calls `spawn("npx", ...)`, which gives `ENOENT` on Windows because the command is `npx.cmd`. It needs `shell: true`, or `npx.cmd` when `process.platform === "win32"`. This is a portability bug in the test runner, not in the app.
- Docker Desktop is not yet confirmed on this machine. `test:system:real` and local dev need it.
- Initial review: the repo is at `bc24a7d` on `main` (PR #4, the signup confirmation redirect fix). No CI workflow exists (`.github/` is absent). This matches the open gap in `CONTEXT.md`.
