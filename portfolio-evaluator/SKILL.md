---
name: portfolio-evaluator
description: Evaluates a design portfolio (given as a live URL) the way a recruiter and hiring manager actually screen one, and produces a weighted score plus prioritized, actionable fixes. Use this skill whenever the user asks to review, score, grade, critique, or audit a design/product/UX portfolio URL, asks "is my portfolio good," "would this pass a recruiter screen," "what should I fix on my portfolio," or shares a portfolio link (Notion, Framer, Webflow, personal site, Figma) wanting feedback before applying to jobs. Also use for evaluating a single case study page on its own. Always run the full rubric in references/rubric.md rather than giving generic design opinions — the rubric encodes real recruiter/hiring-manager screening behavior and specific portfolio feedback, and is the reason this skill exists over just eyeballing the site.
---

# Portfolio Evaluator

Scores a design portfolio the way it's actually judged in the wild: a recruiter doing a 20-second relevance check, then a hiring manager spending ~5 minutes deciding whether to invite the person to a deep-dive. This skill is built from recruiter interviews, hiring-manager panels, and real 1:1 portfolio feedback sessions — not generic "make it pretty" advice. Full source rubric lives in `references/rubric.md`.

## Workflow

### 1. Get the portfolio content

The input is a live portfolio URL. Fetch and read it as thoroughly as you can:

- Load the homepage first. Note what's visible with **zero scrolling** — this is the 10-second test.
- Open each case study linked from the homepage (cap at the first 5-6 if there are many — a portfolio with 8+ case studies is itself a finding, see rubric).
- Also check for an About page and a Contact section/page.
- If the site is JS-heavy, an image-based deck (Figma/Notion embed), or a PDF, grab what content and screenshots you can — a partial read is fine, but say explicitly which parts you couldn't access rather than guessing at them.
- If the URL fails to load or is behind a login, tell the user and ask for an alternative (export, screenshots, or shared PDF) instead of inventing content.

### 2. Score against the rubric

Open `references/rubric.md`. It has 8 scored categories (100 points total) plus a set of instant red flags. For each category:

- Give a score out of that category's max points.
- Cite **specific evidence** from the actual portfolio you just read (a phrase from the hero, the number of case studies, whether a CTA exists, etc.) — never score a category you didn't actually check.
- If something can't be assessed from what you could access, say so and exclude it from scoring rather than guessing.

### 3. Present results in chat (default)

Always give this inline, without being asked:

- **Overall score /100** and a one-line verdict framed the way a recruiter would think about it: "Would this survive the 20-second relevance check?" / "Would I send this to the hiring manager?"
- **Category breakdown** — a compact table: category, score/max, one-line justification.
- **Top 3-5 fixes, ranked by effort-to-impact** — split into "quick wins" (copy edits, reordering, adding a CTA) vs "bigger changes" (restructuring a case study, cutting projects, rebuilding a section). Be concrete: name the exact section/case study and what to change, don't give generic advice.
- **Any instant red flags** hit (see rubric) called out explicitly, since these alone can sink a screen regardless of score.

Keep this readable in chat — use a table and short bullets, not a wall of text.

### 4. Offer, don't assume, a written report

After the inline evaluation, ask the user if they'd like a full written report as a Markdown file (complete rubric breakdown, quoted evidence per category, full prioritized fix list). Only create the file if they say yes.

### 5. If asked to help fix it

If the user asks you to act on the findings (rewrite hero copy, restructure a case study, draft a CTA, etc.), do that as a separate, explicit next step — don't bundle unsolicited rewrites into the evaluation itself. Ground any rewrite in the specific gaps you identified.

## Notes on calibration

- Don't grade against an abstract ideal of "good design." Grade against whether a time-pressed recruiter/hiring manager can extract the story fast. A visually stunning portfolio that hides the thinking scores worse than a plainer one that's scannable and honest about trade-offs.
- Seniority is judged by how the candidate talks about their work (trade-offs, ambiguity, ownership, zooming out to business impact), not by adjectives they use about themselves. Don't take self-reported seniority claims at face value — check whether the case studies actually demonstrate it.
- One strong, honest case study beats three padded ones. Don't penalize a portfolio for having only 2-3 case studies if they're deep and well-argued; do flag one with 8+ as diluted regardless of individual quality.
