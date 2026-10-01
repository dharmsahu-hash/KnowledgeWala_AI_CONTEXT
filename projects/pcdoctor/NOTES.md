# PCDoctor — notes

Newest first. Decisions not yet recorded in code (there is no code yet).

## 2026-10-01 — Sprint 1 built and pushed to stage

- Repo: github.com/dharmsahu-hash/pcdoctor (private). `main` = initial commit; `stage` = sprint 1 (commit 3ffe664).
- Works: Dashboard (live tiles, drives, biggest programs), Slow or hot? (advisor findings), Settings (privacy/about). Verified by running the app on the owner's PC.
- Toolchain on this PC: Rust 1.98.1 (stable-msvc) and VS 2022 Build Tools C++; `sysinfo` 0.37 API compiles as written.
- GitHub CLI token lacks the `workflow` scope, so `.github/workflows/ci.yml` stays local (listed in `.git/info/exclude`) until the owner runs `gh auth refresh -s workflow`; then remove the exclude line and commit it.
- Next (sprint 2): SQLite database + 5-minute sampler, history charts (ECharts), Free up space, Duplicates (delete-one), Apps advisor.

## 2026-10-01 — Product and technical decisions

- Spec: "PCDoctor by KnowledgeWala — BRD & Design", a Claude doc with two tabs (Overview for non-technical readers, Technical design for developers): https://claude.ai/code/artifact/46ba2c71-b421-42c9-a1aa-2b617e3684bc
- What it is: a free, lightweight, local-only Windows app. Features: Dashboard with interactive reports, "Why is my PC slow or hot?", Free up space, Duplicate files (delete one copy from the app, never the last copy, Recycle Bin only), Apps and startup advisor, My Library (separate module: smart collections by user rules, e.g. all PDFs or movies across drives, live updates and a scheduled re-check, open files from the app), and an AI assistant.
- AI: built in, on-device only, one on/off switch, off by default, nothing sent outside. Engine: Microsoft Foundry Local (in-process SDK, OpenAI-compatible, ~20 MB runtime); fallback is a llama.cpp sidecar. Model: Qwen3.5 4B (Apache 2.0; 4-bit GGUF is 2.71 GB) on PCs with 16 GB+ memory; a smaller Qwen on 8–16 GB; no AI under 8 GB. The AI gets read-only tools only.
- Stack: Tauri 2 (Rust core plus React + TypeScript UI in WebView2), ECharts, SQLite with FTS5, jwalk + notify, BLAKE3, trash crate. This replaced the earlier Python + PySide6 plan to keep the installer at 15 MB or less.
- Brand: KnowledgeWala teal #0f7b6c (dark mode #37c2a8), graduation-cap logo, "a KnowledgeWala product · © 2026".
- Flow: code goes in `D:\projects\Claude_AI_Project\repos\pcdoctor`; push to `stage`; the owner merges to `main` and publishes releases.
- Open: whether Foundry Local's terms allow bundling it (check in sprint 1), governing law for the EULA, open vs closed source, background sampler default, default Library folders.
- Origin: features come from a manual cleanup of the owner's laptop on 2026-10-01. That cleanup found an NVIDIA LocalSystem Container crash loop that left 2,700+ rundll32 processes, a nearly full C: drive, and old installers.
