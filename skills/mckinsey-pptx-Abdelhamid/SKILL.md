---
name: McKinsey Style-Abdelhamid
description: Generates professional consulting-grade .pptx files in McKinsey style using Python. Picks from 40 slide templates (charts, matrices, timelines, KPI dashboards, exec summaries) based on the user's brief. Use when asked to create a pitch deck, strategy presentation, business review, or any consulting-style PowerPoint.
author: Abdelhamid Fahim
version: 1.0.0
source: https://github.com/seulee26/mckinsey-pptx
license: MIT
---

# McKinsey-Style PPTX Generator — Claude Skill

> **Adapted by Abdelhamid Fahim** (GCC Marketing Manager, Pharmalink | Dubai, UAE)
> from the open-source project [`seulee26/mckinsey-pptx`](https://github.com/seulee26/mckinsey-pptx)
> by AX Labs (이승필) — MIT License © 2026 AX Labs.

---

## What this skill does

Generates professional, consulting-grade `.pptx` files in McKinsey style using Python.
Claude reads the user's request, picks the right slide template(s) from a catalog of 40,
writes the Python builder code, runs it, and saves a `.pptx` file.

**Trigger this skill when the user says anything like:**
- "make me a McKinsey-style deck"
- "create a consulting presentation"
- "build a pitch deck / strategy deck / business review deck"
- "generate slides in BCG / McKinsey style"
- "turn my data into a professional PPT"
- Any request to produce a `.pptx` from bullet points, Excel data, or a text brief

---

## Installation (one time)

```bash
pip install python-pptx pillow --break-system-packages
pip install git+https://github.com/seulee26/mckinsey-pptx.git --break-system-packages
```

Verify:
```bash
python -m mckinsey_pptx.cli --list-types
```

---

## How to use this skill

### Step 1 — Understand the request
Read the user's brief. Identify:
- **Purpose:** pitch deck, QBR, market analysis, strategy, project plan, etc.
- **Audience:** C-suite, board, investors, internal team
- **Data provided:** raw numbers, bullet points, Excel/CSV, free text
- **Number of slides:** if not specified, 6–10 is a good default

### Step 2 — Select templates
Use the Template Catalog below to pick the right template for each slide.
Apply the quick decision rules to avoid mistakes.

### Step 3 — Write and run the builder code

```python
from mckinsey_pptx import PresentationBuilder
import os

os.makedirs("output", exist_ok=True)
b = PresentationBuilder(default_section_marker="<section name>")

# Add slides using b.add("<template_name>", **kwargs)

b.save("output/deck.pptx")
print("Saved: output/deck.pptx")
```

### Step 4 — Present the file
Tell the user the file path and offer to iterate.

---

## Template Catalog (40 templates)

All templates accept these **common optional kwargs**:
`title`, `page_number`, `section_marker`, `source`, `footnote`, `theme`

---

### EXECUTIVE SUMMARY

#### `executive_summary_paragraph`
Flowing narrative of 2–4 paragraphs. Use for written reports.
```python
b.add("executive_summary_paragraph",
      title="Executive summary",
      paragraphs=["Para 1...", "Para 2..."])
```

#### `executive_summary_takeaways`
2–4 bold takeaways, each with bullets. Most common McKinsey exec-summary pattern.
```python
b.add("executive_summary_takeaways",
      sections=[{"takeaway": "Market growing 22% YoY",
                 "bullets": ["NA share rising", "Europe stagnating"]}],
      final_conclusion="Recommend immediate action on 5 areas.")
```

#### `dark_navy_summary`
Single impact statement, full-bleed deep navy. Section opener or "one thing" moment.
```python
b.add("dark_navy_summary",
      body="[Bottom line]: The next 5 years will determine global leadership.",
      eyebrow="Strategy review")
```

---

### CHARTS — TIME SERIES

#### `column_simple_growth`
One metric over 5–10 periods, single CAGR arrow.
```python
b.add("column_simple_growth",
      categories=[2020,2021,2022,2023,2024],
      values=[100,115,120,135,150],
      growth_pct="10.7%")
```

#### `column_split_growth`
Two-phase growth with inflection point. Two growth arrows.
```python
b.add("column_split_growth",
      categories=[2014,2015,2016,2017,2018,2019,2020,2021,2022],
      values=[1035,1050,1060,1075,1150,1200,1320,1430,1535],
      split_index=4, growth_pct_first="2%", growth_pct_second="8%")
```

#### `column_historic_forecast`
Actuals (navy) vs forecast (blue) with two growth arrows.
```python
b.add("column_historic_forecast",
      categories=[2018,2019,2020,2021,2022,2023,2024,2025,2026],
      values=[1035,1108,1153,1148,1206,1265,1381,1430,1535],
      forecast_from_index=5,
      historic_growth="3%", forecast_growth="6%")
```

#### `line_chart`
1–4 lines over time. Multi-series KPI tracking.
```python
b.add("line_chart",
      categories=["Jan","Feb","Mar","Apr","May","Jun"],
      series=[{"name":"NA","values":[100,108,115,118,124,130]},
              {"name":"EU","values":[80,82,85,88,92,98]}])
```

---

### CHARTS — CATEGORICAL

#### `column_comparison`
Sorted bars high to low, one highlighted. Right-side takeaway pane.
```python
b.add("column_comparison",
      categories=["A","B","C","D","E"], values=[670,623,580,514,421],
      focus_index=1, takeaways=["B is our focus segment"])
```

#### `grouped_column_chart`
2–4 series side by side per category.
```python
b.add("grouped_column_chart",
      categories=["KSA","UAE","Kuwait","Qatar"],
      series=[{"name":"2023","values":[120,80,40,30]},
              {"name":"2024","values":[140,90,48,35]}])
```

#### `stacked_column_chart`
Composition over time — segments stacked per bar.
```python
b.add("stacked_column_chart",
      categories=[2022,2023,2024,2025,2026],
      series=[{"name":"Hospital","values":[100,140,180,230,290]},
              {"name":"Retail","values":[80,100,130,160,200]}])
```

---

### MATRIX / SCATTER

#### `bubble_chart`
5–15 entities on two continuous axes, bubble size = third dimension.
```python
b.add("bubble_chart",
      bubbles=[{"label":"P1","x":200,"y":1500,"size":2,"group":"blue_dark"},
               {"label":"P2","x":350,"y":800,"size":4,"group":"navy"}])
```

#### `bubble_chart_takeaways`
Same as bubble_chart plus right-side bullet pane.

#### `growth_share` (alias `bcg_matrix`)
BCG matrix. Market share (x) x growth rate (y).
```python
b.add("growth_share",
      bus=[{"name":"BU1","x":12,"y":37,"size":4},
           {"name":"BU2","x":55,"y":18,"size":6}])
```

#### `prioritization_matrix`
3x3 grid: time-to-impact x level-of-impact. Color-coded status per item.
```python
b.add("prioritization_matrix",
      items=[{"name":"Initiative A","x_band":2,"y_band":0,"status":"green"},
             {"name":"Initiative B","x_band":1,"y_band":1,"status":"amber"}])
```

---

### KPI / STATUS

#### `kpi_dashboard`
4–8 KPI tiles with big value, delta arrow, and context line.
```python
b.add("kpi_dashboard",
      kpis=[{"label":"Revenue","value":"$1.2B","delta":"+12% YoY","delta_dir":"up"},
            {"label":"Margin","value":"18%","delta":"+200 bps","delta_dir":"up"},
            {"label":"NPS","value":"42","delta":"flat","delta_dir":"flat"}])
```

#### `assessment_table`
KPIs by category with target vs actual and green/amber/red status.
```python
b.add("assessment_table",
      categories=[{"name":"GCC BU",
                   "rows":[{"kpi":"Market share","target":"15%","actual":"12%",
                             "status_label":"Behind","status":"red"}]}])
```

#### `stat_hero` (alias `big_number`)
Single huge statistic — the whole slide is one number.
```python
b.add("stat_hero",
      stat="$3.2B", stat_label="GCC rare disease market by 2028",
      context="Based on IQVIA MENA 2024 projections.")
```

---

### TRENDS / AREAS

#### `three_trends_icons`
Exactly 3 trends with circular icon and bullets.
```python
b.add("three_trends_icons",
      trends=[{"label":"Market growth","icon":"📈","bullets":["22% CAGR"]},
              {"label":"Regulation","icon":"⚖","bullets":["SFDA","MOH"]},
              {"label":"Competition","icon":"🏁","bullets":["3 new entrants"]}])
```

#### `three_trends_table`
3 trends with name, description bullets, and examples column.

#### `three_trends_numbered`
3 trends as numbered rows with bright blue label pills.

#### `five_key_areas`
Exactly 5 strategic areas, each with name pill and one-line description.
```python
b.add("five_key_areas",
      areas=[{"name":"KSA","description":"Largest GCC market, NUPCO tender opportunity"},
             {"name":"UAE","description":"Innovation hub, MOHAP early adoption"}])
```

#### `overview_areas`
5–7 column cards with header pill, letter badge (A–G), and bullets.

---

### COMPARISON / OPTIONS

#### `comparison_table` (alias `option_compare`)
2–4 options x 3–6 criteria. Harvey ball ratings (0–4). Optional recommended column.
```python
b.add("comparison_table",
      options=["Build","Buy","Partner"],
      criteria=[{"name":"Time to market","scores":[1,4,3]},
                {"name":"Capital","scores":[1,2,4]},
                {"name":"Strategic fit","scores":[4,2,3]}],
      recommended_index=2)
```

#### `pros_cons`
Green pros / red cons for ONE option.
```python
b.add("pros_cons",
      pros=["First-mover advantage","Strong KOL support"],
      cons=["High COGS","Reimbursement uncertainty"])
```

#### `two_column_compare` (alias `before_after`)
Before/After or As-is/To-be with connecting arrow.
```python
b.add("two_column_compare",
      left_label="Current state", right_label="Target state",
      left_items=["Single market focus","Reactive BD"],
      right_items=["Pan-GCC presence","Proactive in-licensing"])
```

---

### ORG / TEAM

#### `org_chart`
Reporting structure: CEO to heads to reports.

#### `project_team_circles`
Leader plus N teammates as labeled circles with icons.

#### `team_chart`
Function columns x role rows (filled/outline circles).

---

### TIMELINE / ROADMAP

#### `phases_chevron_3`
Exactly 3 phases as chevron arrows with deliverables and people.

#### `phases_table_4`
4 phases as parallel text columns.

#### `waves_timeline_4`
4 sequential waves on a horizontal arrow.

#### `gantt_timeline`
Multi-stream project plan across many weeks.
```python
b.add("gantt_timeline",
      weeks=list(range(1,13)),
      workstreams=[
          {"name":"Registration","start_week":1,"end_week":6,"color":"blue_dark"},
          {"name":"KOL engagement","start_week":3,"end_week":10,"color":"blue_light"},
      ],
      milestones=[{"week":6,"label":"SFDA approval"}])
```

#### `process_flow_horizontal` (alias `process_flow`)
4–6 sequential steps as numbered chevron tiles.
```python
b.add("process_flow_horizontal",
      steps=[{"name":"Discover","description":"Market & KOL research"},
             {"name":"Register","description":"SFDA/MOHAP dossier"},
             {"name":"Launch","description":"Go-to-market execution"},
             {"name":"Scale","description":"Tender & formulary listing"}])
```

#### `funnel`
TAM/SAM/SOM or conversion funnel.
```python
b.add("funnel",
      stages=[{"name":"TAM","value":"$3.2B","description":"Total GCC pharma market"},
              {"name":"SAM","value":"$420M","description":"Specialty segment"},
              {"name":"SOM","value":"$45M","description":"Year 3 target"}])
```

---

### HIERARCHY & IMPACT

#### `issue_tree`
Root issue to main drivers to secondaries to underlying causes.

#### `quote_slide` (alias `quote`)
Single expert or customer quote, large format.
```python
b.add("quote_slide",
      quote="Rare disease patients in the GCC wait an average of 4 years for diagnosis.",
      author="Dr. Moeenaldeen AlSayed",
      author_title="Head of Metabolic Diseases, KFSH Riyadh")
```

---

### STRUCTURAL SLIDES

#### `cover_slide` (alias `cover`)
First page. Title, subtitle, client, date, optional CONFIDENTIAL tag.
```python
b.add("cover_slide",
      title="GCC Rare Disease Market Entry",
      subtitle="Strategic partnership proposal",
      client="Lucane Pharma", date="June 2026",
      confidentiality="CONFIDENTIAL")
```

#### `section_divider`
Chapter break with giant number and title.
```python
b.add("section_divider", section_number="02",
      section_title="Market opportunity",
      subtitle="Size, growth, and unmet need")
```

#### `agenda`
Table of contents with optional active section highlight.
```python
b.add("agenda",
      items=["Market context","Competitive landscape",
             "Strategic options","Recommendation","Next steps"],
      active_index=0)
```

---

## Quick decision rules

| Situation | Template |
|-----------|----------|
| Single bold statement | `dark_navy_summary` |
| Structured takeaways with bullets | `executive_summary_takeaways` |
| Narrative paragraphs | `executive_summary_paragraph` |
| One huge number | `stat_hero` |
| 4-8 KPI tiles | `kpi_dashboard` |
| KPIs with target/actual/RAG | `assessment_table` |
| Time series, one growth rate | `column_simple_growth` |
| Time series, two phases | `column_split_growth` |
| Actuals + forecast | `column_historic_forecast` |
| Multi-series over time | `line_chart` |
| Sorted bars, one focus | `column_comparison` |
| Year-over-year by country | `grouped_column_chart` |
| Parts of whole over time | `stacked_column_chart` |
| 2D scatter with bubble size | `bubble_chart` |
| BCG market share x growth | `growth_share` |
| Impact x urgency 3x3 | `prioritization_matrix` |
| 3 themes with icons | `three_trends_icons` |
| 5 strategic areas | `five_key_areas` |
| 5-7 area cards | `overview_areas` |
| 2-4 options, Harvey balls | `comparison_table` |
| One option pros/cons | `pros_cons` |
| Before / After | `two_column_compare` |
| Reporting org | `org_chart` |
| 3 phases | `phases_chevron_3` |
| 4 phases | `phases_table_4` |
| 10+ week project plan | `gantt_timeline` |
| TAM/SAM/SOM funnel | `funnel` |
| Issue decomposition | `issue_tree` |
| Expert quote | `quote_slide` |
| First slide | `cover_slide` |
| Chapter break | `section_divider` |
| Table of contents | `agenda` |

---

## Source & license

- **Original project:** [seulee26/mckinsey-pptx](https://github.com/seulee26/mckinsey-pptx)
- **Original author:** AX Labs — Seungpil Lee — MIT © 2026
- **SKILL.md adapted by:** Abdelhamid Fahim 
- **Concept:** Converting open-source tools into reusable Claude SKILL.md files
