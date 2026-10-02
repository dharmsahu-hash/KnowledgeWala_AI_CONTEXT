---
name: learning-notes
description: Turn a video (YouTube link, lecture, transcript) or document (PDF, DOCX, PPT, web article) into a Learning Notes .md file for fast learning, interviews, exams, daily work and hands-on practice.
---

# Learning Notes Agent

Read a video or document and produce ONE Markdown file of learning notes that lets
the reader learn the topic quickly, remember it, and use it (interview, exam, job, hands-on).

## 1. Get the source
- **YouTube / video link** → fetch the page and transcript (WebFetch; if no transcript is
  reachable, ask the user to paste the transcript or upload the subtitle/.srt file).
  Keep timestamps (mm:ss) so notes can point back to the video.
- **Uploaded video/audio file** → extract the audio and transcribe if a tool is available;
  otherwise ask for the transcript.
- **PDF / DOCX / PPTX / web article** → read the full content (use the pdf / docx / pptx skill to read).
- If the source is thin or outdated, do 2–4 web searches on the topic and add ONLY the
  missing essentials, marked **[Added]** with the source link, so the notes stay complete
  and current.

## 2. Ask (only if not already clear) — otherwise assume defaults
- Level: Beginner / Intermediate / Advanced (default: Intermediate)
- Goal: Interview / Exam / Job / Hands-on / All (default: All)
- Save location (default: `D:\projects\Claude_AI_Project\Documents\<Subject>\<Topic>\`)

## 3. Write the notes using this structure (skip sections that don't apply)

```
# <Topic> — Learning Notes
> Source: <title + link/file> | Type: Video/Doc | Length: <mins/pages> | Date: <today> | Level: <level>

## 0. TL;DR (read this in 1 minute)
3–5 bullets: what it is, why it matters, the one thing to remember.

## 1. Learning Objectives
"After this you can…" — 3–6 verbs (explain, compare, build, debug…).

## 2. Big Picture / Mind Map
Mermaid mindmap or flowchart showing how the concepts connect.

## 3. Key Concepts (Cornell style)
| Cue / Question | Notes (simple words + example) |
Each concept: definition in plain language → why it matters → small example → analogy.
**Bold** key terms. Add timestamps for videos ([12:30]).

## 4. How It Works (step-by-step / architecture / process)
Numbered steps or diagram. Include formulas, commands, code snippets that appeared.

## 5. Hands-on Lab
Prerequisites → steps → expected output → common errors & fixes → mini challenge.

## 6. Real-World / Day-to-Day Job Use
Where this is used at work, best practices, do's & don'ts, checklist.

## 7. Comparisons & Trade-offs
X vs Y tables; when to use / when not to use.

## 8. Common Mistakes & Misconceptions

## 9. Interview Prep
- 8–15 Q&A: basic → scenario-based → "explain to a non-technical person".
- One "tell me about a time" STAR-style talking point if relevant.

## 10. Exam Prep
- Definitions to memorise, formulas, mnemonics.
- 5–10 MCQs with answers + 1-line explanations (answers in a collapsible <details> block).

## 11. Active Recall — Flashcards
Q :: A lines (Anki-importable).

## 12. Feynman Check
Explain the whole topic in 5 sentences as if to a 12-year-old. List gaps to revisit.

## 13. Cheat Sheet (1 screen)
The 10 most important facts / commands / formulas.

## 14. Spaced Revision Plan
- [ ] Day 1  - [ ] Day 3  - [ ] Day 7  - [ ] Day 14  - [ ] Day 30
(Each review: close notes → blurt everything you remember → check gaps → redo flashcards.)

## 15. Further Learning
Next topics, official docs, best tutorials (links).

## 16. Summary (Cornell bottom section)
5–7 lines in your own words.
```

## 4. Quality rules
- Accuracy first: never invent facts, quotes or timestamps. Mark anything added from outside
  the source as **[Added]** with a link. Flag anything uncertain as **[Verify]**.
- Simple language, short bullets, one idea per bullet, examples for every concept.
- Prefer tables and diagrams (dual coding) over long paragraphs.
- Questions over statements: notes should double as a self-test (active recall).
- Paraphrase sources; no long copied passages.
- Length guide: ~1 page of notes per 10 minutes of video or ~10 pages of document.

## 5. Save
- Folder: `<Documents>\<Subject>\<Topic>\` with meaningful names, e.g.
  `Cloud\AWS_S3\AWS_S3_Learning_Notes.md`, `AI_ML\RAG\RAG_Learning_Notes.md`.
- File name: `<Topic>_Learning_Notes.md` (underscores, no spaces).
- Optional extras in the same folder: `<Topic>_Flashcards.csv` (Anki), `<Topic>_CheatSheet.md`.
- Keep/append an `INDEX.md` at the Documents root listing every notes file with date and source.
- Tell the user in one line where the file was saved and what it covers.
