# KnowledgeWala Exam — notes

Newest first. Facts that the code and design docs do not already record.

## 2026-10-01 — G1 approved, G2 design package prepared

- Owner approved G1 (BRD) and decided **the MVP is fully free**; Razorpay/UPI moves to phase 2. BRD: https://claude.ai/code/artifact/b3e2cede-641a-4d52-8897-d4b21b467184. G2 prototype: https://claude.ai/artifact/31yrTYCDvi75NrSxiTjHsT (both private to the owner).
- Local repo: `D:\projects\Claude_AI_Project\repos\knowledgewala-exam`, branch `main`, first commit `61d313f`. No GitHub remote yet; owner to create `dharmsahu-hash/knowledgewala-exam` (private).
- Proposed stack: SvelteKit + Cloudflare Pages/Workers + Supabase + R2. Chosen over the TeacherCircle stack because Vercel Hobby forbids commercial use and SvelteKit bundles are smaller. Supabase free tier allows 2 projects per account and TeacherCircle uses one, so staging after launch needs a decision (D1 in `docs/design/01-system-design.md`).
- `git` is not on PATH on this Windows machine; GitHub Desktop's bundled git works: `%LOCALAPPDATA%\GitHubDesktop\app-2.6.6\resources\app\git\cmd\git.exe`. The Bash tool returned exit 1 with no output here; PowerShell works.
- Schema is tested without Docker using PGlite (`node supabase/tests/schema.test.mjs`, 30 checks). Supabase-specific traps applied from TeacherCircle: SECURITY DEFINER role helpers, explicit `revoke ... from public, anon, authenticated` on functions, no pgcrypto in `public` functions.
