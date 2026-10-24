# /portfolio-review

A structured portfolio and case study review skill based on 5 UX influencer frameworks.

## Trigger

`/portfolio-review` — optionally followed by a file path or section name.

Examples:
- `/portfolio-review` — review current working file
- `/portfolio-review C:\Users\tn\portfolio-10\project-klaro.html`
- `/portfolio-review #problem` — review a specific section

---

## What this skill does

1. Loads the 5 source frameworks from disk (paths in memory `portfolio-review-frameworks`)
2. Reads the target file or section
3. Applies the combined review checklist below
4. Returns a structured critique: what works, what to fix, priority order

---

## Source frameworks to read before reviewing

Read these files in parallel:

- `C:\Users\tn\Desktop\Juan Gomez-Vara\JuanGomezVara-Product-Designer\UX-INFLUENCERS\Justine-Montgomery\portfolio.txt`
- `C:\Users\tn\Desktop\Juan Gomez-Vara\JuanGomezVara-Product-Designer\UX-INFLUENCERS\Lena-Kul\Learning-Portfolio-Kate-Lena-Transcription.txt`
- `C:\Users\tn\Desktop\Juan Gomez-Vara\JuanGomezVara-Product-Designer\UX-INFLUENCERS\4-types-Case-Study-Alex-Svyrida.txt`

Sima and Lena recruiter session are encoded in memory `portfolio-review-rules` — load that too.

---

## Review checklist — apply in this order

### 1. The 20-second test (Lena)
- [ ] Is the title a hook, not a project name?
- [ ] Is the hero scannable in under 20 seconds?
- [ ] Does it pass the relevance check instantly (category, role, seniority)?

### 2. The skeleton (Lena) — is every block present?
- [ ] Business context & goals
- [ ] The problem (business + user)
- [ ] Your role (what you owned, team, timeline)
- [ ] Direction — one line stating what was built
- [ ] Impact — before or after approach, never missing
- [ ] Approach & rationale (hypothesis, trade-offs, constraints)
- [ ] Final solution — max 2 screens + prototype if possible
- [ ] Outcomes & reflections

### 3. The 3 moments (Justine)
- [ ] One interesting decision — is it explicit?
- [ ] One constraint/challenge — is it named?
- [ ] One surprise/learning — is it honest and specific?

### 4. The pivot (Sima)
- [ ] Is there a wrong assumption that got corrected?
- [ ] Does the story show work beyond design (research, code, product)?
- [ ] Is failure or limitation mentioned honestly?

### 5. Hook + scannability (Kate)
- [ ] Section titles tell the story without reading body text
- [ ] Visuals show iterations (v1 vs v2, pros/cons)
- [ ] Preview has motion or a strong image — not a static screenshot
- [ ] No buzzwords ("crafting innovative solutions", "empowering users")

### 6. What to kill (Lena + all)
- [ ] More than 2 screens in a row → cut
- [ ] Workshop screenshots or dirty artifacts → cut
- [ ] Company jargon without explanation → replace
- [ ] Same point repeated in two sections → merge
- [ ] "Collaborated" / "participated" → rewrite as what you owned
- [ ] Long narrative paragraph → replace with labeled scannable blocks

---

## Output format

```
## 20-second test — PASS / NEEDS WORK
[one line]

## Skeleton gaps
- Missing: [X]
- Weak: [Y]

## What works
- [specific line or section]

## Fix first (priority order)
1. [highest impact fix]
2.
3.

## One-line verdict
[honest summary]
```

---

## Tone

Ponytail mode applies. Terse, direct, no flattery. Flag what breaks the case study. Suggest the minimum fix. If a section is strong, say so in one word and move on.
