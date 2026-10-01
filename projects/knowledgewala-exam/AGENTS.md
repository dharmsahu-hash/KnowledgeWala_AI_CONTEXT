# KnowledgeWala Exam — agent instructions

Short operating contract for every AI agent (Claude Code subagents in `.claude/agents/`, or any other tool) working in this repo. Design detail: `docs/design/`. BRD: https://claude.ai/code/artifact/b3e2cede-641a-4d52-8897-d4b21b467184

## What this is

Free (MVP) mock-exam and learning web app for NEET, JEE Main, AWS Cloud Practitioner and Java OCP. Users: students, parents, professionals. Staff: authors, reviewers, admin (owner). Stack: SvelteKit on Cloudflare Pages/Workers, Supabase (Postgres + Auth), Cloudflare R2. See `docs/design/01-system-design.md`.

## Human approval gates (never bypass)

| Gate | Needs owner approval before |
|---|---|
| G2 | any application code beyond scaffolding |
| G3 | declaring MVP build complete |
| G4 | first production deploy |
| G5 | every later production deploy (GitHub Environment `production`) |

Agents never: merge to `main`, approve the `production` environment, push to a remote the owner has not set up, hold the `reviewer` role, or publish content.

## Invariants

- **RLS is the authorization layer.** UI checks are convenience only.
- **Students never read `questions`.** Papers via `get_test_paper()` (no answers), solutions via `get_attempt_review()` after submit.
- **Status changes go through functions** (`submit_for_review`, `review_item`, `publish_test`). There is no client column grant on `status`, `role`, `score`.
- **Reviewer ≠ author**, enforced in `review_item()`. AI-drafted rows carry `ai_assisted = true` and start as `draft`.
- **Helpers used in policies are `SECURITY DEFINER` with `search_path = public, pg_temp`**, and are granted to `anon` and `authenticated` (a missing grant breaks anon reads with "permission denied for function").
- **Supabase grants EXECUTE to anon/authenticated by default** — every migration that adds a function must revoke from `public, anon, authenticated` and grant explicitly.
- **Migrations are append-only** (`supabase/migrations/NNNN_name.sql`) and end with `notify pgrst, 'reload schema';`.
- **No pgcrypto calls** in `public` functions (it lives in `extensions` on Supabase). Use built-ins (`gen_random_uuid`, `sha256`).
- **Never return raw Postgres error text** to the browser; map `raise exception '<code>'` codes to messages.
- **Budgets:** first-load JS ≤ 200 KB gz, test paper ≤ 100 KB, image ≤ 50 KB WebP. CI fails above them.
- **Content legality:** `source` must be `original`, `licensed:<who>` or `public:<paper>`. No exam dumps.

## Commands

```bash
node supabase/tests/schema.test.mjs   # schema + RLS + workflow tests (PGlite, no Docker)
npm run check && npm run test && npm run build   # after scaffolding (G2 approved)
```

## Where things live

| Change | Touch |
|---|---|
| Table, policy, RPC | new `supabase/migrations/NNNN_*.sql` + a case in `supabase/tests/schema.test.mjs` |
| Question format / import | `docs/design/06-content-spec.md`, `content/` |
| CI / deploy | `.github/workflows/`, `docs/design/05-devops-release.md` |
| Agent roles | `.claude/agents/*.md` |
