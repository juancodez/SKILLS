# figma-plugin-publisher

A Claude Code skill for debugging, fixing, and resubmitting Figma Community plugins. Built from real rejections — updated each time a new pattern is discovered.

---

## When to use it

- A Figma plugin gets rejected by Figma review
- Plugin works locally but breaks when published
- `ReferenceError: X is not defined` in a published plugin
- Pre-flight check before submitting a new plugin
- Figma can't find a file the plugin references

**Trigger:** `/figma-plugin-publisher`

---

## What it covers

### Core concept
Figma only ships files explicitly listed in `manifest.json`. Everything else is invisible to reviewers — even if it sits right next to your files locally. This single rule is behind most rejections.

### Rejection patterns (living list)

| Pattern | Symptom | Fix |
|---------|---------|-----|
| Missing dependency | `ReferenceError: JSZip is not defined` | Inline the library into `ui.html` |
| Encoding corruption | Special chars render as garbage (`×` → `Ã—`) | Use `[System.IO.File]::ReadAllText` with explicit UTF-8 on Windows |
| CDN script blocked | Library loads in dev, silently fails on publish | Inline it — never use CDN tags in plugins |
| File System Access API | `showDirectoryPicker is not a function` | Use ZIP download (inlined JSZip) instead |

### Pre-submission checklist
- `manifest.json` has the real plugin ID, correct file references, and `networkAccess` declared if needed
- `ui.html` has no local `<script src="...">` — all JS dependencies inlined
- No CDN script tags
- File saved as UTF-8 without BOM
- `code.js` uses no `require()` or ES module imports

---

## How to install

Copy `SKILL.md` into your Claude Code skills directory:

```
~/.claude/skills/figma-plugin-publisher/SKILL.md
```

Then register it in `~/.claude/CLAUDE.md`:

```md
When the user types `/figma-plugin-publisher`, invoke the Skill tool with `skill: "figma-plugin-publisher"` before doing anything else.
```

Or, since the skill has a root `SKILL.md`, Claude Code will auto-discover it.

---

## Lessons log

| Date | Plugin | Rejection reason | Fix applied |
|------|--------|-----------------|-------------|
| 2026-08-21 | Exlo | `JSZip is not defined` — jszip.min.js not shipped because not in manifest.json | Inlined JSZip v3.10.1 directly into ui.html |
| 2026-08-21 | Exlo | Encoding corruption (×, —, ✓ garbled) caused by PowerShell default encoding during inlining | Re-inlined using `[System.IO.File]::ReadAllText` with explicit UTF-8 |

*Add a row here each time a new rejection pattern is discovered.*

---

## File structure

```
figma-plugin-publisher/
├── SKILL.md     # The skill loaded by Claude Code
└── README.md    # This file
```
