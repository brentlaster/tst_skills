# Slide Design Conventions

These conventions are extracted from Brent Laster's existing training decks and must be
followed exactly for conference talk presentations.

## Color Palette

| Role | Color | Hex | Usage |
|------|-------|-----|-------|
| **Primary Dark** | Deep Navy | `011936` | Title slide background, section dividers |
| **Secondary Dark** | Dark Navy | `003366` | Section header backgrounds, emphasis blocks |
| **Tertiary Dark** | Navy | `002060` | Slide title text on light backgrounds |
| **Teal Accent** | Teal | `16A085` | Accent elements, icons, highlights |
| **Teal Secondary** | Seafoam | `00A79D` | Secondary accent, links, callout borders |
| **Text Dark** | Near Black | `1D1D1D` | Body text on light backgrounds |
| **Text Medium** | Dark Gray | `343A40` | Subtitle text, secondary content |
| **Background Light** | Warm Cream | `EBE3D0` | Section divider backgrounds (alternating) |
| **Background Neutral** | Off White | `F5F5F5` | Content slide backgrounds |
| **White** | White | `FFFFFF` | Text on dark backgrounds, card fills |
| **Blue Link** | Blue | `0070C0` | Hyperlinks, URL text |

### Color Usage Rules
- Title slide: `011936` background with `FFFFFF` text
- Section dividers: `003366` or `EBE3D0` background (alternate for variety)
- Content slides: `F5F5F5` or `FFFFFF` background
- Slide titles: `002060` on light backgrounds, `FFFFFF` on dark backgrounds
- Body text: `1D1D1D` on light backgrounds
- Teal (`16A085`) for icons, accent shapes, and emphasis — never for body text
- Ending slide: White background with `0070C0` contact links

## Typography

| Element | Font | Size (pt) | Weight | Color |
|---------|------|-----------|--------|-------|
| Slide title | Lato | 36 | Bold | `002060` (light bg) or `FFFFFF` (dark bg) |
| Section header | Lato | 30 | Bold | `FFFFFF` |
| Subtitle | Poppins | 20 | Regular | `343A40` |
| Body text | Poppins | 16-18 | Regular | `1D1D1D` |
| Bullet text | Poppins | 16 | Regular | `1D1D1D` |
| Code snippets | Consolas or Courier New | 14 | Regular | varies |
| Caption/citation | Poppins | 10-12 | Regular | `999999` |
| Big stat number | Lato | 60-72 | Bold | `16A085` |
| Stat label | Poppins | 14 | Regular | `343A40` |

### Font Notes
- Lato and Poppins are Google Fonts. When building with PptxGenJS, specify them by name.
  They render correctly in PowerPoint if installed on the system, and fall back gracefully.
- For code on slides, use `Consolas` (monospace) with a slightly tinted background rectangle
  behind the code block (`F0F0F0` fill with no border).

## Slide Templates

### 1. Version Slide (Slide 1)
- Small version tracking slide (e.g., "Version 1.0 | MM/DD/YY")
- Minimal — just the version text, small font, top-left

### 2. Title Slide (Slide 2)
- Background: `011936` (deep navy)
- Talk title: Large `FFFFFF` Lato Bold, centered
- Subtitle (if any): `FFFFFF` Poppins, below title
- "Presented by Brent Laster &" in smaller white text
- "Tech Skills Transformations LLC" below
- Copyright line: "© [year] Brent C. Laster & Tech Skills Transformations LLC"
- "All rights reserved" below copyright
- Brent's profile image (from assets if available)
- Company/TM logos (from assets if available)

### 3. About Me Slide (Slide 3 — optional for conferences)
- Include if the talk is at a conference where Brent isn't already known
- Career highlights, company info, book covers, social links
- Layout: Photo left, text right, book covers along bottom

### 4. Section Divider Slides
- Background: `003366` (dark navy) or `EBE3D0` (warm cream), alternating
- Section title: Large centered text
- Optional subtitle describing what's coming

### 5. Content Slides — Layout Variety
Rotate through these patterns. Never use the same layout for consecutive slides:

**a) Text + Visual (two-column)**
- Left 50%: Title + bullet points or short paragraphs
- Right 50%: Diagram, chart, screenshot, or stock image

**b) Icon Row / Card Grid**
- 3 or 4 items in a row, each with:
  - Icon in colored circle (`16A085` background)
  - Bold header
  - 1-2 line description
- Works well for "3 key takeaways", "4 pillars", comparison items

**c) Big Stat + Context**
- One large number (60-72pt, `16A085`)
- Brief label below the number
- Supporting context text to the side
- Citation in small text at bottom

**d) Code Example**
- Slide title at top
- Code in monospace font on tinted background rectangle
- Brief annotation to the side or below explaining what the code does
- Keep code to 8-12 lines max — audience can't read more

**e) Process / Timeline**
- Horizontal flow of 3-5 steps
- Each step: numbered circle or icon → label → brief description
- Connected by arrows or a line

**f) Comparison / Before-After**
- Two columns: "Before" and "After" (or "Without" / "With", "Traditional" / "Modern")
- Each column with its own background tint
- Matching items aligned horizontally

**g) Quote / Callout**
- Large quote text in italics
- Attribution below
- Teal accent bar on left side
- Works well for industry expert quotes or surprising findings

**h) Full-Image with Overlay**
- Full-bleed stock image
- Semi-transparent dark overlay (`011936` at 70% opacity)
- White text overlaid
- Use sparingly — 1-2 per talk for high-impact moments

### 6. Ending Slide (Last Slide)
- Title: "That's all - thanks!" in Lato Bold
- Contact: "Contact: training@getskillsnow.com"
- Book cover images (Professional Git, Learning GitHub Copilot, Learning GitHub Actions)
- Website URLs: techskillstransformations.com, getskillsnow.com
- Social links if room
- Light background with blue accent text

## Visual Approach Hierarchy

For each slide, choose the most appropriate visual type in this priority order:

1. **Diagrams** (architecture, flow, process) — generate programmatically using SVG or
   canvas, then save as PNG. Best for technical concepts, system architectures, workflows.

2. **Charts** (bar, line, pie) — use PptxGenJS native chart support. Best for data,
   statistics, comparisons, trends.

3. **Code examples** — monospace text on tinted background. Best for technical "how-to"
   slides, API examples, configuration snippets.

4. **Icons** — use react-icons (Font Awesome, Material Design, Heroicons) rendered to
   PNG via sharp. Best for concept cards, feature lists, process steps.

5. **Stock images** — search for relevant, high-quality images using web search (look for
   freely licensed or create descriptive placeholder references). Best for emotional
   impact, scene-setting, full-bleed backgrounds.

When generating diagrams programmatically:
- Use Node.js canvas or SVG generation, render to PNG
- Match the color palette (use the teal accent, navy, and white)
- Keep diagrams clean with plenty of whitespace
- Label everything — audiences see diagrams for only seconds

## Citation Format

Every slide that contains a statistic, study result, or attributed claim must have a
citation. Format:

```
Source: [Author/Org], [Year]
```

Place in the bottom-right of the slide, Poppins 10pt, color `999999`.

Examples:
- `Source: McKinsey, 2025`
- `Source: Stack Overflow Developer Survey, 2025`
- `Source: Gartner Hype Cycle, 2025`

In the speaker notes for that slide, include the full URL.

## Spacing & Layout Rules

- Slide dimensions: 10" × 5.625" (16:9)
- Minimum margins: 0.5" on all sides
- Gap between content blocks: 0.3-0.5"
- Slide title area: top 1" of slide
- Content area: 1.0" to 5.0" (vertical)
- Citation area: bottom 0.3" of slide
- Don't crowd slides — leave breathing room
- Left-align body text, center only titles and single-line items
