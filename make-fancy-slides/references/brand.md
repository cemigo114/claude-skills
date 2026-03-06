# Red Hat Brand Reference

## Color Palette

```javascript
const RH = {
  // Backgrounds
  black:    "151515",  // Deepest bg (title, CTA slides)
  darkGray: "212427",  // Primary content bg
  medGray:  "3C3F42",  // Cards, secondary elements
  offWhite: "F0F0F0",  // Light bg (rarely used in dark theme)

  // Text
  white:     "FFFFFF",
  lightGray: "D2D2D2",  // Secondary / body text

  // Brand
  red:    "EE0000",  // Primary accent, CTAs, highlights
  purple: "6753AC",  // Secondary accent, section headers
  blue:   "0066CC",  // Links, technical callouts
  teal:   "009596",  // Success, positive indicators
  green:  "3E8635",  // Positive / confirm
  orange: "EC7A08",  // Warnings, open questions
  yellow: "F4C145",  // Data highlights

  // Borders / dividers
  border: "555555",
};
```

## Typography

Red Hat uses Overpass and Red Hat Display. In pptxgenjs, use `Arial` as the closest
cross-platform substitute (renders as Roboto in Google Slides, which is acceptable).

| Element | Font | Size | Weight |
|---------|------|------|--------|
| Slide title (hero) | Arial | 44-60pt | Bold |
| Slide title (content) | Arial | 30-36pt | Bold |
| Section marker | Arial | 10pt | Normal |
| Subheading / tagline | Arial | 14-16pt | Italic |
| Body / feature label | Arial | 13-14pt | Bold |
| Body detail | Arial | 11-12pt | Normal |
| Code / CLI | Courier New | 10-11pt | Normal |
| Footer / caption | Arial | 8-10pt | Normal |

## Slide Anatomy

Every slide has these structural elements:

```
+----------------------------------------------------------+
| [section marker, 10pt, top-left]     [confidential, top] |
|                                                          |
|   SLIDE TITLE (30-36pt bold white)                       |
|   Tagline / subhead (14pt red italic)                    |
|                                                          |
|   [CONTENT AREA]                                         |
|                                                          |
+--[red accent line, left edge]----------------------------+
| pg# | INTERNAL - CONFIDENTIAL (center) | Red Hat (right)|
+----------------------------------------------------------+
```

### Footer (required on all content slides)

```javascript
function addSlideFooter(slide, pageNum) {
  // Bottom black bar
  slide.addShape("rect", {
    x: 0, y: 5.32, w: 10, h: 0.3,
    fill: { color: RH.black }, line: { color: RH.black }
  });
  // Left red accent
  slide.addShape("rect", {
    x: 0.42, y: 5.15, w: 0.04, h: 0.45,
    fill: { color: RH.red }, line: { color: RH.red }
  });
  // Page number
  slide.addText(String(pageNum), {
    x: 0.1, y: 5.35, w: 0.3, h: 0.2,
    fontSize: 9, color: RH.lightGray, fontFace: "Arial", margin: 0
  });
  // Confidential
  slide.addText("INTERNAL — CONFIDENTIAL", {
    x: 3, y: 5.35, w: 4, h: 0.2,
    fontSize: 8, color: RH.lightGray, fontFace: "Arial",
    align: "center", margin: 0
  });
  // Red Hat brand
  slide.addText("Red Hat", {
    x: 8.5, y: 5.35, w: 1.4, h: 0.2,
    fontSize: 10, color: RH.white, bold: true, fontFace: "Arial",
    align: "right", margin: 0
  });
}
```

### Section Marker (top-left of content slides)

```javascript
function addSectionMarker(slide, text) {
  slide.addText(text, {
    x: 0.52, y: 0.08, w: 4, h: 0.22,
    fontSize: 10, color: RH.lightGray, fontFace: "Arial", margin: 0
  });
}
```

## Layout Principles

### Backgrounds
- Title slide: `darkGray` or `black`
- Content slides: `darkGray` (primary), `black` (for high-impact stat slides)
- CTA / closing: `black` with a red left panel

### Accent Patterns
- **Left red stripe**: `x: 0, w: 0.06, fill: RH.red` — used on title slides, CTA panels
- **Card top stripe**: thin 0.06-0.08h bar at card top in accent color
- **Left card accent**: `w: 0.07` vertical stripe at left edge of content rows

### Cards
```javascript
// Standard card with top accent
slide.addShape("rect", {
  x: xPos, y: yPos, w: cardW, h: cardH,
  fill: { color: RH.medGray },
  line: { color: "555555", width: 0.5 },
  shadow: { type: "outer", color: "000000", blur: 8, offset: 2, angle: 135, opacity: 0.3 }
});
slide.addShape("rect", {
  x: xPos, y: yPos, w: cardW, h: 0.06,
  fill: { color: RH.red }, line: { color: RH.red }
});
```

IMPORTANT: Never reuse the shadow object across multiple shapes. Use a factory:
```javascript
const makeShadow = () => ({ type: "outer", color: "000000", blur: 8, offset: 2, angle: 135, opacity: 0.3 });
```

### Color Assignment by Slide Role
| Role | Primary color |
|------|---------------|
| Problem / pain | red (EE0000) |
| Warning / open question | orange (EC7A08) |
| Solution / positive | teal (009596) or green (3E8635) |
| Architecture / technical | blue (0066CC) |
| AI / product | purple (6753AC) |
| CTA / closing | red panel on black bg |

## Icons (react-icons)

```javascript
const React = require("react");
const ReactDOMServer = require("react-dom/server");
const sharp = require("sharp");

async function iconToBase64(IconComponent, colorHex, size = 256) {
  const svg = ReactDOMServer.renderToStaticMarkup(
    React.createElement(IconComponent, { color: "#" + colorHex, size: String(size) })
  );
  const pngBuffer = await sharp(Buffer.from(svg)).png().toBuffer();
  return "image/png;base64," + pngBuffer.toString("base64");
}

// Usage:
const { FaSearch, FaRandom, FaQuestion } = require("react-icons/fa");
const iconData = await iconToBase64(FaSearch, RH.red, 256);
slide.addImage({ data: iconData, x: 0.2, y: 0.5, w: 0.4, h: 0.4 });
```

Use `react-icons/fa` (Font Awesome) for most icons. Popular options:
- `FaSearch` — discovery, investigation
- `FaRandom` — complexity, variation
- `FaQuestion` — unknowns, open questions
- `FaChartLine` — metrics, growth
- `FaCode` — technical, CLI
- `FaCheckCircle` — success, validation
- `FaExclamationTriangle` — warning
- `FaCog` — configuration, settings
- `FaTerminal` — CLI, command line
- `FaFlask` — testing, experimentation

## Do / Don't

| Do | Don't |
|----|-------|
| Dark backgrounds throughout | Mix dark and light slides randomly |
| Red accent on key actions / titles | Use red for body text |
| One dominant accent color per slide | Give every element a different color |
| `margin: 0` on precisely aligned text | Default margins when aligning to shapes |
| Factory functions for shadow objects | Reuse shadow object across shapes |
| `bullet: true` in pptxgenjs | Unicode "•" characters |
| `Courier New` for code/CLI examples | Monospace for body text |
| Vary slide layouts throughout deck | Same layout two slides in a row |
| Left-align body text | Center body paragraphs |
| 0.5" minimum slide margins | Elements within 0.3" of edges |
