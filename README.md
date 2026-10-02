# KnowledgeWala AI context

Shared memory for AI coding agents (Claude Code, Cursor, and others) that work on dharmsahu-hash projects. Each project has a folder under `projects/`. Load a project's files before you change that project, and add to them when you learn something that is not obvious from the code.

## Layout

| Path | Contents |
|---|---|
| `projects/<name>/CONTEXT.md` | Long-form context: product, runtime, schema map, fixed bugs, open gaps |
| `projects/<name>/AGENTS.md` | Short operating rules and invariants |
| `projects/<name>/NOTES.md` | Running log: decisions, environment facts, session notes (newest first) |

## Projects

| Project | Repo | Production |
|---|---|---|
| TeacherCircle | https://github.com/dharmsahu-hash/teachercircle | https://teachercircle.vercel.app |
| KnowledgeWala Exam | not yet created (local: `repos\knowledgewala-exam`) | not deployed (G2 design stage) |

## Agents

Reusable agents (Claude skills) that are not tied to one project. Each lives under `agents/<name>/` with a `SKILL.md` (the instructions) and a `README.md` (how to use).

| Agent | What it does |
|---|---|
| [`learning-notes`](agents/learning-notes/) | Reads a video (YouTube, lecture, transcript) or document (PDF, DOCX, PPT, article) and writes a Markdown learning-notes file for interviews, exams, day-to-day work and hands-on practice |

## Keeping it in sync

`CONTEXT.md` and `AGENTS.md` are copies of `docs/ai/CONTEXT.md` and `AGENTS.md` in the project repo. Edit the project repo first, then copy the files here. `NOTES.md` lives only here.
