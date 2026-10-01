# TeacherCircle — notes

Newest first. Facts that the code and `CONTEXT.md` do not already record.

## 2026-10-02 — #2/#3 shipped to stage; production deploy order

- `11db050`: teaching mode, classes, boards, exams (migration **0027**) plus `/tutors/online`, `/tutors/exam/<exam>[/<subject>]` and `/tutors/<city>/<subject>/class-<n>`. `14e071e`: readability (Inter font, darker `--muted`, minimum 13px).
- **Before merging stage to main, apply `0026` and then `0027` to production Supabase, then `NOTIFY pgrst, 'reload schema';`.** The search and directory code selects the 0027 columns, so without it /search errors and the directory pages come back empty.
- AdSense (`ca-pub-1900408712967344`) is already live through `NEXT_PUBLIC_ADSENSE_CLIENT_ID` (`components/AdSense.tsx`, `/ads.txt`). Don't add the tag by hand; it would load twice.
- Next on the roadmap: #1 "I need a tutor" posts (signed-in posters only, 60-day expiry).

## 2026-10-02 — Growth feature roadmap (owner decisions)

Owner's six growth ideas, with build order decided as "quick wins first":
1. #5 invite on empty results, #6 fee/availability line on city pages, #4 WhatsApp inquiry: **shipped to stage in `ea9ad97`.** WhatsApp chat links are shown only after Connect, because teacher phone numbers are private.
2. Next: #2 online as a listing (`teaching_mode` home/online/both, `/tutors/online/[subject]`) together with #3 class/board/exam fields. Use a **fixed vocabulary**, not free text, so landing-page slugs stay clean, and publish a page only when at least one real teacher matches. One migration for both.
3. Last: #1 "I need a tutor" posts. Decisions: **signed-in users only** may post; teachers reply through in-app messages; posts **expire after 60 days** (the poster can close one earlier), and expired posts leave the sitemap. Never show the poster's contact details publicly. Needs profanity filter, rate limit and admin removal.

Other changes on 2026-10-02: confirmation-email fix (`ea13d17`: resend endpoint, redirect-host logging, docs/03 Step 4b). Root cause of "email links to localhost" is the Supabase Site URL / Redirect URLs setting, which the owner must fix in the dashboard. Header/footer redesign in `a3af2cd`.

## 2026-10-01 — Claim listing, blog, validation, health check on stage

- `a5f4876` claim listing (migration **0026**), `ffc1498` blog (G6), `28486e6` zod validation, `55fc172` CI postgres-start fix, `88e30a9` `/api/health`. Totals: unit 125, system 118, real 48.
- **Production still needs `db/migrations/0026_claim_listing.sql` applied to Supabase** (SQL editor or psql, as in docs/03 Step 2), then `NOTIFY pgrst, 'reload schema';`. Until then the app works but nothing gets claimed, and the admin pages simply show no "Not claimed yet" labels (that RPC call is best effort).
- `db/run-migrations.sh` is not actually safe to re-run: `0001` fails with `relation "users" already exists`, so on an existing local DB apply new migrations by hand. Fresh databases (CI) are fine.
- CI flake (fixed in `55fc172`): on first boot, supabase/postgres can exit once ("Peer authentication failed" in its init psql) and restart. CI now starts postgres alone and waits for it.
- Blog articles: `content/blog/*.ts` + `content/blog/index.ts`. The owner should review the three starter articles' wording.
- Point an uptime monitor at `https://teachercircle.vercel.app/api/health` (200 ok / 503 degraded).

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
