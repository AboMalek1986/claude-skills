[CleanSlides_SKILL.md](https://github.com/user-attachments/files/27374537/CleanSlides_SKILL.md)
---
name: Clean Slides — Consulting-Style PowerPoint Generator-Abdelhamid
description: Inspect, edit, and generate clean consulting-style PowerPoint slides from YAML. Minimal information-dense tables with no graphics or effects — the way McKinsey slides are actually written. Use when asked to create structured argument slides, comparison tables, RAG status slides, or any text-heavy consulting deck. Built by an ex-McKinsey consultant.
author: Abdelhamid Fahim
version: 1.0.0
source: https://github.com/tmustier/clean-slides
license: MIT
---

# Clean Slides — PowerPoint Skill

> **Adapted by Abdelhamid Fahim** (GCC Marketing Manager, Pharmalink | Dubai, UAE)
> from the open-source project [`tmustier/clean-slides`](https://github.com/tmustier/clean-slides)
> by Thomas Mustier (ex-McKinsey consultant) — MIT License.
>
> Part of the [claude-skills](https://github.com/AboMalek1986/claude-skills) collection:
> open-source AI tools converted into reusable Claude SKILL.md files.

---

## What this skill does

Generates clean, consulting-grade `.pptx` table slides from YAML specs using the `pptx` CLI.
No graphics, no effects — just structured thinking in a clear visual format.
Built by someone who spent 6 years writing slides at McKinsey.

**The philosophy:** A good slide is a unit of structured argumentation that happens to be visual.
You think the thoughts, Claude structures them, the tool handles formatting.

**Trigger this skill when the user says:**
- "make me a clean consulting slide"
- "create a structured argument slide / comparison table"
- "build a RAG status table / assessment table"
- "generate a table slide from this data"
- "edit / inspect / fix my PowerPoint file"
- "create slides with no graphics, just structured text"
- Any request to produce minimal information-dense `.pptx` slides

**Use this instead of mckinsey-pptx when:**
- The slide is primarily a table or structured text (not charts/visuals)
- The user wants argument-driven slides, not visual decks
- You need to inspect or edit an existing `.pptx` file

---

## Installation (one time)

```bash
# Clone into your agent skills directory
git clone https://github.com/tmustier/clean-slides
cd clean-slides
pip install -e . --break-system-packages

# Initialize with bundled example template
pptx init

# Or with your own corporate template
pptx init -t path/to/your-template.pptx
```

Verify:
```bash
pptx --help
```

---

## Core workflow

### 1. Inspect → Edit → Verify

```bash
# Inspect — progressive drill-down
pptx show deck.pptx                  # slide list
pptx show deck.pptx 3                # shapes on slide 3
pptx show deck.pptx 3 5              # full detail for shape 5

# Edit
pptx edit deck.pptx 3 5 "New text" --out edited.pptx

# Verify visually
pptx render edited.pptx 3 --out images/
```

### 2. Generate table slides from YAML

Write a YAML spec, run one command, get a `.pptx`:

```bash
pptx generate slide.yaml -o output.pptx
pptx generate slide1.yaml slide2.yaml slide3.yaml -o output.pptx
```

---

## YAML spec format

```yaml
title: The potential scale of tokenization hinges on multiple factors.
subtitle: Three adoption scenarios, 2030, nonexhaustive

table:
  rows: 4
  cols: 4
  has_col_header: true
  has_row_header: true
  col_headers: ["Slower adoption", "Base scenario", "Accelerated adoption"]
  col_header_color: accent1
  column_widths: [0, 1, 1, 1]

  row_headers:
    - "Regulation"
    - "Infrastructure"
    - "Market demand"
    - "Risk"

  cells:
    - ["Regulatory challenges remain high", "Regional disparities exist", "Permissive regulation"]
    - ["Inadequate infrastructure", "Infrastructure reaches maturity", "Institutional-grade maturity"]
    - ["No solution to cold start problem", "Some assets tokenized at scale", "Cold start problem overcome"]
    - ["Systemic risk event occurs", "Limited security issues", ""]
```

**Top-level YAML keys:**

| Key | Purpose |
|-----|---------|
| `title` | Required — fills title placeholder |
| `subtitle` | Subtitle placeholder |
| `tracker` | On-page tracker/breadcrumb |
| `slide_layout` | Layout name e.g. `"Default"`, `"2/3"`, `"3/4"` |
| `content_layout` | `default` or `full` |
| `sidebar` | Text for secondary area in split layouts |

---

## Table features

### Inline formatting
```
**bold text**         → bold
*italic text*         → italic
[link text](url)      → clickable hyperlink
```

### Row overrides
```yaml
row_overrides:
  0:                  # first data row
    align: ctr
    anchor: ctr
    bold: true
    color: accent1
    size: 20
```

### Column widths
```yaml
column_widths: equal          # equal columns
column_widths: [0, 1, 2, 1]  # manual proportions (0 = auto row header)
```

### Icon indicators (RAG status)
Use `🟢`, `🟡`, `🔴` directly in cell text for traffic-light status indicators.

### Sidebar (split layouts)
```yaml
slide_layout: "2/3"
sidebar:
  - paragraphs:
    - runs:
      - text: "Key takeaway"
        bold: true
```

---

## All CLI commands

| Command | Purpose |
|---------|---------|
| `pptx generate <yaml...> -o out.pptx` | Generate slides from YAML |
| `pptx charts spec.json output.pptx` | Generate bar/stacked/waterfall charts (alpha) |
| `pptx validate <yaml...>` | Check YAML against schema |
| `pptx verify <yaml...>` | Check sizing and overflow |
| `pptx show <file> [slide] [shape]` | Inspect slides and shapes |
| `pptx layouts <file>` | Show template layouts and content areas |
| `pptx theme <file>` | Show colour scheme |
| `pptx edit <file> <slide> <shape> <text>` | Edit shape text |
| `pptx batch <file> edits.json` | Batch text edits |
| `pptx insert <deck> <source> [--at N]` | Merge slides from another file |
| `pptx render <file> <slides> [--out DIR]` | Render slides to PNG |
| `pptx crop <png> L T R B` | Crop a rendered PNG |
| `pptx add-slide <file> <layout>` | Add a blank slide |
| `pptx delete-slide <file> <slide>` | Delete a slide |
| `pptx delete-shape <file> <slide> <shape>` | Delete a shape |
| `pptx init [-t template]` | Bootstrap `.clean-slides/` project |
| `pptx init-config <template>` | Generate config from template |
| `pptx xml <file> <slide> <shape>` | Raw OOXML for debugging |

---

## Replace a slide in an existing deck

```bash
# 1. Generate the new table slide
pptx generate spec.yaml -o /tmp/new-slide.pptx

# 2. Insert at position 8 (pushes old slide 8 → 9)
pptx insert deck.pptx /tmp/new-slide.pptx --at 8 --out deck.pptx

# 3. Delete the old slide (now at position 9)
pptx delete-slide deck.pptx 9 --confirm --out deck.pptx

# 4. Verify
pptx render deck.pptx 8 --out renders/
```

---

## Custom corporate template

```bash
# Step 1 — inspect the template
pptx layouts my-template.pptx     # see layouts, placeholders, content areas
pptx theme my-template.pptx       # see color scheme

# Step 2 — generate starter config
pptx init-config my-template.pptx -o config.yaml

# Step 3 — put files in place
mkdir .clean-slides
cp my-template.pptx .clean-slides/template.pptx
cp config.yaml .clean-slides/config.yaml

# Step 4 — generate (auto-discovers .clean-slides/)
pptx generate spec.yaml -o output.pptx
```

Key config areas to verify after auto-generation:
- `colors` — check accent colors match `pptx theme` output
- `fonts` — may differ from theme fonts; inspect an actual slide
- `font_sizes` — defaults are conservative (12pt); adjust to template
- `placeholders` — verify title (usually 0) and subtitle (usually 1) indices
- `bullets` — if template has custom bullets, inspect the XML

---

## Pharma BD example — GCC market assessment table

```yaml
title: GCC rare disease market — competitive landscape assessment
subtitle: Four key markets evaluated across five criteria, May 2026

table:
  rows: 5
  cols: 5
  has_col_header: true
  has_row_header: true
  col_headers: ["KSA", "UAE", "Qatar", "Kuwait"]
  col_header_color: accent1
  column_widths: [0, 1, 1, 1, 1]

  row_headers:
    - "Market size (USD M)"
    - "Regulatory pathway"
    - "NUPCO / tender access"
    - "KOL landscape"
    - "Overall readiness"

  row_overrides:
    4:
      bold: true
      color: accent1

  cells:
    - ["$280M", "$95M", "$42M", "$38M"]
    - ["SFDA — 18–24 months", "MOHAP — 12–18 months", "MoPH — 12 months", "MOH — 18 months"]
    - ["🟢 NUPCO tender open", "🟡 DHA formulary only", "🟢 HMC direct", "🔴 No central tender"]
    - ["Strong — Dr. AlSayed KFSH", "Moderate", "Strong — Sidra Medicine", "Limited"]
    - ["**High priority**", "**Medium**", "**High priority**", "**Low**"]
```

---

## Backup policy

Always back up before editing:
```bash
cp deck.pptx deck.backup.pptx
pptx edit deck.pptx ... --out deck.edited.pptx
```

---

## Source & license

- **Original project:** [tmustier/clean-slides](https://github.com/tmustier/clean-slides)
- **Original author:** Thomas Mustier (ex-McKinsey consultant) — MIT License
- **SKILL.md adapted by:** Abdelhamid Fahim (Pharmalink / Medicina Group, Dubai UAE)
- **Part of:** [github.com/AboMalek1986/claude-skills](https://github.com/AboMalek1986/claude-skills)
- **Concept:** Converting open-source AI tools into reusable Claude SKILL.md files
