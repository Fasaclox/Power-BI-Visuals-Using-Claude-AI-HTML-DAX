# Power-BI-Visuals-Using-Claude-AI-HTML-DAX

A collection of custom Power BI visuals built entirely from **DAX measures that
return HTML/CSS or SVG** — KPI cards, gauges, donuts, custom bar/column charts,
conditional-format status badges, category card grids, trend lines, and dynamic
narrative text. No paid custom visuals required (most render through the free
**HTML Content** visual, or Power BI's native Image URL data category).

The repo doubles as a **Claude skill** (`powerbi-html-visuals/`) so you can have
Claude generate the right DAX for your own model on demand.

## Claude Skill: `powerbi-html-visuals`

The `powerbi-html-visuals/` folder is a self-contained Claude skill. Its job is to
**write the HTML/CSS/DAX code** for a visual you describe — it does **not** build
the visual inside Power BI for you. You paste the generated measure into a visual
yourself.

### Install

**Claude Desktop / Claude.ai (Skills)**

1. Download this repo (green **Code → Download ZIP** button) and unzip it.
2. Re-zip **just the `powerbi-html-visuals` folder** so the archive contains
   `powerbi-html-visuals/SKILL.md` at its root.
3. In Claude, go to **Settings → Capabilities → Skills** and **upload** that zip.

**Claude Code**

Copy (or symlink) the `powerbi-html-visuals` folder into a skills directory:

- `~/.claude/skills/powerbi-html-visuals/` — available in every project, or
- `.claude/skills/powerbi-html-visuals/` — scoped to a single project.

The folder must contain `SKILL.md` directly inside it. Restart/reload so Claude
Code picks it up.

### How to use it

1. **Tell Claude the kind of visual you want** — by name ("KPI card with a
   target", "100% stacked bar", "status badge") or by intent ("progress to
   target", "compare two groups", "color-code this column"). This is required —
   it's how the skill knows which code to write.
2. **Give it your model details** when asked — the measure/column names,
   dimensions, and any thresholds it needs to bind to.
3. **Get back a ready-to-use DAX measure** plus short instructions on which visual
   to drop it into and how to bind it.
4. **In Power BI**, create the measure, add the target visual, and assign the
   measure:
   - **Full-canvas cards** (KPI cards, donuts, charts, grids) need the free
     **HTML Content** visual by **Daniel Marsh-Patrick**. Add it from **AppSource**
     — in Power BI Desktop, go to the **Visualizations** pane → **… (Get more
     visuals)** → search **"HTML Content"** → **Add**. Then drop the visual on the
     canvas and put the measure in its field well.
   - **Conditional-format badges** need no custom visual — add the measure to a
     **table/matrix** column and set its **Data category** to **Image URL**.

That's it — Claude handles the DAX/HTML; you place it in the report.
