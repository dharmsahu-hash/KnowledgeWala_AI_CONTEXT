# TeacherCircle — notes

Newest first. Facts that the code and `CONTEXT.md` do not already record.

## 2026-10-01 — Windows workstation setup

- Owner's GitHub account: `dharmsahu-hash`. The GitHub CLI is installed at `C:\Program Files\GitHub CLI\gh.exe` and signed in with `repo` scope. Git uses it as its credential helper.
- Clones: `C:\Users\dknitk\code\teachercircle` and `C:\Users\dknitk\code\KnowledgeWala_AI_CONTEXT`. Both set the repo-local git identity to `dharmsahu-hash <331318054+dharmsahu-hash@users.noreply.github.com>`. The machine's global identity is `dknitk`.
- The README's run path (`/Users/dharmendrakumar/Desktop/...`) is from the owner's Mac. On Windows, use the clone path above. The shell scripts in `db/` need Git Bash or WSL.
- **This machine's Node is v12.14.1, too old for the project.** Next.js 14 needs Node 18.17 or newer. `test:unit` uses `node --import` and `--experimental-strip-types`, which need Node 22.6 or newer. Install Node 22 LTS or newer before running `npm ci`, `tsc`, `build`, or tests.
- Docker Desktop is not yet confirmed on this machine. `test:system:real` and local dev need it.
- Initial review: the repo is at `bc24a7d` on `main` (PR #4, the signup confirmation redirect fix). No CI workflow exists (`.github/` is absent). This matches the open gap in `CONTEXT.md`.
