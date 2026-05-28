---
name: bluemodus-html-output
description: Use when producing client-facing deliverables (proposals, project plans, statements of work, executive summaries, scoping documents, recap reports) for BlueModus where the default would be markdown — outputs a self-contained HTML document styled with the BlueModus proposal design system instead.
---

# BlueModus HTML Output

## Overview

When the request is for a **deliverable document** (something a client or executive would read), produce a complete HTML file styled with the BlueModus design system, rather than markdown. The stylesheet supplies all typography, color, layout, dark/print themes, and component visuals — your job is only to compose semantic HTML using the documented class names.

**Stylesheet — single source of truth (do not bundle, copy, or fork):**
`https://bluemodus.github.io/design-refs/refs/60c3a446-2f52-402f-8686-847fc616dbf0/proposal-system.css`

**Live component reference (open to see rendered examples + copy-paste markup):**
`https://bluemodus.github.io/design-refs/refs/60c3a446-2f52-402f-8686-847fc616dbf0/`

## Required: include the stylesheet TWO ways

Every generated HTML output **must** include both:

1. A `<link rel="stylesheet" href="…proposal-system.css">` pointing at the hosted URL above, AND
2. A `<style>…</style>` block immediately after it containing the **full contents** of that stylesheet, fetched live from the hosted URL at the time you generate the document.

**Workflow:** Before emitting the HTML, fetch `https://bluemodus.github.io/design-refs/refs/60c3a446-2f52-402f-8686-847fc616dbf0/proposal-system.css` (web fetch / URL read tool — whichever is available in the current surface). Paste the entire response body verbatim into the `<style>` block. Do not abridge, paraphrase, summarize, or omit rules.

**Why both:** Claude's artifact-preview iframe applies a Content Security Policy that blocks external `<link>` stylesheet loads, so the preview renders unstyled if you only use `<link>`. Inlining via `<style>` makes the preview render correctly. Keeping the `<link>` preserves the canonical reference so the design system has a single source of truth — when the hosted CSS updates, the next generated document picks up the change.

**Never** emit a document with only the `<link>` and no inlined `<style>` — the preview iframe will render unstyled and the user will see a broken document. **Never** bundle, cache, or fork the CSS into the skill directory or another local file — always pull from the hosted URL at generation time.

## When to use

Use for:
- Proposals, SOWs, scoping documents
- Project plans, phase breakdowns, timelines
- Executive summaries, recap reports, retros
- Anything the user labels "client-facing", "deliverable", "polished", or asks to be exported to PDF

Do **not** use for:
- Conversational chat replies in the terminal
- Code answers, debugging output, file diffs
- Internal task lists, todos, scratch notes
- Anything the user asks for as markdown explicitly

## Document scaffold

Every output is a single self-contained `.html` file with this shell. Do not copy the style guide's `class="sg-mode"` or any `.sg-*` classes — those are viewer chrome.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>[Document Title]</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://bluemodus.github.io/design-refs/refs/60c3a446-2f52-402f-8686-847fc616dbf0/proposal-system.css">
<style>
  /* ===== Inlined for artifact-preview rendering (CSP blocks the <link> above). ===== */
  /* Fetch the hosted proposal-system.css URL and paste its FULL contents here verbatim. */
  /* Do not paraphrase, abridge, or "summarize" — emit the entire file. */
</style>
</head>
<body>
<main>
  <!-- content goes here -->
</main>
</body>
</html>
```

If the user wants the full proposal chrome (header, sidebar nav, footer pagination), look at the hosted reference page — those layout elements (`<header>`, `<nav>`, `.ftr`) are styled but optional. For most outputs `<main>` alone is correct.

## Section pattern

Every content section follows this rhythm:

```html
<div class="eyebrow">Section category</div>
<h2>Section title</h2>
<p class="intro">One- or two-sentence framing paragraph.</p>

<div class="section-divider"><h3>Sub-section</h3></div>
<p class="section-body">Body copy for this sub-section.</p>
```

## Component cheat sheet

Pick the component that matches the intent. Full markup for each is on the hosted reference page.

| Intent | Class | Notes |
|---|---|---|
| Cover/title block | `.hero` + `.hero-ey` / `.hero-sub` / `.hero-meta` | One per document, at top |
| Feature/value cards | `.card-grid` > `.card` | Auto-fit responsive grid |
| Before/after comparison | `.ba` > `.ba-col.before` / `.ba-col.after` | Two columns |
| Phase / step boxes | `.phases` (3-up) or `.phases-4` (4-up) > `.ph` | Teal left-accent |
| Checklist | `.chk` > `<li>` | Auto teal checkmark |
| Big stats (3-up) | `.exec-stats` > `.exec-stat` | Executive summary |
| Compact stats row | `.stats` > `.stat` | Inline metrics |
| Pull-quote / emphasis | `.callout` (tinted) or `.exec-callout` (framed quote) | One short paragraph |
| Numbered action list | `.ai-phases` > `.ai-ph` (+ optional `.ai-ph-icon`) | Steps or principles |
| Deliverables box | `.dlv` + `.dlv-title`; content as `<p>`, `.dlv-grid`, or `.dlv-list` w/ `.dlv-group-label` | Teal accent panel |
| Differentiator panel | `.why-feature` + `.why-feature-eyebrow` / `-title` / `-body` / `-chips` > `.why-chip` | Hero-style mid-doc |
| Table | `.ptable` (last row highlighted as total) or `.ptable-plain` | For budgets/scope |
| Timeline | `.gantt-wrapper` > `.gantt-table` + `.gantt-bar.active` / `.inactive` | See reference for cell pattern |
| Team / bios | `.person-grid-3` or `.person-grid-2` > `.person-card` | Avatar + role + bio |
| Case studies | `.case` (or `.case-grid` > `.case`) | Image-topped card with `.case-mets` |
| Section nav cards | `.ns-card` (+ `.ns-inline-link` for sub-references) | Linked sub-sections |

## Composition rules

- **Wrap all content in `<main>`.** The stylesheet expects this.
- **Use the eyebrow + h2 + intro opener once per major section.** Don't repeat for sub-sections — use `.section-divider` instead.
- **Teal is the only accent color.** Don't introduce new colors via inline styles; use the design tokens.
- **Avoid inline `style="..."` overrides** except for surgical fixes the reference page itself demonstrates (e.g. `style="margin:0;border-radius:0;border:none;"` on a `.hero` inside a constrained container).
- **Print-mode is automatic.** The stylesheet includes a `@media print` block that swaps to a light theme and adds page breaks at `.page` boundaries — wrap major sections in `<div class="page">` if the document is intended to be PDF-exported.
- **No JavaScript.** Outputs are static HTML documents.

## When in doubt

Open the hosted reference, find the component closest to the intent, copy the markup pattern, swap the placeholder copy. The reference is the source of truth; this skill is the index.
