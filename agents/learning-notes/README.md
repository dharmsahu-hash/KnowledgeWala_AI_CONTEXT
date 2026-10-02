# AI Learning Notes Agent — How to Use

An AI agent (Claude skill: **`learning-notes`**) that reads a **video** or **document** and
creates an effective `.md` notes file to learn a topic quickly — useful for **interviews,
exams, day-to-day work and hands-on practice**.

---

## 1. Quick Start (copy-paste prompts)

| Source | What to type to Claude |
|---|---|
| YouTube video | `/learning-notes https://youtube.com/watch?v=... Level: Beginner, Goal: Interview` |
| PDF / Word / PPT | Attach the file → `/learning-notes Goal: Exam` |
| Web article / docs page | `/learning-notes https://docs.example.com/page` |
| Transcript text | Paste transcript → `/learning-notes Topic: Kubernetes Pods` |
| Multiple sources | Attach all → `/learning-notes combine these into one set of notes` |

Follow-ups that work well:
- "Make it shorter — cheat sheet only"
- "Add 20 more interview questions, scenario-based"
- "Export flashcards as Anki CSV"
- "Explain section 4 more simply"
- "Quiz me now from these notes"

Notes are saved to: `D:\projects\Claude_AI_Project\Documents\<Subject>\<Topic>\<Topic>_Learning_Notes.md`

---

## 2. Why this structure works (research-backed)

| Technique | What it is | Where it appears in the notes |
|---|---|---|
| **Active recall** | Retrieve from memory instead of re-reading — the highest-yield study method | Flashcards, Q&A, MCQs, Cornell cue column |
| **Spaced repetition** | Review at growing gaps: Day 1 → 3 → 7 → 14 → 30 | Revision plan checklist |
| **Cornell method** | Cue questions + notes + bottom summary | Key Concepts table + Summary |
| **Feynman technique** | Explain simply to find gaps | Feynman Check section |
| **Dual coding** | Words + visuals together | Mermaid mind maps, diagrams, tables |
| **Elaborative interrogation** | Ask "why?" and "how?" | Concept explanations, trade-off tables |
| **Chunking** | Break big topics into small parts | Numbered sections, one idea per bullet |

Low-value habits this avoids: re-reading, highlighting, copying notes word-for-word, cramming.

---

## 3. Notes Template — sections at a glance

0. TL;DR (1-minute read)
1. Learning Objectives
2. Big Picture / Mind Map
3. Key Concepts (Cornell table, with timestamps for videos)
4. How It Works (steps, diagrams, code, formulas)
5. Hands-on Lab (prereqs, steps, expected output, errors & fixes, challenge)
6. Real-World / Job Use (best practices, checklist)
7. Comparisons & Trade-offs
8. Common Mistakes
9. Interview Prep (basic → scenario Q&A)
10. Exam Prep (definitions, formulas, mnemonics, MCQs)
11. Flashcards (Anki `Q :: A` format)
12. Feynman Check
13. Cheat Sheet
14. Spaced Revision Plan
15. Further Learning
16. Summary

---

## 4. Recommended study workflow (per topic)

1. **Generate** notes with the agent (2–3 min).
2. **Skim** the TL;DR + Mind Map (5 min).
3. **Watch/read** the source with notes open; add your own "so what" comments.
4. **Close notes → blurt** everything you remember on a blank page (active recall).
5. **Do the Hands-on Lab** (learning by doing beats reading).
6. **Import flashcards** into Anki / Quizlet; review 15–20 min daily.
7. **Tick off** the revision plan on Day 1, 3, 7, 14, 30.
8. **Before an interview/exam:** Cheat Sheet + Interview Q&A + MCQs only.

---

## 5. Folder naming convention

```
Documents\
├── INDEX.md                         ← list of all notes (date, topic, source)
├── AI_Learning_Notes_Agent\         ← this agent: SKILL.md, README, template
├── AI_ML\
│   └── RAG\RAG_Learning_Notes.md
├── Cloud\
│   └── AWS_S3\AWS_S3_Learning_Notes.md
└── Programming\
    └── Python_Decorators\Python_Decorators_Learning_Notes.md
```
Rules: `Subject\Topic\Topic_Learning_Notes.md`, underscores instead of spaces, one topic per folder.

---

## 6. Tips for getting video content
- YouTube: Claude reads the page/transcript from the link. If it can't, open the video →
  "…more" → **Show transcript** → copy & paste it, or download the `.srt` subtitle file.
- Private/company videos: paste the transcript or attach the recording's captions.
- Long videos (> 1 hr): ask for notes "chapter by chapter" for better depth.

## 7. Accuracy markers used in notes
- **[Added]** — extra info from the internet (with link) to fill gaps in the source.
- **[Verify]** — uncertain point; check against the source/official docs.
- `[12:30]` — video timestamp to jump back to.

## Sources used to design this agent
- Wooclap — Active recall guide: https://www.wooclap.com/en/blog/active-recall/
- Num8ers — Top 20 science-backed study techniques: https://num8ers.com/guides/top-20-study-techniques-backed-by-science/
- SmartEdge — 10 proven study techniques (2026): https://smartedge.blog/articles/10-proven-study-techniques-that-actually-work
- youtube-transcript.ai — YouTube to study notes: https://youtube-transcript.ai/blog/youtube-transcript-study-notes
- SummarizeYou — YouTube video to notes workflow: https://summarizeyou.com/blog/youtube-video-to-notes
