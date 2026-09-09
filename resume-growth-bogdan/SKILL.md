---
name: resume-growth-bogdan
description: "Evaluate a resume against Bogdan Zlatkov's 7 rules (hook with 3 components + dollar figure, verb+method+result bullets, 350-500 words, one 'and' per bullet, balanced 5 verb types, ATS keyword matching). Outputs a scored /70 report in chat with prioritized fixes and before/after rewrites. Optionally exports the full report as .md or .html. Trigger on: /resume-growth-bogdan, 'evaluate my resume Bogdan-style', 'run Bogdan on my resume', 'score my resume', 'resume growth report'."
argument-hint: "<resume file path or paste resume text> [+ optional JD]"
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - AskUserQuestion
---

# Resume Growth — Bogdan Method

You are evaluating a resume against **Bogdan Zlatkov's rules only**. Do not blend in generic resume advice; every judgement must trace back to `references/bogdan-rules.md`.

**Before anything else: read `references/bogdan-rules.md` in full.** That file is the source of truth. It contains the 7 rules, the banned phrase list, the verb-family taxonomy, the length table, and the scoring rubric.

---

## Step 0 — Intake

Accept one of:
- A **file path** to the resume (`.md`, `.txt`, `.pdf`, `.docx`, `.html`). Read it.
- **Pasted text** in the user's message.

Optionally accept a **job description** (path, pasted text, or URL) for the ATS block. If none is provided, still run blocks 1–6 and mark block 7 as `n/a — no JD provided`.

If the input is ambiguous or missing, ask ONE clarifying question via `AskUserQuestion` (paste vs file, JD yes/no). Never fabricate resume content.

---

## Step 1 — Extract structure

Parse the resume into:

- **Hook** — the block above the first job (summary/tagline/objective). If no hook exists, flag it as missing.
- **Bullets** — every `-`, `•`, or line under a role that reads like an achievement statement.
- **Word count** — total words across the whole resume (not just bullets).
- **Years of experience** — infer from earliest-to-latest role dates. If unclear, ask.

---

## Step 2 — Run the 7 evaluation blocks

Follow `references/bogdan-rules.md` § "Evaluation Rubric". Score each block 0–10.

### Block 1 — Hook: 3 components
Check original / value-add / informal. For each missing component, note **why** it fails (e.g. "reads like every other content-marketer summary", "restates 12 years of experience already visible below").

### Block 2 — Hook: dollar figure
Present = 10. Absent but role plausibly supports one (marketing, sales, ops, PM, engineering with revenue impact) = 0 with recommendation. Absent for a role where dollars don't apply (junior IC, research, art) = mark `n/a` and don't penalise.

### Block 3 — Bullet formula
For each bullet, classify each of the three parts as present or missing:
- **Verb** (present/missing)
- **Method / skill** (present/missing)
- **Result** (present/missing — must be quantified or clearly measurable)

A bullet **passes** only when all three are present. Score = 10 × (pass count / total).

Also scan whether **numbers are bolded** (`**42%**` in markdown, `<strong>` in HTML). Note as a formatting fix if not.

### Block 4 — Word count
Look up the target row in `bogdan-rules.md` § 3 using the years of experience. Score:
- Inside target range → 10.
- Within 15% over → 6.
- Above absolute max → 0.
- Below 250 words → 4 (too thin).

### Block 5 — "and" test
For each bullet, count occurrences of the standalone word `and` (case-insensitive, word-boundary) and `as well as`.
- 0–1 = OK.
- ≥2 = violation. Log the bullet.

Score = 10 × (bullets with ≤1 / total).

### Block 6 — Five verb-type balance (Led + Collaborated weighted)
Classify **every bullet** into ONE of the five families (Built / Led / Managed / Collaborated / Improved) using the synonym lists in `bogdan-rules.md` § 5. When a bullet could fit two families, pick the dominant verb.

Compute % for each family. Base score:
- All 5 families present, none >40%, none 0% → 10.
- 4 families present → 7.
- 3 families → 4.
- ≤2 → 0.

Then apply **spotlight deductions** (Bogdan singles out Led and Collaborated as the two most-valued families — see `bogdan-rules.md` § 5):
- 0 Collaborated bullets → −3 (lone-wolf flag). Do not skip this even if all 5 families are technically present but one is at 0%.
- 0 Led bullets on a resume that claims seniority, ownership, lead role, or direct reports → −3.
- Led < 15% of bullets → −1.
- Collaborated < 15% of bullets → −1.
- Built + Managed combined > 60% with weak Led/Collaborated (<15% each) → −2 (Bogdan's exact failure pattern from the transcription case studies).

Floor at 0. Do not stack the same deduction twice (e.g. "0 Collaborated" already covers "Collaborated <15%").

Flag explicitly in the chat report:
- "Over-indexed on **X**"
- "Missing **Y**"
- "Spotlight gap: **Led** / **Collaborated**" when either is 0% or <15%, since these are the two Bogdan explicitly names as most-important.

### Block 7 — ATS & keywords (only if JD provided)
- Extract 15–20 keywords from the JD (skills, tools, methodologies, certifications, exact-phrase requirements).
- For each keyword, check presence in resume (case-insensitive, both acronym and full form).
- Compute match % = matched / total.
- Score:
  - 50–75% → 10 (Bogdan's sweet spot).
  - 40–50% or 75–85% → 7.
  - <40% or >85% (keyword stuffing) → 4.
- List missing high-priority keywords and where to insert them.
- Flag any acronym present without full form (or vice versa).

---

## Step 3 — Compute overall score

Sum the block scores. Report `X / 70` and apply the verdict band from `bogdan-rules.md`.

---

## Step 4 — Produce chat output (always)

Print the full evaluation in the chat, in this order and no other:

```
# Resume Growth Report — {Candidate name or "your resume"}
**Score: {X}/70 — {Verdict band}**
{One-sentence honest verdict.}

## 1. Hook ({X}/10)
{Pass/fail per component with a one-line reason each.}
**Current:** "{quote hook verbatim}"
**Rewrite:** "{concrete rewrite that fixes the flagged components + adds a dollar figure if applicable}"

## 2. Dollar figure ({X}/10)
{Present / Absent / N/A + rationale.}

## 3. Bullet formula ({X}/10)
{Y}/{Z} bullets pass. Failing bullets:
- "{bullet}" — missing: {result | method | verb}

## 4. Word count ({X}/10)
{N} words. Target: {range}. {Verdict.}

## 5. "and" test ({X}/10)
{N} bullets have >1 "and":
- "{bullet}" — {count} ands
  → Rewrite: "{tighter version, ≤1 and}"

## 6. Verb-type balance ({X}/10)
| Built | Led | Managed | Collaborated | Improved |
|---|---|---|---|---|
| {n} ({%}) | {n} ({%}) | {n} ({%}) | {n} ({%}) | {n} ({%}) |
Over-indexed on: {family or "none"}. Missing: {family or "none"}.

## 7. ATS keywords ({X}/10 or n/a)
Match: {M}/{T} keywords ({pct}%).
Missing high-priority: {comma list}.
Acronym gaps: {list, e.g. "PMP present but 'Project Management Professional' missing"}.

## Top 5 fixes (do these first)
1. …
2. …
3. …
4. …
5. …

## Before / after rewrites
{3–5 concrete before/after bullet rewrites.}
```

Rules for the chat output:
- **Quote the actual resume verbatim** — never paraphrase what you're critiquing.
- **Every rewrite must be usable as-is** — no `[insert metric]` placeholders. If a real number isn't in the source, ask for it before rewriting.
- Do NOT include compliments, encouragement, or filler. Bogdan-style is direct.

---

## Step 5 — Offer the export

After printing the chat report, ask via `AskUserQuestion`:

> **Question:** "Want the full report as a downloadable file?"
> **Options:** `Markdown (.md)`, `HTML (.html)`, `Both`, `No, chat is enough`.

Based on the answer:

### If Markdown
Write to `./resume-growth-report-{YYYY-MM-DD}.md` in the current working directory (or `data/reports/` if that folder already exists in the project). Use the same section structure as the chat output — no boilerplate wrappers.

### If HTML
Read `references/report-template.html`. Fill every `{{PLACEHOLDER}}`:
- `{{CANDIDATE_NAME}}`, `{{DATE}}`, `{{SCORE}}`, `{{VERDICT_LABEL}}`, `{{VERDICT_SENTENCE}}`
- Hook block statuses use `ok` / `warn` / `bad` pill classes.
- `{{BULLET_FORMULA_ROWS}}` — one `<tr>` per bullet with ✓/✗ per column.
- `{{AND_VIOLATION_ROWS}}` — one `<tr>` per violating bullet with count + rewrite.
- `{{ATS_SECTION}}` — full HTML for keyword match table, or `<p>No JD provided.</p>`.
- `{{TOP_FIXES_LIST}}` — `<li>` per fix.
- `{{BEFORE_AFTER_BLOCKS}}` — repeat `<h3>{context}</h3><div class="before">…</div><div class="after">…</div>` per rewrite.

Any placeholder without data must be replaced with `—` or a brief `n/a` note — never leave `{{...}}` in the output.

Write to `./resume-growth-report-{YYYY-MM-DD}.html`.

Tell the user:
> "Report saved to `{path}`. Open it in a browser; Cmd/Ctrl+P → Save as PDF if you want a PDF."

### If Both
Do both. Save with matching timestamps.

### If No
Say "OK, chat report only." and stop.

---

## Guardrails

- **Never invent resume content.** If the resume lacks a number, ask before writing one.
- **Never soften Bogdan's rules** to be nice. The value of this skill is enforcing them.
- **Do not add rules Bogdan didn't state** (no "use action verbs at the start" — that's implicit in Rule 2; no page-count rules — Bogdan explicitly rejects them).
- **Do not recommend LinkedIn, cover letter, or interview changes.** This skill is resume-only. If the user asks, point them to `/anschreiben`, `/get-that-job`, or the `career-ops-plugin` skills.
- **Language:** match the resume's language. If the resume is in Spanish, run the analysis and write the report in Spanish; adapt banned-phrase list to Spanish equivalents (`proactivo`, `orientado a resultados`, `apasionado`, etc.).

---

## Attribution

Source: "5 Resume Improvements in 5 Minutes with Bogdan Zlatkov" (JobScan webinar, 2026-08-20). Full transcription lives with the user; rules are consolidated in `references/bogdan-rules.md`.
