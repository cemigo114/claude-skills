---
name: redhat-pptx
description: >
  Creates polished, on-brand Red Hat PowerPoint presentations (.pptx) from any input — product
  specs, pitch docs, PDFs, notes, or just a topic. Use this skill whenever the user asks to
  create a Red Hat deck, presentation, pitch, or slides for internal or external audiences.
  Also trigger for requests like "make me a deck for this", "create slides from this doc",
  "build a pitch for engineering leaders", or "put this into a Red Hat presentation".
  Produces Google Slides-compatible .pptx files using the official Red Hat brand palette,
  typography, and slide layout patterns. Automatically runs visual QA before delivering.
---

# Red Hat PPTX Skill

Creates production-quality Red Hat branded presentations using pptxgenjs. Outputs .pptx
files importable directly into Google Slides or PowerPoint.

---

## Quick Start

1. Read `references/brand.md` — colors, fonts, layout rules
2. Read `references/slides.md` — slide type recipes and code patterns
3. Write the JS generation script using pptxgenjs
4. Run it, convert to PDF, render JPGs, do visual QA
5. Fix issues, deliver

**Always read both reference files before writing any code.**

---

## Workflow

### Step 1: Understand the Brief

Extract from the user's input:
- **Purpose**: internal pitch / customer-facing / team update / product vision
- **Audience**: engineering leaders / PMs / execs / customers
- **Slides needed**: estimate from content volume (typically 8-14 slides)
- **Key ask**: what decision or action should the audience take?

If the input is a PDF/doc, read it fully before planning slides.

### Step 2: Plan the Narrative Arc

Every strong technical pitch follows this arc:

  Problem (sharp, quantified) -> Solution (phased, concrete) -> Decision Point (what we need from this room) -> Call to Action (3 asks max)

For engineering audiences specifically:
- Lead with the problem engineers feel, not business metrics
- Show architecture / flow diagrams as shapes (not images)
- Make the "decision slide" the centerpiece -- be explicit about trade-offs
- End with specific asks: Decide / Assign / Schedule

### Step 3: Choose Slide Types

See references/slides.md for full code patterns. Available types:

| Type | When to use |
|------|------------|
| title | Opening slide |
| agenda | 4+ section decks |
| problem-cards | 2-4 pain points with icons |
| stat-callouts | 2-3 big numbers |
| solution-panel | Left dark panel + right feature list |
| flow-diagram | Pipelines / architectures (shapes only, no images) |
| comparison | Two options side-by-side with pros/cons |
| table | Competitive landscape / capability matrix |
| recommendation | Thesis + decision criteria |
| questions | Open items with owners |
| cta | Closing: 3 actionable asks |

Layout rule: vary slide types -- never use the same layout twice in a row.

### Step 4: Write the Generator Script

```javascript
const pptxgen = require("pptxgenjs");
const React = require("react");
const ReactDOMServer = require("react-dom/server");
const sharp = require("sharp");
```

See references/slides.md for complete slide implementations.

**Critical rules (will corrupt the file if broken):**
- NEVER use '#' prefix on hex colors: "EE0000" is correct, "#EE0000" breaks the file
- NEVER encode opacity in 8-char hex: use opacity: 0.15 as a separate property
- NEVER reuse option objects across calls -- always use factory functions for shadows
- NEVER use unicode bullets bullet -- use bullet: true in pptxgenjs

### Step 5: Install Dependencies and Run

```bash
cd /home/claude
npm install pptxgenjs react react-dom react-icons sharp --save
node your_script.js
```

### Step 6: Visual QA (Required)

```bash
python /mnt/skills/public/pptx/scripts/office/soffice.py --headless --convert-to pdf output.pptx
pdftoppm -jpeg -r 120 output.pdf slide
```

View each slide JPG. QA checklist:
- Text overflowing boxes or cut off at edges
- Elements overlapping (text through shapes)
- Low contrast (light text on light bg, dark icons on dark bg)
- Uneven spacing / elements too close to edges (less than 0.5 inch margin)
- Footer bar present on every content slide
- Section marker present on content slides
- No two consecutive slides with the same layout

Fix all issues before delivering.

### Step 7: Deliver

```bash
cp /home/claude/output.pptx /mnt/user-data/outputs/output.pptx
```

Call present_files with the output path. Note: to use in Google Slides, upload to Drive and open with Google Slides. Fonts substitute cleanly to Roboto.

---

## Dependencies

```bash
npm install pptxgenjs react react-dom react-icons sharp
```

LibreOffice (soffice) and Poppler (pdftoppm) are pre-installed.
The soffice wrapper is at /mnt/skills/public/pptx/scripts/office/soffice.py

---

## Reference Files

- references/brand.md -- Complete Red Hat brand system (colors, fonts, layout rules)
- references/slides.md -- Full code implementations for every slide type
