# PCDoctor — notes

Newest first. Decisions not yet recorded in code (there is no code yet).

## 2026-10-02 — Sprint 2 on stage

- Commits on `stage`: `bdb5633` Free up space, `ed0fcaa` Duplicates, `ee45ba9` Apps advisor, `2fe18fe` history charts + docs. Tests: 77 Rust (+3 ignored real-PC checks), 39 screen.
- Design choices that matter later:
  - Free up space rescans categories inside `clean`, so the UI only sends category ids, never paths.
  - Duplicates delete re-hashes (BLAKE3) each chosen copy and deletes only if it still matches a kept copy. The last copy, a changed file, or a stranger path is impossible to delete.
  - "Spare place" folders (Downloads/Desktop/Temp) are judged only below the searched folder's parent. The first version matched `\Temp\` anywhere and broke on paths under `AppData\Local\Temp`.
  - Apps advisor never uninstalls. `open_settings` accepts only `apps` and `startup` (`ms-settings:` pages). Verdicts live in `resources/app_advice.json`.
  - History: SQLite at `%LOCALAPPDATA%\com.knowledgewala.pcdoctor\pcdoctor.db`, a reading every 5 minutes while the app is open (no tray process yet), kept 90 days, about 240 points per chart.
- Real-PC check (read-only, `cargo test real_pc -- --ignored --nocapture`) on the owner's laptop: 55 apps / 11 startup items; 1,005 files and 16 duplicate groups found in 1.4 s; little left to clean after the 2026-10-01 cleanup.
- Tests must not use a fake drive like `Z:\` for "missing folder": Windows spent about 6 minutes timing out. Use a missing subfolder of a tempdir.

## 2026-10-01 — Sprint 1 built and pushed to stage

- Repo: github.com/dharmsahu-hash/pcdoctor (private). `main` = initial commit; `stage` = sprint 1 (commit 3ffe664).
- Works: Dashboard (live tiles, drives, biggest programs), Slow or hot? (advisor findings), Settings (privacy/about). Verified by running the app on the owner's PC.
- Toolchain on this PC: Rust 1.98.1 (stable-msvc) and VS 2022 Build Tools C++; `sysinfo` 0.37 API compiles as written.
- Commit `8ac92eb`: 38 Rust + 15 screen tests, INSTALL.md (customer, click-only), SETUP.md (developer), and the CI workflow (the gh token has had the `workflow` scope since 2026-10-01).
- Customer install = one file: `src-tauri\target\release\bundle\nsis\PCDoctor_0.1.0_x64-setup.exe` from `npm run tauri build` (1.4 MB). It is a per-user NSIS installer with no admin prompt. Verified: silent install (`/S`) → Start menu entry → app opens (34 MB memory) → silent uninstall leaves nothing registered. Until it is code-signed, SmartScreen shows "More info → Run anyway"; the owner wants setup kept click-only and simple (no over-engineering).
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
