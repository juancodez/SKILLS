---
name: figma-plugin-publisher
description: |
  Figma Plugin Publisher — helps developers and designers debug, fix, and resubmit Figma Community plugins that were rejected or aren't working on publish. Use this skill whenever: a Figma plugin gets rejected by Figma review, a plugin works locally but breaks when published, there's a "not defined" or "undefined" JS error in a plugin, you're about to submit a new plugin and want a pre-flight check, or you need to know why Figma can't find a file the plugin uses. Trigger: /figma-plugin-publisher. This skill grows over time — new rejection patterns are added after each real publish attempt.
---

# Figma Plugin Publisher

A living debug guide for publishing Figma Community plugins. Built from real rejections — updated each time a new pattern is discovered.

---

## How Figma packages a plugin

This is the single most important thing to understand:

**Figma only ships files explicitly listed in `manifest.json`.** Everything else is invisible to reviewers and end users — even if it sits right next to your files locally.

```json
{
  "main": "code.js",     // ← shipped
  "ui": "ui.html"        // ← shipped
}
```

If your `ui.html` loads `<script src="jszip.min.js">` but `jszip.min.js` is not in `manifest.json`, that file **never reaches the reviewer**. It works on your machine because the file exists locally. It fails on publish because it was never included.

### The two plugin threads

| Thread | File | Has access to |
|--------|------|---------------|
| Plugin thread | `code.js` | `figma.*` API, selected nodes, exportAsync |
| UI thread | `ui.html` | DOM, Blob, fetch, download triggers — NO `figma.*` |

They communicate only via `postMessage`. Keep this in mind when debugging — an error in `ui.html` cannot be caused by `code.js` logic, and vice versa.

---

## Rejection patterns (updated as discovered)

### ❌ Pattern 1 — Missing dependency: `X is not defined`

**Figma's message:** `ReferenceError: JSZip is not defined` (or any library)

**Cause:** A JS library is loaded via `<script src="library.min.js">` in `ui.html`, but the file isn't declared in `manifest.json`, so Figma drops it on publish.

**Fix:** Inline the library directly inside `ui.html`:

```html
<!-- BEFORE (breaks on publish) -->
<script src="jszip.min.js"></script>

<!-- AFTER (self-contained, always works) -->
<script>
/* jszip.min.js inlined — copy full file content here */
!function(e){...}
</script>
```

**How to inline on Windows (PowerShell):**
```powershell
# Use explicit UTF-8 to avoid encoding corruption
$enc = [System.Text.Encoding]::UTF8
$lib = [System.IO.File]::ReadAllText("jszip.min.js", $enc)
$ui  = [System.IO.File]::ReadAllText("ui.html", $enc)
$out = $ui -replace '<script src="jszip\.min\.js"></script>', "<script>`n$lib`n</script>"
[System.IO.File]::WriteAllText("ui.html", $out, [System.Text.UTF8Encoding]::new($false))
```

**How to inline on Mac/Linux (bash):**
```bash
# Insert jszip content between the script tags
node -e "
  const fs = require('fs');
  const lib = fs.readFileSync('jszip.min.js', 'utf8');
  const ui  = fs.readFileSync('ui.html', 'utf8');
  fs.writeFileSync('ui.html', ui.replace('<script src=\"jszip.min.js\"></script>', \`<script>\n\${lib}\n</script>\`));
"
```

---

### ❌ Pattern 2 — Encoding corruption (Windows PowerShell)

**Symptom:** Special characters render as garbage in the plugin UI: `×` → `Ã—`, `—` → `â€"`, `✓` → `âœ"`.

**Cause:** PowerShell's default `Get-Content` reads UTF-8 files as Windows-1252, then `Set-Content -Encoding utf8` re-encodes the already-corrupted text, doubling the damage.

**Fix:** Always use `[System.IO.File]::ReadAllText` with explicit UTF-8, and write with no-BOM UTF-8:
```powershell
$enc = [System.Text.Encoding]::UTF8
$content = [System.IO.File]::ReadAllText("ui.html", $enc)
# ... make changes ...
[System.IO.File]::WriteAllText("ui.html", $content, [System.Text.UTF8Encoding]::new($false))
```

**Alternative:** Use HTML entities for all non-ASCII characters in the source file:
- `×` → `&times;`
- `—` → `&mdash;`
- `✓` → `&#x2713;` or keep as UTF-8 literal if file encoding is clean

---

### ❌ Pattern 3 — CDN / network access blocked

**Symptom:** Plugin loads a library from a CDN (`<script src="https://cdn.jsdelivr.net/...">`). Works in dev, silently fails on publish.

**Cause:** Figma's plugin sandbox blocks all network requests unless domains are whitelisted in `manifest.json`. CDNs for scripts are never appropriate in plugins.

**Fix:** Inline the library (see Pattern 1). Never rely on CDN for plugin dependencies.

If your plugin legitimately needs network access (e.g., calling your own API), declare it:
```json
"networkAccess": {
  "allowedDomains": ["https://api.yourservice.com"]
}
```

---

### ❌ Pattern 4 — File System Access API not available

**Symptom:** `showDirectoryPicker is not a function` or picker never appears.

**Cause:** Figma's plugin iframe sandbox may restrict File System Access API calls in certain environments.

**Fix:** Use ZIP download instead of folder picker for multi-file exports. ZIP (via inlined JSZip) is the reliable cross-platform delivery method for multiple files from a Figma plugin.

---

## Pre-submission checklist

Run this before every publish attempt:

### manifest.json
- [ ] `"id"` is the real plugin ID from figma.com/plugin/create (not a dev placeholder like `"exlo-dev"`)
- [ ] `"main"` and `"ui"` point to the correct files
- [ ] `"networkAccess"` declared if plugin makes any HTTP calls (use `"allowedDomains": ["none"]` if none)
- [ ] `"documentAccess"` set if plugin reads beyond the current page

### ui.html
- [ ] No `<script src="...">` for local files — all JS dependencies inlined
- [ ] No CDN script tags
- [ ] File saved as UTF-8 without BOM
- [ ] All special characters display correctly in local test

### code.js
- [ ] No `require()` or ES module imports — Figma plugin thread is not Node.js
- [ ] `figma.showUI(__html__, { width, height })` called at top level
- [ ] All `exportAsync` calls are inside `try/catch`

### Local test (required before every submit)
1. Figma Desktop → Plugins → Development → Import plugin from manifest → select `manifest.json`
2. Select 1 frame → run plugin → confirm single-file export works
3. Select 2+ frames, multiple formats → confirm ZIP downloads and opens correctly
4. Check browser console inside plugin UI (right-click plugin → Inspect) for any errors

---

## Resubmission steps

1. Fix the issue identified in Figma's rejection email
2. Run the pre-submission checklist above
3. Test locally (see above)
4. In Figma: Plugins → Development → right-click your plugin → Publish
5. Increment the version number in the publish dialog
6. Write a short note in the "What's new" field describing what you fixed — this helps reviewers

---

## Lessons log

*Add a new entry here each time a new pattern is discovered.*

| Date | Plugin | Rejection reason | Fix applied |
|------|--------|-----------------|-------------|
| 2026-08-21 | Exlo | `JSZip is not defined` — jszip.min.js not shipped because not in manifest.json | Inlined JSZip v3.10.1 directly into ui.html |
| 2026-08-21 | Exlo | Encoding corruption (×, —, ✓ garbled) caused by PowerShell default encoding during inlining | Re-inlined using `[System.IO.File]::ReadAllText` with explicit UTF-8 |
