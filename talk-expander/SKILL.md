---
name: talk-expander
description: >
  Expand a conference talk deck by adding slides that surface key talking points from the speaker
  script, reducing memorization burden. Takes a .pptx deck and matching .md script with SLIDE
  delimiters, finds sections hardest to deliver from memory (stats, case studies, lists), and
  inserts visually attractive new slides showing those key points. Updates the script with new
  SLIDE delimiters to stay in sync. Creates new file versions. Trigger on: "expand my slides",
  "can't memorize this talk", "add more slides from script", "break up dense slides", "too much
  to memorize", "put talking points on slides", "expand the deck", "talk expander", or any
  request to reduce memorization by adding slides with key script content. Also trigger when
  someone has too much script per slide or wants their deck to carry more content visually.
  Do NOT use for speaker notes only (use presentation-speaker-cues) or creating talks from
  scratch (use conference-talk-builder).
---

# Talk Expander Skill

Expand a conference talk deck by inserting new slides that surface key talking points from the
speaker script, so the speaker can read/deduce content from their slides rather than memorize
long script passages.

## Philosophy

The speaker has a polished deck and a detailed script synced by SLIDE delimiters. The problem:
too much script per slide means too much memorization. The solution: split dense script sections
across more slides, each displaying the key points the speaker needs to convey. The audience
sees relevant, well-designed content slides. The speaker sees their talking-point cues right
on screen.

New slides should look like natural, intentional content slides that add value for the audience
— not cheat sheets or teleprompter screens. Think of it as "unpacking" a dense slide into a
short sequence where each slide focuses on one idea, with the key phrases and data points
visible.

## Before You Start

1. **Collect inputs**: You need a `.pptx` deck and a `.md` script file with SLIDE delimiters.
2. **Confirm output naming**: Create new versions with a suffix like `_expanded` — never
   overwrite the originals.
3. **Read this skill fully before starting** — especially the Phase 3 section on deck
   modification, which contains critical technical guidance that prevents file corruption.

## Step-by-Step Process

### Phase 1: Analyze the Deck and Script

1. **Extract deck content using python-pptx** (read-only — see Phase 3 for why):
   ```python
   from pptx import Presentation
   prs = Presentation('input.pptx')
   for i, slide in enumerate(prs.slides, 1):
       for shape in slide.shapes:
           if shape.has_text_frame:
               print(f"Slide {i}: {shape.text_frame.text[:80]}")
   ```
   If markitdown is available, you can also try `python -m markitdown input.pptx`, but be
   aware it may fail on some decks. Always have python-pptx as a fallback for reading.

2. **Read the full script** and parse it into sections by SLIDE delimiter. Detect the delimiter
   format used. Common variants include:
   - `## [SLIDE N — Title]`
   - `## SLIDE N: TITLE`
   - `## **SLIDE N — Title**`
   The delimiter format varies between talks. Detect the actual format and use it consistently.

3. **Map presentation order to file names** — this is critical because slide file names inside
   the .pptx (slide1.xml, slide5.xml, slide37.xml) do NOT correspond to display order. You must
   read `ppt/presentation.xml` for the `<p:sldIdLst>` element, which defines the actual
   presentation order, and cross-reference with `ppt/_rels/presentation.xml.rels` to get the
   file-to-position mapping.

4. For each slide section, assess:
   - **Word count** of the script section
   - **Memorization difficulty**:
     - HIGH: Contains specific statistics, multiple case studies, technical configurations,
       ordered lists of 4+ items, code examples, or citations
     - MEDIUM: Contains 2-3 key points with some specifics, comparisons, or scenarios
     - LOW: Narrative flow, transitions, audience interactions, or content already well
       represented on the existing slide

5. Build the **expansion plan** — a table showing:
   - Each original slide number and title
   - Script word count for that section
   - Memorization difficulty rating
   - Recommended action (keep as-is, or expand with description of new slide)

   Expansion criteria (use judgment, not rigid rules):
   - Slides with HIGH difficulty and 150+ words of script are strong expansion candidates
   - Slides with MEDIUM difficulty and 200+ words may benefit from splitting
   - Slides with LOW difficulty generally stay as-is
   - Title slides, Q&A slides, section dividers, and audience interaction slides stay as-is
   - Don't expand so aggressively that transitions feel choppy

6. **Present the expansion plan to the user** for approval before making changes.

### Phase 2: Design the New Slides

For each expansion point, design the new slide content:

**What goes on new slides:**
- Key statistics with their context (e.g., "+14% collaboration time" with source)
- Individual case studies or examples that were bundled into one script section
- Steps in a process, shown one or two per slide instead of all at once
- Comparison columns (before/after, problem/solution)
- Key quotes or principles the speaker needs to deliver verbatim
- Code snippets or configuration examples mentioned in the script

**Visual approach — match the existing deck's style:**
Before creating any new slides, study the existing deck's visual language. Specifically extract:
- Background color (check `<p:bg>` in slide XML for `<a:solidFill>`)
- Title font, size, color, and position
- Body font, size, color
- Accent elements (colored bars, card backgrounds, shadows)
- Slide number format, font, position, and color
- Any recurring layout patterns (narrative cards, stat callouts, two-column layouts)

New slides must use identical fonts, colors, and positioning so they blend seamlessly into the
deck. The audience should not be able to tell which slides are original and which are new.

**Content limits:**
- Maximum 4-5 text elements per slide; if you need more, split again
- No more than 30-40 words of visible text per slide
- No full paragraphs from the script — these are visual cue slides, not teleprompter screens
- No audience interaction instructions on slides (keep those in the script only)

### Phase 3: Build the Expanded Deck

**THIS IS THE MOST CRITICAL SECTION. Read it fully before writing any code.**

#### Why python-pptx Cannot Be Used for Saving

python-pptx rewrites nearly every file in the .pptx zip archive when it saves, even if you
change nothing. In testing, a zero-change open-and-save with python-pptx modified 168 out of
212 internal files, dropping thousands of bytes from Content_Types.xml and individual slide
XMLs. This causes PowerPoint to show "found a problem with content" repair dialogs, delete
content, and produce duplicate slides.

**python-pptx is safe for READ-ONLY operations** (Phase 1 analysis). It must NEVER be used to
save the final deck. All deck modification must use direct ZIP/XML manipulation.

#### The Correct Approach: Direct ZIP/XML Manipulation

The approach: copy the original .pptx byte-for-byte as a zip archive, surgically modify only
the 3 XML files that need updating, and add new slide files. Every original file is preserved
exactly. Here's the complete pattern:

```python
import zipfile
import io
from lxml import etree

INPUT = 'original.pptx'
OUTPUT = 'expanded.pptx'

# Namespaces
NS_RELS = 'http://schemas.openxmlformats.org/package/2006/relationships'
NS_CT = 'http://schemas.openxmlformats.org/package/2006/content-types'
NS_P = 'http://schemas.openxmlformats.org/presentationml/2006/main'
NS_A = 'http://schemas.openxmlformats.org/drawingml/2006/main'
NS_R = 'http://schemas.openxmlformats.org/officeDocument/2006/relationships'

SLIDE_CT = 'application/vnd.openxmlformats-officedocument.presentationml.slide+xml'
SLIDE_REL_TYPE = 'http://schemas.openxmlformats.org/officeDocument/2006/relationships/slide'

# Read original into memory
with open(INPUT, 'rb') as f:
    orig_data = f.read()
orig_zip = zipfile.ZipFile(io.BytesIO(orig_data))

out_buf = io.BytesIO()
out_zip = zipfile.ZipFile(out_buf, 'w', zipfile.ZIP_DEFLATED)

# Files we will modify (only these 3)
MODIFIED_FILES = {
    '[Content_Types].xml',
    'ppt/presentation.xml',
    'ppt/_rels/presentation.xml.rels',
}

# Step 1: Copy ALL original files byte-for-byte, except the 3 we modify
for item in orig_zip.infolist():
    if item.filename in MODIFIED_FILES:
        continue
    data = orig_zip.read(item.filename)
    info = zipfile.ZipInfo(item.filename)
    info.compress_type = zipfile.ZIP_DEFLATED
    out_zip.writestr(info, data)
```

#### Determining IDs for New Slides

Before creating slides, scan the original for the maximum existing IDs:

```python
# Find max slide file number (for naming new files)
existing_slides = [n for n in orig_zip.namelist()
                   if n.startswith('ppt/slides/slide') and n.endswith('.xml')]
max_slide_num = max(int(n.split('slide')[1].split('.')[0]) for n in existing_slides)

# Find max rId in presentation.xml.rels
rels_data = orig_zip.read('ppt/_rels/presentation.xml.rels')
rels_tree = etree.fromstring(rels_data)
all_rels = rels_tree.findall(f'{{{NS_RELS}}}Relationship')
max_rId = max(int(r.get('Id').replace('rId', '')) for r in all_rels)

# Find max sldId in presentation.xml
pres_data = orig_zip.read('ppt/presentation.xml')
pres_tree = etree.fromstring(pres_data)
sldIdLst = pres_tree.find(f'{{{NS_P}}}sldIdLst')
max_sldId = max(int(sid.get('id')) for sid in sldIdLst)
```

Then assign new IDs sequentially: `slide{max_slide_num + 1}.xml`, `rId{max_rId + 1}`,
sldId `{max_sldId + 1}`, etc.

#### Creating New Slide XML

Each new slide needs two files:

1. **The slide XML** (`ppt/slides/slideNN.xml`) — built from scratch matching the deck's
   visual theme. Structure follows this pattern:
   ```xml
   <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
   <p:sld xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main"
          xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships"
          xmlns:p="http://schemas.openxmlformats.org/presentationml/2006/main">
     <p:cSld name="Slide Label">
       <p:bg><p:bgPr><a:solidFill><a:srgbClr val="F5F5F5"/></a:solidFill></p:bgPr></p:bg>
       <p:spTree>
         <p:nvGrpSpPr><p:cNvPr id="1" name=""/><p:cNvGrpSpPr/><p:nvPr/></p:nvGrpSpPr>
         <p:grpSpPr>
           <a:xfrm><a:off x="0" y="0"/><a:ext cx="0" cy="0"/>
                   <a:chOff x="0" y="0"/><a:chExt cx="0" cy="0"/></a:xfrm>
         </p:grpSpPr>
         <!-- shapes go here -->
       </p:spTree>
     </p:cSld>
     <p:clrMapOvr><a:masterClrMapping/></p:clrMapOvr>
   </p:sld>
   ```

2. **The slide rels** (`ppt/slides/_rels/slideNN.xml.rels`) — minimal, just the layout ref:
   ```xml
   <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
   <Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
     <Relationship Id="rId1"
       Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/slideLayout"
       Target="../slideLayouts/slideLayout1.xml"/>
   </Relationships>
   ```

Study the existing slides to determine which slideLayout to reference. Most decks use
`slideLayout1.xml` but check the existing slides' rels files to confirm.

#### Shape XML Patterns

Shapes in slides follow this general pattern. Use exact positions, sizes, fonts, and colors
extracted from the existing deck in Phase 2:

**Text shape (title, body, source text):**
```xml
<p:sp>
  <p:nvSpPr><p:cNvPr id="N" name="Text N"/><p:cNvSpPr/><p:nvPr/></p:nvSpPr>
  <p:spPr>
    <a:xfrm><a:off x="LEFT" y="TOP"/><a:ext cx="WIDTH" cy="HEIGHT"/></a:xfrm>
    <a:prstGeom prst="rect"><a:avLst/></a:prstGeom><a:noFill/><a:ln/>
  </p:spPr>
  <p:txBody>
    <a:bodyPr wrap="square" rtlCol="0" anchor="t"/>
    <a:lstStyle/>
    <a:p><a:pPr indent="0" marL="0"><a:buNone/></a:pPr>
      <a:r><a:rPr lang="en-US" sz="SIZE" b="1" dirty="0">
        <a:solidFill><a:srgbClr val="COLOR"/></a:solidFill>
        <a:latin typeface="FONT" pitchFamily="34" charset="0"/>
      </a:rPr><a:t>Text content here</a:t></a:r>
    </a:p>
  </p:txBody>
</p:sp>
```

**Filled rectangle (accent bars, card backgrounds):**
```xml
<p:sp>
  <p:nvSpPr><p:cNvPr id="N" name="Shape N"/><p:cNvSpPr/><p:nvPr/></p:nvSpPr>
  <p:spPr>
    <a:xfrm><a:off x="LEFT" y="TOP"/><a:ext cx="WIDTH" cy="HEIGHT"/></a:xfrm>
    <a:prstGeom prst="rect"><a:avLst/></a:prstGeom>
    <a:solidFill><a:srgbClr val="COLOR"/></a:solidFill><a:ln/>
  </p:spPr>
</p:sp>
```

**Adding a drop shadow to a shape (for card backgrounds):**
```xml
<a:effectLst>
  <a:outerShdw blurRad="76200" dist="25400" dir="8100000" algn="bl" rotWithShape="0">
    <a:srgbClr val="000000"><a:alpha val="12000"/></a:srgbClr>
  </a:outerShdw>
</a:effectLst>
```

Always XML-escape text content (`&amp;`, `&lt;`, `&gt;`, `&quot;`).

#### Registering New Slides in the Archive

After creating the slide XML and rels content, you need to update 3 files and write the
new files:

```python
# 1. Update [Content_Types].xml — add Override entries for each new slide
ct_data = orig_zip.read('[Content_Types].xml')
ct_tree = etree.fromstring(ct_data)
for new_slide in new_slides:
    el = etree.SubElement(ct_tree, f'{{{NS_CT}}}Override')
    el.set('PartName', f'/ppt/slides/{new_slide["filename"]}')
    el.set('ContentType', SLIDE_CT)
out_zip.writestr('[Content_Types].xml',
    etree.tostring(ct_tree, xml_declaration=True, encoding='UTF-8', standalone=True))

# 2. Update ppt/_rels/presentation.xml.rels — add Relationship entries
rels_tree = etree.fromstring(rels_data)
for new_slide in new_slides:
    el = etree.SubElement(rels_tree, f'{{{NS_RELS}}}Relationship')
    el.set('Id', new_slide['rId'])
    el.set('Type', SLIDE_REL_TYPE)
    el.set('Target', f'slides/{new_slide["filename"]}')
out_zip.writestr('ppt/_rels/presentation.xml.rels',
    etree.tostring(rels_tree, xml_declaration=True, encoding='UTF-8', standalone=True))

# 3. Update ppt/presentation.xml — insert sldId entries at correct positions
#    PROCESS IN REVERSE ORDER so earlier insertions don't shift later positions
for new_slide in sorted(new_slides, key=lambda x: x['insert_after_pos'], reverse=True):
    new_el = etree.Element(f'{{{NS_P}}}sldId')
    new_el.set('id', str(new_slide['sldId']))
    new_el.set(f'{{{NS_R}}}id', new_slide['rId'])
    entries = list(sldIdLst)
    ref_entry = entries[new_slide['insert_after_pos'] - 1]  # 1-indexed
    ref_idx = list(sldIdLst).index(ref_entry)
    sldIdLst.insert(ref_idx + 1, new_el)
out_zip.writestr('ppt/presentation.xml',
    etree.tostring(pres_tree, xml_declaration=True, encoding='UTF-8', standalone=True))

# 4. Write new slide files
for new_slide in new_slides:
    out_zip.writestr(f'ppt/slides/{new_slide["filename"]}', new_slide['xml'])
    out_zip.writestr(f'ppt/slides/_rels/{new_slide["filename"]}.rels', slide_rels_xml)

out_zip.close()
with open(OUTPUT, 'wb') as f:
    f.write(out_buf.getvalue())
```

#### Insert Position: Reverse-Order Processing

When inserting multiple slides, always process insert positions in **reverse order** (highest
position first). This prevents earlier insertions from shifting the indices of later ones.
The `insert_after_pos` values refer to positions in the ORIGINAL deck's sldIdLst, before any
insertions.

### Phase 4: Update the Script

The script MUST be updated to match the new slide structure, AND the ordering must match the
deck's presentation order exactly.

1. **Detect the existing delimiter format** from the script (e.g., `## SLIDE N: TITLE`)

2. **Split the original section**: Take the script text that was under the original slide and
   redistribute it across the original + new slide sections. Each section should contain only
   the script text the speaker delivers while that specific slide is showing.

3. **Insert new SLIDE delimiters** for each added slide. Use letter suffixes to avoid
   renumbering the entire script (e.g., if you split slide 14, they become SLIDE 14 and
   SLIDE 14b). This preserves the original numbering so the speaker's existing mental map
   isn't disrupted.

4. **Critical: Match the deck's insertion order.** If a new slide is inserted AFTER the
   original it expands from in the deck, the new script section must appear AFTER the
   original's section in the script. If the new slide appears BEFORE another original slide
   in the deck, the script section must appear in the same relative position. Always verify
   the final script section order against the deck's sldIdLst order.

5. **Preserve all stage directions** — `[PAUSE]`, `[GESTURE]`, audience polls, etc. Place
   them in whichever section they naturally belong to.

6. **Preserve timing checkpoints** — update them to note the expanded deck has more slides
   but the same timing targets.

### Phase 5: Verify (CRITICAL — do not skip)

Script-to-deck alignment is the single most important quality gate. A mismatch means the
speaker loses their place during delivery.

1. **Count deck slides** using the output file (not python-pptx, which may miscount):
   ```python
   import zipfile
   from lxml import etree
   with zipfile.ZipFile('output.pptx') as z:
       pres = etree.fromstring(z.read('ppt/presentation.xml'))
       NS_P = 'http://schemas.openxmlformats.org/presentationml/2006/main'
       sldIds = pres.findall(f'.//{{{NS_P}}}sldIdLst/{{{NS_P}}}sldId')
       print(f'Deck slides: {len(sldIds)}')
   ```

2. **Count script SLIDE delimiters**:
   ```bash
   grep -c '^## SLIDE' output_script.md
   ```
   (Adjust the pattern to match the actual delimiter format.)

3. **Counts MUST be equal.** If not, diagnose and fix before proceeding.

4. **Verify ordering** — walk through both lists side by side and confirm each deck slide's
   title matches the corresponding script section's title. This catches insertion-order
   mismatches where counts are equal but positions are swapped.

5. **Verify content** — spot-check that new slides contain the intended content and that no
   placeholder text remains. Read the output .pptx with python-pptx (read-only) to extract
   text from the new slides and compare against the script.

6. **Zip integrity check**:
   ```python
   import zipfile
   with zipfile.ZipFile('output.pptx') as z:
       bad = z.testzip()
       assert bad is None, f"Corrupt zip entry: {bad}"
   ```

### Phase 6: Produce the Change Report

Write a markdown report (`expansion-report.md`) containing:

1. **Summary**: Original slide count, new slide count, number of slides added
2. **Expansion Details**: For each expanded slide:
   - Original slide number and title
   - New slide label and title
   - What the new slide displays
   - Why this section was expanded (the memorization challenge it addresses)
3. **Script Changes**: Summary of how the script was restructured
4. **Delivery Tips**: Advice on using the expanded deck — e.g., "Slides 14 and 14b now walk
   through the PR summary example. Slide 14 shows the code issue; 14b shows the process
   failure. You can reference what's on screen rather than recalling the details."

## Output Files

All outputs go in the same directory as the inputs, with suffixes:
- `{basename}_expanded.pptx` — the expanded deck
- `{basename}_expanded.md` — the updated script
- `{basename}_expansion-report.md` — the change report

## Important Reminders

- **Never overwrite originals** — always create new files with `_expanded` suffix
- **Never use python-pptx to save** — it silently corrupts .pptx files by rewriting internal
  XML. Use it only for read-only analysis. All deck modification must use direct ZIP/XML
  manipulation as described in Phase 3.
- **New slides must look intentional** — they should enhance the audience experience, not
  look like speaker notes projected on screen
- **The script must stay in sync** — every slide in the deck needs a matching SLIDE section
  in the script, and vice versa. Verify both count AND ordering.
- **Letter suffixes for numbering** — use 14, 14b, 14c rather than renumbering everything,
  so the speaker's existing familiarity with slide numbers is preserved
- **Process insertions in reverse order** — when inserting multiple slides, work from the
  highest position to the lowest to prevent index shifting
- **Respect the talk's timing** — adding slides doesn't add time; the same content is just
  spread across more visual anchors
