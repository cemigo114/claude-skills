# Slide Type Implementations

Full pptxgenjs code for each slide type. Import RH colors and helper functions from brand.md.

## Boilerplate: Script Setup

```javascript
const pptxgen = require("pptxgenjs");
const React = require("react");
const ReactDOMServer = require("react-dom/server");
const sharp = require("sharp");

const RH = {
  black: "151515", darkGray: "212427", medGray: "3C3F42",
  white: "FFFFFF", lightGray: "D2D2D2", red: "EE0000",
  purple: "6753AC", blue: "0066CC", teal: "009596",
  green: "3E8635", orange: "EC7A08", yellow: "F4C145",
};

const makeShadow = () => ({ type: "outer", color: "000000", blur: 8, offset: 2, angle: 135, opacity: 0.3 });

async function iconToBase64(IconComponent, colorHex, size = 256) {
  const svg = ReactDOMServer.renderToStaticMarkup(
    React.createElement(IconComponent, { color: "#" + colorHex, size: String(size) })
  );
  const buf = await sharp(Buffer.from(svg)).png().toBuffer();
  return "image/png;base64," + buf.toString("base64");
}

function addFooter(slide, pageNum) {
  slide.addShape("rect", { x: 0, y: 5.32, w: 10, h: 0.3, fill: { color: RH.black }, line: { color: RH.black } });
  slide.addShape("rect", { x: 0.42, y: 5.15, w: 0.04, h: 0.45, fill: { color: RH.red }, line: { color: RH.red } });
  slide.addText(String(pageNum), { x: 0.1, y: 5.35, w: 0.3, h: 0.2, fontSize: 9, color: RH.lightGray, fontFace: "Arial", margin: 0 });
  slide.addText("INTERNAL — CONFIDENTIAL", { x: 3, y: 5.35, w: 4, h: 0.2, fontSize: 8, color: RH.lightGray, fontFace: "Arial", align: "center", margin: 0 });
  slide.addText("Red Hat", { x: 8.5, y: 5.35, w: 1.4, h: 0.2, fontSize: 10, color: RH.white, bold: true, fontFace: "Arial", align: "right", margin: 0 });
}

function addSectionMarker(slide, text) {
  slide.addText(text, { x: 0.52, y: 0.08, w: 5, h: 0.22, fontSize: 10, color: RH.lightGray, fontFace: "Arial", margin: 0 });
}

async function buildDeck() {
  const pres = new pptxgen();
  pres.layout = "LAYOUT_16x9";  // 10" x 5.625"

  // --- ADD SLIDES HERE ---

  await pres.writeFile({ fileName: "/home/claude/output.pptx" });
  console.log("Done.");
}
buildDeck().catch(console.error);
```

---

## 1. Title Slide

Dark background with purple accent box top-right, large product name left, red left stripe.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.darkGray };

  // Red left accent stripe
  s.addShape("rect", { x: 0, y: 0, w: 0.06, h: 5.625, fill: { color: RH.red }, line: { color: RH.red } });

  // Purple info box top-right
  s.addShape("rect", { x: 5.5, y: 0, w: 4.5, h: 1.5, fill: { color: RH.purple }, line: { color: RH.purple } });
  s.addText("RED HAT INFERENCE", { x: 5.6, y: 0.1, w: 4.2, h: 0.35, fontSize: 11, color: RH.white, bold: true, charSpacing: 3, fontFace: "Arial", margin: 0 });
  s.addText("OpenShift AI · vLLM", { x: 5.6, y: 0.5, w: 4.2, h: 0.25, fontSize: 10, color: "D0BCFF", fontFace: "Arial", margin: 0 });
  s.addText("Product Engineering Review", { x: 5.6, y: 0.85, w: 4.2, h: 0.4, fontSize: 9, color: RH.lightGray, fontFace: "Arial", italic: true, margin: 0 });

  // Main title
  s.addText("ProductName", { x: 0.5, y: 0.9, w: 5.0, h: 1.0, fontSize: 60, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Subtitle / Tagline", { x: 0.5, y: 1.95, w: 5.5, h: 0.5, fontSize: 22, fontFace: "Arial", color: RH.red, margin: 0 });
  s.addText("Stop guessing. Ship optimal configs.", { x: 0.5, y: 2.55, w: 6, h: 0.4, fontSize: 16, fontFace: "Arial", color: RH.lightGray, italic: true, margin: 0 });

  // Divider + presenter
  s.addShape("rect", { x: 0.5, y: 3.1, w: 4.5, h: 0.04, fill: { color: RH.medGray }, line: { color: RH.medGray } });
  s.addText([
    { text: "Your Name", options: { bold: true, breakLine: true } },
    { text: "Your Title", options: { color: RH.lightGray } }
  ], { x: 0.5, y: 3.25, w: 4, h: 0.65, fontSize: 13, color: RH.white, fontFace: "Arial", margin: 0 });
  s.addText("Month Year", { x: 0.5, y: 4.1, w: 3, h: 0.3, fontSize: 11, color: RH.lightGray, fontFace: "Arial", margin: 0 });

  addFooter(s, 1);
}
```

---

## 2. Problem Cards

3 pain point cards side by side with icon, title, and body. Top red stripe per card.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.darkGray };
  addSectionMarker(s, "THE PROBLEM");
  addFooter(s, pageNum);

  s.addText("Engineers are flying blind", { x: 0.5, y: 0.25, w: 9, h: 0.65, fontSize: 34, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("vLLM defaults leave performance on the table for non-standard workloads", { x: 0.5, y: 0.9, w: 9, h: 0.32, fontSize: 14, color: RH.red, fontFace: "Arial", italic: true, margin: 0 });

  const { FaSearch, FaRandom, FaQuestion } = require("react-icons/fa");
  const cards = [
    { icon: FaSearch, title: "No Guidance for Edge Cases", body: "Long prefill, prefill-only, long decode — vLLM defaults are wrong. Engineers trial-and-error for days." },
    { icon: FaRandom, title: "One Config Does Not Fit All", body: "20K vs 500K context workloads need fundamentally different knob settings. Same YAML breaks both." },
    { icon: FaQuestion, title: "Which Knob? Why?", body: "max_num_batched_tokens, KV quant, cuda_graph_sizes — no decision tree. Zero explainability." },
  ];

  for (let i = 0; i < cards.length; i++) {
    const xPos = 0.4 + i * 3.1;
    s.addShape("rect", { x: xPos, y: 1.45, w: 2.95, h: 3.5, fill: { color: RH.medGray }, line: { color: "555555", width: 0.5 }, shadow: makeShadow() });
    s.addShape("rect", { x: xPos, y: 1.45, w: 2.95, h: 0.06, fill: { color: RH.red }, line: { color: RH.red } });
    const iconData = await iconToBase64(cards[i].icon, RH.red);
    s.addImage({ data: iconData, x: xPos + 0.2, y: 1.65, w: 0.4, h: 0.4 });
    s.addText(cards[i].title, { x: xPos + 0.12, y: 2.15, w: 2.7, h: 0.55, fontSize: 13, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
    s.addText(cards[i].body, { x: xPos + 0.12, y: 2.75, w: 2.7, h: 1.9, fontSize: 11, fontFace: "Arial", color: RH.lightGray, margin: 0 });
  }
}
```

---

## 3. Stat Callouts

2–3 large number callouts on black background. Each stat gets a colored top stripe.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.black };
  addSectionMarker(s, "CUSTOMER IMPACT");
  addFooter(s, pageNum);

  s.addText("The cost of misconfiguration", { x: 0.5, y: 0.25, w: 9, h: 0.6, fontSize: 34, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });

  const stats = [
    { num: "32%", label: "Throughput left on table", sub: "default vLLM config\nvs. tuned long-context", color: RH.red },
    { num: "Days", label: "Wasted per deployment", sub: "trial-and-error tuning\nper new model/GPU", color: RH.orange },
    { num: "0", label: "Documented decision trees", sub: "for vLLM production\nconfig at Red Hat", color: RH.purple },
  ];

  for (let i = 0; i < stats.length; i++) {
    const x = 0.5 + i * 3.1;
    s.addShape("rect", { x, y: 1.2, w: 2.9, h: 3.6, fill: { color: RH.darkGray }, line: { color: RH.medGray, width: 0.5 } });
    s.addShape("rect", { x, y: 1.2, w: 2.9, h: 0.07, fill: { color: stats[i].color }, line: { color: stats[i].color } });
    s.addText(stats[i].num, { x, y: 1.5, w: 2.9, h: 1.2, fontSize: 64, fontFace: "Arial", bold: true, color: stats[i].color, align: "center", margin: 0 });
    s.addText(stats[i].label, { x: x + 0.1, y: 2.75, w: 2.7, h: 0.55, fontSize: 14, fontFace: "Arial", bold: true, color: RH.white, align: "center", margin: 0 });
    s.addText(stats[i].sub, { x: x + 0.1, y: 3.35, w: 2.7, h: 0.9, fontSize: 11, fontFace: "Arial", color: RH.lightGray, align: "center", margin: 0 });
  }

  s.addText("Source: internal benchmarks", { x: 0.5, y: 5.0, w: 9, h: 0.2, fontSize: 8, color: "888888", fontFace: "Arial", italic: true, margin: 0 });
}
```

---

## 4. Solution Panel

Left dark panel with product name. Right side: vertical feature list with left accent stripe.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.darkGray };
  addSectionMarker(s, "THE SOLUTION");
  addFooter(s, pageNum);

  // Left dark panel
  s.addShape("rect", { x: 0, y: 0, w: 4.2, h: 5.32, fill: { color: RH.black }, line: { color: RH.black } });
  s.addShape("rect", { x: 0, y: 0, w: 0.06, h: 5.32, fill: { color: RH.red }, line: { color: RH.red } });
  s.addText("ProductName", { x: 0.2, y: 0.5, w: 3.8, h: 0.9, fontSize: 42, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Subtitle / Value Prop", { x: 0.2, y: 1.45, w: 3.8, h: 0.6, fontSize: 16, fontFace: "Arial", color: RH.red, margin: 0 });
  s.addText("One-line strategic summary.", { x: 0.2, y: 2.2, w: 3.8, h: 0.8, fontSize: 13, fontFace: "Arial", color: RH.lightGray, italic: true, margin: 0 });

  // Right feature rows
  const features = [
    { label: "Feature One", detail: "Brief explanation of what this does and why it matters" },
    { label: "Feature Two", detail: "Brief explanation of what this does and why it matters" },
    { label: "Feature Three", detail: "Brief explanation of what this does and why it matters" },
    { label: "Feature Four", detail: "Brief explanation of what this does and why it matters" },
    { label: "Feature Five", detail: "Brief explanation of what this does and why it matters" },
  ];

  for (let i = 0; i < features.length; i++) {
    const y = 0.5 + i * 0.83;
    s.addShape("rect", { x: 4.35, y, w: 5.45, h: 0.72, fill: { color: RH.medGray }, line: { color: "555555", width: 0.5 } });
    s.addShape("rect", { x: 4.35, y, w: 0.07, h: 0.72, fill: { color: RH.red }, line: { color: RH.red } });
    s.addText(features[i].label, { x: 4.52, y: y + 0.04, w: 5.1, h: 0.28, fontSize: 12, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
    s.addText(features[i].detail, { x: 4.52, y: y + 0.34, w: 5.1, h: 0.3, fontSize: 10, fontFace: "Arial", color: RH.lightGray, margin: 0 });
  }
}
```

---

## 5. Flow Diagram (Architecture)

Pipeline steps with colored boxes and arrows. All shapes — no images.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.darkGray };
  addSectionMarker(s, "ARCHITECTURE");
  addFooter(s, pageNum);

  s.addText("Phase I: The Recipe Machine", { x: 0.5, y: 0.2, w: 9, h: 0.6, fontSize: 32, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Copy-paste deployable. Benchmarked. Explainable.", { x: 0.5, y: 0.8, w: 9, h: 0.32, fontSize: 14, color: RH.red, fontFace: "Arial", italic: true, margin: 0 });

  const steps = [
    { label: "CLI Tool\n(oc ai config)", color: RH.purple, x: 0.3 },
    { label: "Recipe\nSelector", color: RH.blue, x: 2.15 },
    { label: "Recipe\nLibrary\n(YAMLs)", color: RH.teal, x: 4.0 },
    { label: "Config YAML\nOutput", color: RH.green, x: 5.85 },
    { label: "vLLM\nDeployment", color: RH.red, x: 7.7 },
  ];

  for (let i = 0; i < steps.length; i++) {
    const { label, color, x } = steps[i];
    s.addShape("rect", { x, y: 1.4, w: 1.7, h: 0.9, fill: { color }, line: { color } });
    s.addText(label, { x, y: 1.4, w: 1.7, h: 0.9, fontSize: 10, fontFace: "Arial", bold: true, color: RH.white, align: "center", valign: "middle", margin: 0 });
    if (i < steps.length - 1) {
      s.addShape("rect", { x: x + 1.72, y: 1.81, w: 0.4, h: 0.06, fill: { color: RH.lightGray }, line: { color: RH.lightGray } });
      s.addText("->", { x: x + 1.98, y: 1.74, w: 0.2, h: 0.2, fontSize: 10, color: RH.lightGray, margin: 0, fontFace: "Arial" });
    }
  }

  // Add notes or taxonomy callout box at bottom
  s.addShape("rect", { x: 0.3, y: 4.45, w: 9.4, h: 0.65, fill: { color: "2A2D30" }, line: { color: RH.purple, width: 0.7 } });
  s.addText("Taxonomy: Model x Workload x Hardware x Objective", { x: 0.45, y: 4.5, w: 9.1, h: 0.55, fontSize: 10, color: RH.lightGray, fontFace: "Arial", italic: true, margin: 0 });
}
```

---

## 6. Comparison (Decision Slide)

Two options side by side with pros/cons. This is the centerpiece for engineering pitches.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.black };
  addSectionMarker(s, "ENGINEERING DECISION");
  addFooter(s, pageNum);

  s.addText("The Decision We Are Here to Make", { x: 0.5, y: 0.2, w: 9, h: 0.65, fontSize: 32, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Option A vs Option B — let's decide.", { x: 0.5, y: 0.85, w: 9, h: 0.35, fontSize: 14, color: RH.orange, fontFace: "Arial", italic: true, margin: 0 });

  // Option A card (teal border)
  s.addShape("rect", { x: 0.3, y: 1.35, w: 4.4, h: 3.7, fill: { color: RH.darkGray }, line: { color: RH.teal, width: 1.5 } });
  s.addShape("rect", { x: 0.3, y: 1.35, w: 4.4, h: 0.07, fill: { color: RH.teal }, line: { color: RH.teal } });
  s.addText("Option A: CLI Tool", { x: 0.45, y: 1.5, w: 4.1, h: 0.45, fontSize: 18, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  // Code example
  s.addText("oc ai config get --model llama-3 --hw h100x8", { x: 0.35, y: 2.05, w: 4.3, h: 0.55, fontSize: 10, fontFace: "Courier New", color: RH.teal, margin: 4 });
  // Pros and cons
  s.addText([
    { text: "yes  ", options: { color: RH.teal, bold: true } }, { text: "Fits existing oc/kubectl workflow", options: { breakLine: true } },
    { text: "yes  ", options: { color: RH.teal, bold: true } }, { text: "Scriptable, CI/CD friendly", options: { breakLine: true } },
    { text: "yes  ", options: { color: RH.teal, bold: true } }, { text: "Fast to ship (no UI work needed)", options: { breakLine: true } },
    { text: "no   ", options: { color: RH.orange, bold: true } }, { text: "Harder for onboarding non-experts", options: { breakLine: true } },
    { text: "no   ", options: { color: RH.orange, bold: true } }, { text: "No visual diff vs. defaults", options: {} },
  ], { x: 0.45, y: 2.7, w: 4.05, h: 2.1, fontSize: 11, fontFace: "Arial", color: RH.lightGray, margin: 0 });

  // Option B card (purple border)
  s.addShape("rect", { x: 5.3, y: 1.35, w: 4.4, h: 3.7, fill: { color: RH.darkGray }, line: { color: RH.purple, width: 1.5 } });
  s.addShape("rect", { x: 5.3, y: 1.35, w: 4.4, h: 0.07, fill: { color: RH.purple }, line: { color: RH.purple } });
  s.addText("Option B: Cursor / IDE Extension", { x: 5.45, y: 1.5, w: 4.1, h: 0.45, fontSize: 18, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Decision Tree -> Visual Diff -> 1-click Apply", { x: 5.35, y: 2.05, w: 4.3, h: 0.55, fontSize: 10, fontFace: "Arial", color: RH.purple, italic: true, margin: 4 });
  s.addText([
    { text: "yes  ", options: { color: RH.purple, bold: true } }, { text: "Guided questionnaire lowers barrier", options: { breakLine: true } },
    { text: "yes  ", options: { color: RH.purple, bold: true } }, { text: "Visual side-by-side YAML diff", options: { breakLine: true } },
    { text: "yes  ", options: { color: RH.purple, bold: true } }, { text: "Cursor MCP: meets devs in their IDE", options: { breakLine: true } },
    { text: "no   ", options: { color: RH.orange, bold: true } }, { text: "Higher build cost, maintenance burden", options: { breakLine: true } },
    { text: "no   ", options: { color: RH.orange, bold: true } }, { text: "Not scriptable without extra API layer", options: {} },
  ], { x: 5.45, y: 2.7, w: 4.05, h: 2.1, fontSize: 11, fontFace: "Arial", color: RH.lightGray, margin: 0 });
}
```

---

## 7. Competitive Table

Capability matrix with highlighted "our product" column in red header.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.black };
  addSectionMarker(s, "COMPETITIVE POSITIONING");
  addFooter(s, pageNum);

  s.addText("Why We Win", { x: 0.5, y: 0.2, w: 9, h: 0.6, fontSize: 30, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Nobody else does vLLM-specific, explainable, self-hosted config optimization.", { x: 0.5, y: 0.78, w: 9, h: 0.35, fontSize: 13, color: RH.red, fontFace: "Arial", italic: true, margin: 0 });

  const headers = ["Capability", "Competitor A", "Competitor B", "Competitor C", "OurProduct"];
  const rows = [
    ["Serving engine config", "no", "Partial", "Partial", "yes"],
    ["LLM-specific knobs", "no", "no", "Partial", "yes"],
    ["Pre-built recipes", "no", "no", "no", "yes"],
    ["Explainability", "no", "Partial", "no", "yes"],
    ["Self-hosted", "no", "yes", "yes", "yes"],
    ["ROI quantification", "no", "no", "no", "yes (v1)"],
  ];

  const colW = [2.7, 1.6, 1.6, 1.6, 1.6];
  const tableRows = [
    headers.map((h, i) => ({
      text: h,
      options: { bold: true, color: i === 4 ? RH.black : RH.white, fill: { color: i === 4 ? RH.red : "2A2D30" }, fontSize: 11, fontFace: "Arial", align: "center", valign: "middle" }
    })),
    ...rows.map(r => r.map((cell, ci) => ({
      text: cell,
      options: {
        color: ci === 4 ? (cell === "yes" ? "00CC44" : RH.lightGray) : (cell === "yes" ? RH.teal : cell === "no" ? "666666" : RH.lightGray),
        fill: { color: ci === 4 ? "1A1A1A" : "222527" },
        bold: ci === 4,
        fontSize: 10.5, fontFace: "Arial", align: "center", valign: "middle"
      }
    })))
  ];

  s.addTable(tableRows, { x: 0.35, y: 1.2, w: 9.3, h: 3.8, colW, rowH: 0.55, border: { pt: 0.5, color: "3A3D42" } });
}
```

---

## 8. Open Questions

Rows of open items, each with an owner badge. Use orange left accent.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.darkGray };
  addSectionMarker(s, "OPEN QUESTIONS");
  addFooter(s, pageNum);

  s.addText("Questions for Engineering Leaders", { x: 0.5, y: 0.2, w: 9, h: 0.65, fontSize: 30, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("We need decisions on these before breaking ground.", { x: 0.5, y: 0.83, w: 9, h: 0.35, fontSize: 14, color: RH.red, fontFace: "Arial", italic: true, margin: 0 });

  const questions = [
    { q: "UI Delivery Mechanism", detail: "CLI via oc ai config vs. Cursor MCP vs. OpenShift AI console widget — which ships Phase I?", owner: "OpenShift AI Team" },
    { q: "CLI Command Namespace", detail: "Standalone binary, oc plugin, or ocm plugin? Affects discoverability and maintenance ownership.", owner: "OpenShift AI Team" },
    { q: "PSAP to Model Catalog Integration", detail: "How do YAML recipe files flow into the model card pipeline? What schema does the team need?", owner: "PSAP + Model Catalog" },
    { q: "Counterintuitive Batching Gain", detail: "10x increase in max_num_batched_tokens (8K to 80K) improves both throughput AND latency. Why exactly?", owner: "vLLM Tech Deep Dive" },
  ];

  for (let i = 0; i < questions.length; i++) {
    const y = 1.35 + i * 0.93;
    s.addShape("rect", { x: 0.35, y, w: 9.3, h: 0.83, fill: { color: "1E2124" }, line: { color: RH.medGray, width: 0.4 } });
    s.addShape("rect", { x: 0.35, y, w: 0.07, h: 0.83, fill: { color: RH.orange }, line: { color: RH.orange } });
    s.addText(`Q${i + 1}: ${questions[i].q}`, { x: 0.52, y: y + 0.06, w: 7, h: 0.28, fontSize: 12, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
    s.addText(questions[i].detail, { x: 0.52, y: y + 0.37, w: 7.4, h: 0.38, fontSize: 10, fontFace: "Arial", color: RH.lightGray, margin: 0 });
    s.addShape("rect", { x: 7.9, y: y + 0.2, w: 1.7, h: 0.42, fill: { color: "2A2A3A" }, line: { color: RH.purple, width: 0.5 } });
    s.addText(questions[i].owner, { x: 7.9, y: y + 0.2, w: 1.7, h: 0.42, fontSize: 8, color: RH.lightGray, fontFace: "Arial", align: "center", valign: "middle", margin: 0 });
  }
}
```

---

## 9. CTA / Closing

Red left panel on black bg. Right side: 3 action asks.

```javascript
{
  const s = pres.addSlide();
  s.background = { color: RH.black };

  // Red left panel
  s.addShape("rect", { x: 0, y: 0, w: 4.0, h: 5.625, fill: { color: RH.red }, line: { color: RH.red } });
  s.addText("What we\nneed today", { x: 0.2, y: 1.2, w: 3.6, h: 2.0, fontSize: 40, fontFace: "Arial", bold: true, color: RH.white, margin: 0 });
  s.addText("Three decisions.\nShips in one sprint.", { x: 0.2, y: 3.4, w: 3.6, h: 0.9, fontSize: 15, fontFace: "Arial", color: "FFB0B0", italic: true, margin: 0 });

  const asks = [
    { label: "Decide:", detail: "CLI vs Cursor vs Console — which ships Phase I?" },
    { label: "Assign:", detail: "Team DRI for CLI namespace and schema design" },
    { label: "Schedule:", detail: "PSAP integration sync with model catalog team" },
  ];

  for (let i = 0; i < asks.length; i++) {
    const y = 1.3 + i * 1.22;
    s.addShape("rect", { x: 4.3, y, w: 5.5, h: 1.05, fill: { color: RH.darkGray }, line: { color: RH.medGray, width: 0.5 } });
    s.addShape("rect", { x: 4.3, y, w: 0.07, h: 1.05, fill: { color: RH.orange }, line: { color: RH.orange } });
    s.addText(asks[i].label, { x: 4.47, y: y + 0.1, w: 5.1, h: 0.32, fontSize: 13, fontFace: "Arial", bold: true, color: RH.orange, margin: 0 });
    s.addText(asks[i].detail, { x: 4.47, y: y + 0.48, w: 5.1, h: 0.4, fontSize: 12, fontFace: "Arial", color: RH.lightGray, margin: 0 });
  }

  s.addText("Product Name · Red Hat · Month Year", { x: 4.3, y: 5.02, w: 5.5, h: 0.25, fontSize: 9, color: "555555", fontFace: "Arial", italic: true, margin: 0 });
  addFooter(s, pageNum);
}
```

---

## Tips: Phase II Pipeline Slides

For multi-step pipeline flows (Phase II dynamic tuning style):

```javascript
const steps = [
  { label: "1. Install\nProfiler Agent", color: RH.purple },
  { label: "2. Answer\nQuestionnaire", color: RH.blue },
  { label: "3. Baseline\n24-72h", color: RH.teal },
  { label: "4. Shadow\nA/B Test", color: RH.green },
  { label: "5. ROI\nReport", color: RH.orange },
  { label: "6. Promote\nor Queue", color: RH.red },
];

for (let i = 0; i < steps.length; i++) {
  const x = 0.3 + i * 1.6;
  s.addShape("rect", { x, y: 1.3, w: 1.45, h: 1.05, fill: { color: steps[i].color }, line: { color: steps[i].color } });
  s.addText(steps[i].label, { x, y: 1.3, w: 1.45, h: 1.05, fontSize: 10, fontFace: "Arial", bold: true, color: RH.white, align: "center", valign: "middle", margin: 0 });
  if (i < steps.length - 1) {
    s.addText("->", { x: x + 1.45, y: 1.65, w: 0.2, h: 0.35, fontSize: 16, color: RH.lightGray, margin: 0, fontFace: "Arial" });
  }
}
```
