# Resume Growth — Bogdan Method

A Claude Code skill that evaluates a resume against **Bogdan Zlatkov's 7 rules** for the 2026 job market. Prints a scored `/70` report in chat with prioritized fixes and before/after rewrites — optionally exports the full report as **Markdown** or **HTML** (print-to-PDF ready).

Bogdan Zlatkov runs [Growth Hack Your Career](https://growthackyourcareer.com/) and has helped 160,000+ people with their job search. This skill enforces the specific rules he laid out in the JobScan webinar *"5 Resume Improvements in 5 Minutes"* (Aug 2026).

---

## Trigger

```
/resume-growth-bogdan
```

Also fires on: *"score my resume"*, *"run Bogdan on my resume"*, *"resume growth report"*, *"evaluate my resume Bogdan-style"*.

Optional argument: a resume file path (`.md`, `.txt`, `.pdf`, `.docx`, `.html`) — or paste the resume text directly. You can also paste a job description alongside for the ATS keyword block.

---

## The 7 rules it enforces

| # | Rule | What it checks |
|---|---|---|
| 1 | **Hook — 3 components** | Original + value-add + informal (human/personal). Missing any one = fail. |
| 2 | **Hook — dollar figure** | Presence of a `$` amount in the hook. Lifts interview rate from 4% → 7%. |
| 3 | **Bullet formula** | Every bullet = Power Verb + Method/Skill + Result. Numbers must be bolded. |
| 4 | **Word count** | 350–500 words (1–15 yrs experience) or ≤650 (15+ yrs). |
| 5 | **"and" test** | Max **one** `and` per bullet. More = over-written. |
| 6 | **5 verb-type balance** | Built / Led / Managed / Collaborated / Improved — all present, none over 40%. |
| 7 | **ATS keywords** | 50–75% JD match (Bogdan's sweet spot). Both acronym + full form required. |

Full rubric with scoring bands lives in [`references/bogdan-rules.md`](references/bogdan-rules.md).

---

## What you get

**Always in chat:**

```
# Resume Growth Report — {name}
**Score: 52/70 — Fix flagged blocks, then ship.**

## 1. Hook (6/10)
Original: ✓  Value-add: ✗ (restates 12 years already visible below)  Informal: ✗
Current: "Experienced product manager with 12 years of experience…"
Rewrite: "PM. Shipped $18M ARR at three SaaS startups. Once fixed a bug mid-flight over the Atlantic."

## 2. Dollar figure (0/10)
Absent — role type supports one. See rewrite above.

## 3. Bullet formula (7/10)
9/13 bullets pass. Failing:
- "Led cross-functional initiatives" — missing method + result
- …

… (blocks 4–7, top 5 fixes, before/after rewrites)
```

**Optionally exported as:**
- `.md` — same structure as chat, saved to `./resume-growth-report-{date}.md`
- `.html` — styled report, print-ready → Cmd/Ctrl+P → Save as PDF

---

## Install

Drop the `resume-growth-bogdan/` folder into your Claude Code skills directory:

```bash
# macOS / Linux
cp -r resume-growth-bogdan ~/.claude/skills/

# Windows (PowerShell)
Copy-Item -Recurse resume-growth-bogdan $env:USERPROFILE\.claude\skills\
```

Claude Code auto-discovers any folder with a root `SKILL.md`. No further registration needed.

---

## Files

```
resume-growth-bogdan/
├── SKILL.md                     # 5-step workflow the model follows
├── README.md                    # this file
└── references/
    ├── bogdan-rules.md          # source of truth — the 7 rules + scoring rubric
    └── report-template.html     # placeholder-driven HTML export template
```

---

## What this skill does NOT do

- ❌ Write a resume from scratch → use [career-ops-plugin `tailor-resume`](https://github.com/khoj-ai/career-ops-plugin) instead.
- ❌ Write cover letters → use `/anschreiben`, `/pd-cover-letter`, or `/get-that-job`.
- ❌ Interview prep or LinkedIn optimization → out of scope.
- ❌ Blend in generic resume advice — every judgement traces back to a Bogdan rule.

---

## Design principles

1. **Direct, not polite.** Bogdan-style feedback. No cheerleading, no filler compliments.
2. **Quote verbatim.** Every critique quotes the actual resume text — never paraphrases what it's critiquing.
3. **Rewrites are drop-in.** No `[insert metric]` placeholders — the skill asks for the real number before rewriting.
4. **Language-aware.** Runs and reports in the resume's language (adapts banned-phrase list for ES/DE/FR equivalents).

---

## Attribution

Rules extracted from Bogdan Zlatkov's JobScan webinar *"5 Resume Improvements in 5 Minutes"* (2026-08-20). Bogdan is not affiliated with this skill. All numeric benchmarks (4% → 7% interview lift, 350–500 word optimum, 50–75 ATS sweet spot, etc.) are Bogdan's, not universal truths.

Skill built with [Claude Code](https://claude.com/claude-code), Sept 2026.
