# PCDoctor — notes

Newest first. Decisions not yet recorded in code (there is no code yet).

## 2026-10-03 — 0.5.0 Explain + weekly card, 0.6.0 agent mode + action buttons (stage 6f04e0c)

- 0.5.0 (b8ec4a6): explanations include the known_apps.json entry for any program they name. Without it, the 4B model called the NVIDIA Container "part of Docker".
- 0.6.0 agent mode: Qwen3.5 4B on llama.cpp b11342 calls OpenAI-style tools reliably, with no extra flag (jinja templates are on by default). Tool calls arrive in the stream as `delta.tool_calls`, keyed by index, with the arguments split into pieces. The context window is now 6144 tokens. The prompt has to tell the model to use the tools for files, history and crashes; even so, it skipped `pc_history` until the tool description named drives explicitly.
- Privacy line for tools: the AI gets file names, sizes, dates and drive letters, never folders. PRIVACY.md says so.
- My Library's search box now requires every word, in any order. The AI tool uses the same search, so "Show these files" opens exactly what the AI saw.
- **Incident:** on 2026-10-03 at about 00:45, `move-to-d.ps1 -Set tools` moved PCDoctor's data folder while a stopped test binary still had `pcdoctor.db` open. The first run stopped partway, the second finished, and the 10.9 MB database (history since 2026-10-01 and My Library's list) did not survive; a fresh 45 KB database exists. My Library was rebuilt (42,020 files). History restarts and the owner's own collections are gone.
  - Fix: the script now checks that every file can be opened exclusively before moving anything.
  - Lesson: never move or copy a live SQLite database; stop every process that uses it first and check for open files.

## 2026-10-03 — 0.4.1: Ask focus fix, hardware and live readings for the AI, readability (stage e74fbc5)

- Ask box focus: a `disabled` input loses focus for good, so the box stays enabled; only Ask is blocked while an answer streams, and the box is refocused after each answer.
- Hardware details come from one hidden PowerShell CIM script (`health/hardware.rs`). The CIM classes take about 1 s in total; `Get-PhysicalDisk` and `Get-NetAdapter` took about 8 s, so they are avoided. On this laptop: 2 × 16 GB DDR4, a firmware maximum of 64 GB in 4 slots, a Samsung 1 TB NVMe SSD, no NPU, Wi-Fi at 192.168.1.5. `BatteryStaticData.DesignedCapacity` needs admin, so battery health cannot be computed.
- Live readings go in the user message ("Measured just now: …"), not the system message, so the system prompt and history stay identical and llama.cpp reuses the cached prompt. First words now arrive in about 2 s, including about 0.5 s of measuring.
- Qwen3.5 4B linked "how to remove it" to memory-heavy programs and suggested uninstalling Chrome or Cursor. A prompt rule alone did not fix it; a concrete "To free memory, close programs and tabs…" line in the notes did.
- Python heredocs keep mangling `
`, `\` and quote marks inside Rust strings. Use the Edit tool for those lines.

## 2026-10-02 — Sprint 4 (0.4.0): Activity + Undo, Dashboard reports, CI safety (stage fd31580)

- Undo: `trash::os_limited::list()` reports Explorer's display name, which drops the extension when Windows hides extensions. `restore_all` then restores under that name, so "report.pdf" came back as "report". Matching therefore accepts the name with or without the extension, and restored files are renamed to the logged path. Tested against the real bin (`undo_with_the_real_recycle_bin`, ignored test). The first failed runs left four 18-byte "pcdoctor-undo-test" items in the owner's Recycle Bin; they are harmless.
- Crash report: `wevtutil qe Application` events 1000 and 1002 must also be filtered by provider ("Application Error" / "Application Hang"). On the owner's PC the Codex sandbox service logs its own events under the same ids. Unexpected shutdowns are Kernel-Power 41; there were 4 in the last 30 days on this laptop.
- The drive-full projection needs at least 2 days and 6 readings, so it appears about two days after install.
- `network_guard.rs` ignores code inside `#[cfg(test)]` blocks, using brace counting, so code after a test module is still checked. Checked by planting a leak. A new network feature must be added to `NETWORK_FILES`.
- CI also runs `npm audit --audit-level=high` and `cargo audit`. cargo audit reports only 2 warnings, both for Linux-only GTK crates. `npm run sbom` writes `release/sbom-*.cdx.json`; it needs `cargo install cargo-cyclonedx`.
- Lesson: when editing Rust or JS with Python heredocs, escape sequences such as `
` and Windows paths get mangled. Use the Edit tool for those lines.
- Drive C: on the laptop was down to 11 GB free (4%) on 2026-10-02.

## 2026-10-02 — 0.3.0: opt-in Check for updates (stage dcfd1ca)

- Public repo **dharmsahu-hash/pcdoctor-releases** (created with the owner's OK) holds installers and release notes only; the source repo stays private. Its README is the customer download page.
- `update.rs` asks `api.github.com/repos/dharmsahu-hash/pcdoctor-releases/releases/latest`. It is off by default. Once switched on it checks at most once a day when the app opens, and Settings > Check now can check at any time. The request uses a fixed host, no redirects and the user agent `PCDoctor/<version>`; a 404 means nothing is published yet. The app never downloads an update itself: Download opens the releases page in the browser.
- To publish a release, make the tag the version (`v0.3.0`), attach the setup .exe and write short plain notes, whose first lines show in the app. Drafts and pre-releases are ignored. First release, v0.3.0, was published on 2026-10-02 from stage dcfd1ca (installer SHA-256 e2848a08…a68f3, checked against the public download).
- The setting and the last answer are kept in `update.json` next to the database.

## 2026-10-02 — 0.2.1: smarter AI answers (stage 013cd9a)

- With Qwen3.5 4B, a fixed format ("one sentence, then up to 3 steps") made every answer padded and generic. What worked:
  - Steps only when the person must act, with one example of an action answer in the prompt.
  - Short examples for simple questions.
  - General knowledge allowed for general questions.
  - "Never mention the notes, never offer to act, never end with a question".
  - Call the data "notes", not "PC FACTS"; it echoed the label back.
- Basic facts were missing (Windows version, PCDoctor version, processor). Windows 11 still reports "Windows 10" in ProductName, so build 22000 or higher decides.
- The regression harness is `ai_end_to_end_on_this_pc`, which asks the owner's real 10 questions. It backs up and restores `conversation.json`, because it clears the history.
- An old conversation full of bad answers can make the model copy that style (last 6 turns go into the prompt), so tell testers to press Clear conversation after prompt changes.

## 2026-10-02 — 0.2.0: About screen and safe updates (stage b27a5e9)

- Updates: customers run the new installer over the old one. The app lives in `%LOCALAPPDATA%\PCDoctor` and the data in `%LOCALAPPDATA%\com.knowledgewala.pcdoctor`. Tauri's NSIS installer deletes data only on uninstall with "Delete the application data" ticked, and never with /UPDATE.
- Risk found: on an upgrade the installer's default choice is "Uninstall before installing", which runs the old uninstaller and shows that tick box. `src-tauri/windows/hooks.nsh` (NSIS_HOOK_PREUNINSTALL) ignores the tick when the uninstaller was launched by an installer (`_?=` is in $CMDLINE; checked with a test NSIS build). This only protects updates from 0.2.0 onwards.
- Verified: a silent install of 0.1.0 then 0.2.0 left all 435 data files unchanged.
- Release rules: bump the version in package.json, tauri.conf.json and Cargo.toml; database changes must be additive; never change `identifier` or `productName`.
- The About screen renders PRIVACY.md, EULA.md, LICENSE and THIRD_PARTY.md (Vite `?raw` + a tiny renderer in `src/lib/markdown.tsx`); the version comes from package.json via `__APP_VERSION__`.
- Still open: the EULA is a draft (governing law), the privacy contact email is missing, and there is no auto-update (planned as an opt-in check later).

## 2026-10-02 — AI speed, grounding and memory (stage fd3dff1)

- Slowness was prompt reading: about 900 tokens at about 42 tokens/s on the processor, plus 6.7 tokens/s writing. Fixes: prime the engine (system + facts + history) when the Ask screen opens, use `cache_prompt` so llama.cpp reuses that prefix, cache the facts for 10 minutes, stream with SSE through a Tauri `ipc::Channel`, and set `chat_template_kwargs.enable_thinking=false` for Qwen3.5.
- Graphics card: the llama.cpp b11342 Vulkan build (33 MB, pinned SHA-256) goes in `ai\llama-gpu`. The card is found in the registry display class; the GPU comes from `--list-devices` (free MiB at least 3100) and is used with `--device VulkanN -ngl 99`. If it fails it falls back to the processor. GTX 1650: first words in 0.4–1 s, answers in 3.5–5.5 s, load in 5–10 s.
- Grounding: the 4B model invented "Open Settings" and "right-click, Stop". Fixed by adding a "How to act" line to the facts with the exact clicks (Apps, then Open Startup apps or Open Installed apps, then the three dots and Uninstall in Windows), separate "remove" and "update" app lists, and saying in the prompt that PCDoctor's Settings is not for apps. `ai_end_to_end_on_this_pc` asserts that no invented steps appear.
- Memory: `ai\conversation.json` keeps the last 100 messages, and the last 6 turns go into the prompt. `ai\pc-context.md` holds the facts the AI last saw. Both are private-filtered and cleared by Clear conversation or Delete everything.
- Test automation of the real window: posting clicks to the `Chrome_RenderWidgetHostHWND` child works even when PCDoctor is behind other windows; WindowFromPoint and UIA do not.

## 2026-10-02 — Sprint 3 on stage

- Commits: `dd15509` My Library, `31c71a0` built-in AI + Ask screen + Delete everything saved. Tests: 100 Rust (+5 ignored real-PC checks), 59 screen.
- **AI engine:** llama.cpp **b11342** CPU x64 zip (19,274,166 bytes, sha256 `cc6f3ac9…acd8d`). **Model:** `lmstudio-community/Qwen3.5-4B-GGUF` Q4_K_M (2,707,513,696 bytes, sha256 `25082a7d…1418c`, Apache 2.0). Both are pinned in `ai.rs`.
  - Foundry Local is NOT used yet: its redistribution terms are still unconfirmed.
  - llama.cpp's "latest release" on GitHub is a dummy `v0.5.0` holding only `nightly-tag.txt`; the real builds are tagged `b#####`.
  - In reqwest 0.13 the TLS feature is `rustls` (not `rustls-tls`).
- AI runtime: `llama-server.exe -m model.gguf --host 127.0.0.1 --port <free> -c 8192 -t <cores/2> --api-key <session key>`, started with CREATE_NO_WINDOW. `chat_template_kwargs.enable_thinking=false`, then `<think>` and markdown are stripped. On the owner's laptop answers take 14–25 s; the download took about 8 minutes.
- Files: `%LOCALAPPDATA%\com.knowledgewala.pcdoctor\ai\{llama\, model.gguf, switched-off}`. The owner's laptop already has the AI downloaded (it was used by the end-to-end test).
- My Library: user folders via `dirs` (Windows known folders; the owner's Documents, Desktop and Pictures are under OneDrive) plus non-system fixed drives. It lists 28,637 files in about 6 s. Duplicates skips OneDrive online-only files (attributes RECALL_ON_OPEN, RECALL_ON_DATA_ACCESS, OFFLINE) so nothing is downloaded.
- Not built yet (later): Foundry Local, a background tray sampler, the update check, AI tool-calling and sentence-to-collection rules, live file watching (the library uses a 6-hour rescan instead).

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
