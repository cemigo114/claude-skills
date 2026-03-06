# Red Hat Dark Theme Slide Skill

Cursor/Claude skill that generates polished, on-brand Red Hat PowerPoint presentations from any input -- product specs, pitch docs, PDFs, notes, or just a topic.

## What it does

Give the agent any content (a PRD, meeting notes, a topic sentence) and it will:

1. Plan a narrative arc (Problem -> Solution -> Decision -> CTA)
2. Pick from 11 slide types (title, problem-cards, stat-callouts, flow-diagram, comparison, etc.)
3. Generate a pptxgenjs script with Red Hat brand colors, typography, and layout patterns
4. Run visual QA (PDF render + per-slide JPG check)
5. Deliver a `.pptx` file importable into Google Slides or PowerPoint

## Trigger phrases

- "Make me a deck for this"
- "Create slides from this doc"
- "Build a pitch for engineering leaders"
- "Put this into a Red Hat presentation"

## Installation

```bash
# Clone the repo
git clone https://github.com/cemigo114/claude-skills.git

# Install to Cursor (personal)
cp -r claude-skills/make-fancy-slides ~/.cursor/skills/

# Or project-level
cp -r claude-skills/make-fancy-slides your-project/.cursor/skills/
```

## Prerequisites

- **Node.js** with npm (for pptxgenjs, react, react-dom, react-icons, sharp)
- **LibreOffice** (`soffice`) for PDF conversion during visual QA
- **Poppler** (`pdftoppm`) for slide-to-JPG rendering

Dependencies are installed automatically by the skill:

```bash
npm install pptxgenjs react react-dom react-icons sharp
```

## Files

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Core skill -- workflow steps, slide type catalog, critical rules, QA checklist |
| [references/brand.md](references/brand.md) | Red Hat brand system -- color palette, typography, slide anatomy, layout principles, do/don't |
| [references/slides.md](references/slides.md) | Full pptxgenjs code implementations for every slide type (title, problem-cards, stat-callouts, solution-panel, flow-diagram, comparison, table, questions, CTA) |

## Available slide types

| Type | When to use |
|------|------------|
| title | Opening slide |
| agenda | 4+ section decks |
| problem-cards | 2-4 pain points with icons |
| stat-callouts | 2-3 big numbers |
| solution-panel | Left dark panel + right feature list |
| flow-diagram | Pipelines / architectures (shapes only) |
| comparison | Two options side-by-side with pros/cons |
| table | Competitive landscape / capability matrix |
| questions | Open items with owners |
| cta | Closing: 3 actionable asks |
