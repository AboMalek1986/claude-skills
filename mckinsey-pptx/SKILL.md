# McKinsey-Style PPTX Generator — Claude Skill

> **Adapted by Abdelhamid Fahim** (GCC Marketing Manager, Pharmalink | Dubai, UAE)
> from the open-source project [`seulee26/mckinsey-pptx`](https://github.com/seulee26/mckinsey-pptx)
> by AX Labs (이승필) — MIT License © 2026 AX Labs.
>
> Converted into a Claude SKILL.md for use in Claude.ai and Claude Desktop.
> Original concept: reverse-engineer open-source tools into reusable Claude skill files.

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
Use the **Template Catalog** below to pick the right template for each slide.
Apply the "Choosing between similar templates" rules to avoid mistakes.

### Step 3 — Write and run the builder code

```python
from mckinsey_pptx import PresentationBuilder

b = PresentationBuilder(default_section_marker="<section name>")

# Add slides using b.add("<template_name>", **kwargs)
# See catalog below for required/optional kwargs per template

b.save("output/deck.pptx")
```

Always save to `output/deck.pptx` (or a path the user specifies).
Create the `output/` folder if it doesn't exist: `import os; os.makedirs("output", exist_ok=True)`

### Step 4 — Present the file
Tell the user the file path and offer to iterate ("want to change any slide?").

---

## Template Catalog (40 templates)

All templates accept these **common optional kwargs** (omit unless useful):
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
2–4 bold takeaways, each with bullets. The most common McKinsey exec-summary pattern.
```python
b.add("executive_summary_takeaways",
      sections=[{"takeaway": "Market growing 22% YoY",
                 "bullets": ["NA share rising", "Europe stagnating"]}],
      final_conclusion="Recommend immediate action on 5 areas.")
```

#### `dark_navy_summary`
Single impact statement, full-bleed deep navy. Use as a section opener or "one thing" moment.
```python
b.add("dark_navy_summary",
      body="[Bottom line]: The next 5 years will determine global leadership in EV batteries.",
      eyebrow="K-battery global strategy")
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
Two-phase growth (inflection point). Two growth arrows.
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
1–4 lines over time. Use for multi-series KPI tracking.
```python
b.add("line_chart",
      categories=["Jan","Feb","Mar","Apr","May","Jun"],
      series=[{"name":"NA","values":[100,108,115,118,124,130]},
              {"name":"EU","values":[80,82,85,88,92,98]}])
```

---

### CHARTS — CATEGORICAL

#### `column_comparison`
Sorted bars (high to low), one highlighted in bright blue. Right-side takeaway pane.
```python
b.add("column_comparison",
      categories=["A","B","C","D","E"], values=[670,623,580,514,421],
      focus_index=1, takeaways=["B is our focus segment"])
```

#### `grouped_column_chart`
2–4 series side by side per category (e.g. year-over-year by country).
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
               {"label":"P2","x":350,"y":800, "size":4,"group":"navy"}])
```

#### `bubble_chart_takeaways`
Same as above + right-side bullet pane.

#### `growth_share` (alias `bcg_matrix`)
BCG matrix. Market share (x) × growth rate (y). Bubbles = BUs.
```python
b.add("growth_share",
      bus=[{"name":"BU1","x":12,"y":37,"size":4},
           {"name":"BU2","x":55,"y":18,"size":6}])
```

#### `prioritization_matrix`
3×3 grid: time-to-impact × level-of-impact. Color-coded status per item.
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
Exactly 3 trends with circular icon + bullets.
```python
b.add("three_trends_icons",
      trends=[{"label":"Market growth","icon":"📈","bullets":["22% CAGR","..."]},
              {"label":"Regulation","icon":"⚖","bullets":["SFDA","MOH"]},
              {"label":"Competition","icon":"🏁","bullets":["3 new entrants"]}])
```

#### `three_trends_table`
3 trends with name, description bullets, and examples column.

#### `three_trends_numbered`
3 trends as numbered rows with bright blue label pills.

#### `five_key_areas`
Exactly 5 strategic areas, each with name pill + one-line description.
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
2–4 options × 3–6 criteria. Harvey ball ratings (0–4). Optional recommended column.
```python
b.add("comparison_table",
      options=["Build","Buy","Partner"],
      criteria=[{"name":"Time to market","scores":[1,4,3]},
                {"name":"Capital","scores":[1,2,4]},
                {"name":"Strategic fit","scores":[4,2,3]}],
      recommended_index=2)
```

#### `pros_cons`
Green ✓ pros / red ✗ cons for ONE option.
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
Reporting structure: CEO → heads → reports.

#### `project_team_circles`
Leader + N teammates as labeled circles with icons.

#### `team_chart`
Function columns × role rows (filled/outline circles).

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
      weeks=[1,2,3,4,5,6,7,8,9,10,11,12],
      workstreams=[{"name":"Registration","start_week":1,"end_week":6,"color":"blue_dark"},
                   {"name":"KOL engagement","start_week":3,"end_week":10,"color":"blue_light"}],
      milestones=[{"week":6,"label":"SFDA approval"}])
```

#### `process_activities`
3–4 time blocks with activities, management interaction, and deliverables.

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
TAM/SAM/SOM or conversion funnel. Narrowing bands.
```python
b.add("funnel",
      stages=[{"name":"TAM","value":"$3.2B","description":"Total GCC pharma market"},
              {"name":"SAM","value":"$420M","description":"Specialty segment"},
              {"name":"SOM","value":"$45M","description":"Year 3 target"}])
```

---

### HIERARCHY

#### `issue_tree`
Root issue → main drivers → secondaries → underlying causes.

#### `quote_slide` (alias `quote`)
Single customer or expert quote, large format.
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
Chapter break. Giant number + title.
```python
b.add("section_divider", section_number="02",
      section_title="Market opportunity",
      subtitle="Size, growth, and unmet need")
```

#### `agenda`
Table of contents. Optional active section highlight.
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
| 4–8 KPI tiles | `kpi_dashboard` |
| KPIs with target/actual/RAG | `assessment_table` |
| Time series, one growth rate | `column_simple_growth` |
| Time series, two phases | `column_split_growth` |
| Actuals + forecast | `column_historic_forecast` |
| Multi-series over time | `line_chart` |
| Sorted bars, one focus | `column_comparison` |
| Year-over-year by country | `grouped_column_chart` |
| Parts of whole over time | `stacked_column_chart` |
| 2D scatter, bubble size | `bubble_chart` |
| BCG market share × growth | `growth_share` |
| Impact × urgency 3×3 | `prioritization_matrix` |
| 3 themes with icons | `three_trends_icons` |
| 5 strategic areas | `five_key_areas` |
| 5–7 area cards | `overview_areas` |
| 2–4 options, Harvey balls | `comparison_table` |
| One option +/− | `pros_cons` |
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

## Custom theme (optional)

```python
from mckinsey_pptx import DEFAULT_THEME, PresentationBuilder
from mckinsey_pptx.theme import Typography
from dataclasses import replace

CUSTOM_THEME = replace(
    DEFAULT_THEME,
    typography=replace(DEFAULT_THEME.typography, family="Calibri"),
    copyright_text="© 2026 Pharmalink",
)
b = PresentationBuilder(theme=CUSTOM_THEME, default_section_marker="Pharmalink BD")
```

---

## Full example — pharma BD pitch deck

```python
import os
from mckinsey_pptx import PresentationBuilder

os.makedirs("output", exist_ok=True)
b = PresentationBuilder(default_section_marker="Pharmalink – Lucane Pharma")

b.add("cover_slide",
      title="GCC Rare Disease Market Opportunity",
      subtitle="Pheburane & Ucedane — Partnership Proposal",
      client="Lucane Pharma", date="June 2026",
      confidentiality="CONFIDENTIAL")

b.add("agenda",
      items=["Market overview","Regulatory landscape",
             "Commercial opportunity","Partnership model","Next steps"])

b.add("dark_navy_summary",
      body="[Bottom line]: The GCC rare metabolic disease market is underserved, SFDA-registered, and ready for a focused commercial partner.")

b.add("executive_summary_takeaways",
      sections=[
          {"takeaway":"$420M addressable GCC rare disease market",
           "bullets":["KSA accounts for 60%","NUPCO tender NPT0017/25 open"]},
          {"takeaway":"Pheburane & Ucedane are SFDA-registered",
           "bullets":["No active local promotion","Clear KOL engagement path"]},
      ],
      final_conclusion="Pharmalink is the right partner to unlock this opportunity.")

b.add("column_historic_forecast",
      title="GCC rare disease market — SAR millions",
      categories=[2020,2021,2022,2023,2024,2025,2026,2027,2028],
      values=[180,200,225,260,300,345,390,440,500],
      forecast_from_index=4,
      historic_growth="13%", forecast_growth="15%",
      section_marker="Market overview")

b.add("funnel",
      title="Market sizing approach",
      stages=[{"name":"TAM","value":"$3.2B","description":"Total GCC pharma market"},
              {"name":"SAM","value":"$420M","description":"Rare & specialty diseases"},
              {"name":"SOM","value":"$45M","description":"Year 3 Pharmalink target"}])

b.add("gantt_timeline",
      title="Launch roadmap — 12 months",
      weeks=list(range(1,13)),
      workstreams=[
          {"name":"SFDA/MOHAP listing","start_week":1,"end_week":4,"color":"blue_dark"},
          {"name":"KOL engagement","start_week":2,"end_week":8,"color":"blue_light"},
          {"name":"NUPCO tender submission","start_week":5,"end_week":9,"color":"royal"},
          {"name":"Commercial launch","start_week":9,"end_week":12,"color":"navy"},
      ],
      milestones=[{"week":4,"label":"Listing confirmed"},
                  {"week":9,"label":"First tender awarded"}])

b.add("pros_cons",
      title="Why Pharmalink vs. alternatives",
      pros=["GCC-wide presence (6 markets)","Existing MOHAP & SFDA relationships",
            "Rare disease commercial experience","Aguettant hospital channel access"],
      cons=["No current Lucane product in portfolio","Ramp-up period 6–9 months"])

b.add("cover_slide",
      title="Thank you",
      subtitle="Abdelhamid Fahim | abdelhamid@pharmalink.ae",
      date="June 2026")

b.save("output/lucane_pharma_pitch.pptx")
print("Saved: output/lucane_pharma_pitch.pptx")
```

---

## Source & license

- **Original project:** [seulee26/mckinsey-pptx](https://github.com/seulee26/mckinsey-pptx)
- **Original author:** AX Labs — 이승필 (Seungpil Lee)
- **License:** MIT © 2026 AX Labs
- **SKILL.md adapted by:** Abdelhamid Fahim 
- **Adaptation concept:** Converting open-source tools into reusable Claude SKILL.md files

Include this attribution line in any public fork or republication:
```
SKILL.md by Abdelhamid Fahim — based on seulee26/mckinsey-pptx (MIT © 2026 AX Labs)
```
