# KnowledgeWala Exam — notes

Newest first. Facts that the code and design docs do not already record.

## 2026-10-01 (later) — Pivot: phase 1 = KnowledgeWala Olympiad

- Owner asked to start with SOF IMO, NSO, IEO for Classes 2–12, beginning with Classes 3 and 7; free with donations; subdomain of knowledgewala.com; certificates; copyright/policy pages. Built as a static Vite + TS app in the same repo (`src/`, `content/`, `docs/olympiad/`). Supabase design kept as phase 2.
- **SOF renamed NSO to ISO (International Science Olympiad).** Syllabus page URLs still use `/nso/`; `/iso/` URLs 404. Class 6/7 syllabi now follow the new NCERT books (maths "Ganita Prakash" chapter names such as "Large Numbers Around Us"; science "Curiosity").
- Official SOF Level 1 patterns (sofworld.org/pattern-questions-and-marking-scheme): IMO 1–4: LR 10, MR 10, EM 10, Ach 5×2 = 40; 5–10: 15/20/10/5×3 = 60. ISO 1–4: LR 5, Sci 25, Ach 5×2; 5–10: 10/35/5×3. IEO 1–4: WSK 15, Reading 10, SWE 5, Ach 5×2; 5–12: 30/10/5/5×3. 60 min; 60% current / 40% previous class.
- knowledgewala.com DNS is self-hosted on a Plesk server (nginx, PleskLin; ns1/ns2.knowledgewala.com, A 160.250.204.37). Subdomain go-live = one CNAME in Plesk to Cloudflare Pages.
- Free AI quotas (Oct 2026): Gemini Flash/Flash-Lite free ≈ 500–1,500 requests/day; Cloudflare Workers AI 10k neurons/day ≈ 15–25 generations. Hence papers come from code generators + reviewed bank; AI only drafts offline.
- Owner's Silvermine trucking portal was reviewed for patterns only (static, localStorage, print-to-PDF certificate); its content is company-owned and must not be copied.
- On this machine, starting node/git processes can stall 1–2 minutes (likely antivirus). Use background runs.

## 2026-10-01 — G1 approved, G2 design package prepared

- Owner approved G1 (BRD) and decided **the MVP is fully free**; Razorpay/UPI moves to phase 2. BRD: https://claude.ai/code/artifact/b3e2cede-641a-4d52-8897-d4b21b467184. G2 prototype: https://claude.ai/artifact/31yrTYCDvi75NrSxiTjHsT (both private to the owner).
- Local repo: `D:\projects\Claude_AI_Project\repos\knowledgewala-exam`, branch `main`, first commit `61d313f`. No GitHub remote yet; owner to create `dharmsahu-hash/knowledgewala-exam` (private).
- Proposed stack: SvelteKit + Cloudflare Pages/Workers + Supabase + R2. Chosen over the TeacherCircle stack because Vercel Hobby forbids commercial use and SvelteKit bundles are smaller. Supabase free tier allows 2 projects per account and TeacherCircle uses one, so staging after launch needs a decision (D1 in `docs/design/01-system-design.md`).
- `git` is not on PATH on this Windows machine; GitHub Desktop's bundled git works: `%LOCALAPPDATA%\GitHubDesktop\app-2.6.6\resources\app\git\cmd\git.exe`. The Bash tool returned exit 1 with no output here; PowerShell works.
- Schema is tested without Docker using PGlite (`node supabase/tests/schema.test.mjs`, 30 checks). Supabase-specific traps applied from TeacherCircle: SECURITY DEFINER role helpers, explicit `revoke ... from public, anon, authenticated` on functions, no pgcrypto in `public` functions.
