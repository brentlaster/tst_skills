---
name: training-deck-major-updater
description: >
  Apply major content updates to a training deck (.pptx) based on a QA report. Reads the QA report
  (qa-report.md, phase analysis files, or similar), extracts every recommended change, then walks
  the user through each change ONE AT A TIME asking whether to apply or skip it. Only applies
  user-approved changes. Use this skill whenever the user says "apply QA changes", "update deck
  from report", "make the changes from the review", "apply the recommended fixes", "implement the
  QA recommendations", or references updating a training deck based on QA findings, gap analysis,
  or review recommendations. Also trigger when the user has both a .pptx training deck and a QA
  report/gap analysis and wants changes applied selectively. Trigger on "major updater" or
  "apply changes from review" even without explicit skill name. For TechUpSkills / Brent Laster
  AI/ML training courses.
---

# Training Deck Major Updater

Apply QA-recommended changes to a training deck with **per-change user approval**. Each change
is presented individually — the user decides yes/no on every single one before anything is modified.

## Before You Start

1. **Read the PPTX skill** — ALWAYS read both files before touching any deck:
   ```
   Read /path/to/.skills/skills/pptx/SKILL.md
   Read /path/to/.skills/skills/pptx/editing.md
   ```
   These contain critical rules about slide master matching, background image awareness, and the
   unpack/edit/clean/pack workflow that this skill depends on.

2. **Read the training-course-builder skill** if available — it contains slide conventions,
   content modernization rules, and gap analysis methodology that inform how changes should be
   applied. Key sections: "Slide Creation & Updates", "Content Modernization", "Update Audit Trail".

3. **Read the training-deck-reviewer skill** if available — it defines the auto-fix policy,
   change tracking format, backup slide approach, and new slide guidelines that this skill follows.

---

## Phase 1: Discover Inputs

1. **Locate the QA report** — look for files like `qa-report.md`, `phase2_accuracy.md`,
   `phase5_gaps.md`, `phase6_flow.md`, or any markdown file containing QA findings. If the
   training-deck-reviewer was run earlier, its outputs will be in the working directory or an
   outputs folder. Ask the user if not obvious.

2. **Locate the deck** — find the `.pptx` file to update. This may be:
   - The original deck (if changes haven't been applied yet)
   - A `_reviewed.pptx` copy (if the deck-reviewer already made first-pass changes)
   - Ask the user which to use if both exist

3. **Make a working copy** — never modify the original:
   ```bash
   cp <deck>.pptx <deck>_updated.pptx
   ```

---

## Phase 2: Extract Change List

Read ALL QA report files and extract every recommended change into a structured list. Be thorough —
scan for changes across all phases:

- **Phase 2 (Accuracy)**: Inaccurate stats, deprecated APIs, outdated references, misleading claims
- **Phase 5 (Technology Gaps)**: Missing topics at Critical/Important/Nice-to-Have levels
- **Phase 6 (Content Flow)**: Prerequisite ordering issues, redundancy, pacing problems, lab placement
- **Phase 7 (Report)**: Prioritized action items, year/date updates needed
- **Phase 8 (Applied Changes)**: Changes already applied (skip these — they're done)

For each change, capture:

| Field | Description |
|-------|-------------|
| **ID** | Sequential number (C1, C2, I1, I2, N1... by priority) |
| **Priority** | `CRITICAL`, `IMPORTANT`, `NICE-TO-HAVE`, `FLOW-FIX`, `ACCURACY-FIX` |
| **Summary** | One-line description of the change |
| **Affected slides** | Which slide numbers are impacted |
| **Change type** | `text-edit` (in-place fix), `new-slide` (gap-fill), `reorder` (move slides), `remove` (delete content), `rewrite` (significant content change) |
| **Effort** | `low` (single text edit), `medium` (new slide from template), `high` (multiple new slides + content research) |
| **Detail** | Full description: what's wrong, what should change, what the new content should be |
| **Already done?** | Check if Phase 8 already applied this — if so, mark it and skip |

---

## Phase 3: Per-Change User Approval

**This is the core of the skill.** Present each change to the user ONE AT A TIME using the
AskUserQuestion tool. The user decides individually whether to apply or skip each change.

### Approval Flow

Walk through the list in priority order: CRITICAL first, then IMPORTANT, then NICE-TO-HAVE,
then FLOW-FIX, then ACCURACY-FIX.

For EACH change, present it like this using AskUserQuestion:

```
Question: "Change [ID]: [Summary] — [Priority] priority, [Effort] effort. [1-2 sentence detail]. Apply this change?"
Options:
  - "Yes, apply it" (description: what will be modified)
  - "No, skip it" (description: leave the deck as-is for this item)
```

If a change has sub-options (e.g., a gap-fill could go in two different locations), include those
as additional options.

**Important rules:**
- Present ONE change at a time — do not batch them
- Wait for the user's response before presenting the next change
- Keep a running tally of approved vs. skipped changes
- If the user says something like "apply all remaining" or "skip the rest", respect that and
  stop asking — but only if they explicitly say so
- After all changes have been presented, summarize: "You approved X changes and skipped Y.
  Ready to apply?"

### Grouping exception

If there are more than 15 changes total, you may group the NICE-TO-HAVE items together and
ask about them as a batch (with multiSelect). But CRITICAL and IMPORTANT changes are always
presented individually.

---

## Phase 4: Apply Approved Changes

Follow the PPTX editing workflow. This is where the training-deck-reviewer and training-course-builder
patterns matter most.

### 4.1 Setup

```bash
# Install dependencies
pip install "markitdown[pptx]" python-pptx Pillow --break-system-packages

# Unpack the working copy
python scripts/office/unpack.py <deck>_updated.pptx unpacked/

# Extract text for reference
python -m markitdown <deck>_updated.pptx > deck_text.md
```

### 4.2 Structural Changes First

Apply in this order to avoid ID/position conflicts:

1. **Remove slides** (if approved) — delete `<p:sldId>` entries from `presentation.xml`
2. **Add new slides** (if approved) — use `add_slide.py` (NEVER python-pptx):
   ```bash
   python scripts/add_slide.py unpacked/ <source_slide>.xml
   ```
3. **Reorder slides** (if approved) — rearrange `<p:sldId>` entries in `<p:sldIdLst>`
4. **Assign unique IDs** to new slides — check existing IDs to avoid duplicates

### 4.3 Content Edits

Use the Edit tool on individual slide XML files. For parallel editing, use subagents.

**Subagent prompt template:**
```
Edit slide content in /path/to/unpacked/ppt/slides/slideN.xml.
Use the Edit tool for all changes. Read the slide XML first.

Rules:
- Only change text inside <a:t> tags
- Preserve ALL XML structure, attributes, and <a:rPr> formatting
- Preserve background <p:pic> elements (typically rId3 for full-slide background PNGs)
- Use b="1" on <a:rPr> for bold headers
- Use XML entities for special chars: &amp; &apos; &quot; &#x201C; &#x201D;
- If slide was duplicated from a template:
  * Check for leftover images (<p:pic>) — remove if they overlap new content
  * Check ALL text shapes including second columns and footer text boxes
  * Update hyperlink URLs in the slide's .rels file
- A single visible text line may span multiple <a:r> (run) elements — update ALL of them
- When updating code examples, watch for text split across runs with different formatting

Changes to make:
[specific changes for this slide]
```

### 4.4 Critical: Slide Master Matching

**DO NOT use python-pptx to create new slides.** The `prs.slide_layouts[N]` API often selects
the wrong slide master, causing new slides to have completely different backgrounds, colors, and
fonts. This is especially common in decks with multiple slide masters (e.g., imported from
Google Slides).

**Instead, use the `add_slide.py` duplicate-and-edit workflow:**
1. Identify an existing content slide with the layout/style you want
2. Render thumbnails to verify it has the right theme
3. Duplicate with `add_slide.py`
4. Edit the duplicated XML — replace text while preserving background image refs
5. Insert into `presentation.xml` at the correct position

**python-pptx IS safe for:**
- Adding speaker notes (does not affect slide master assignment)
- Updating text in existing shapes (text replacement within existing runs)
- Year/date replacements across slide masters, layouts, and slides

### 4.5 New Slide Content Guidelines

When creating gap-fill slides:

- **Match the existing theme** — study 2-3 nearby slides for exact font sizes, families, colors
- **Include visual design elements** — colored cards, comparison boxes, two-column layouts. Don't
  create text-only slides. Use the deck's existing visual vocabulary.
- **Use full slide width** — avoid layouts where content clusters on one side with large empty areas
- **Position logically** — place where it fits in the content flow, not just appended at the end
- **Research content** — use web search to ensure new content is accurate and current

### 4.6 Year/Date Update Rules

From the training-course-builder conventions:

- **Update presentation years** — copyright, version dates, workshop dates
- **Do NOT update factual/historical years** — "Transformers introduced in 2017" stays as-is
- **Bump minor version** — e.g., v2.2 → v2.3 when updating
- Common patterns to update: `© 2025` → `© 2026`, version dates
- Common patterns to leave alone: "GPT-3 released June 2020", "As of 2023, MTEB..."

### 4.7 Change Tracking (Audit Trail)

Every modified slide gets a speaker note:
```
[Update - YYYY-MM-DD] Changed: <short description of what was fixed>
```

Every NEW slide gets a speaker note:
```
[Update - YYYY-MM-DD] NEW SLIDE: Added to address [gap description].
Placed after slide [N] to maintain logical flow.
```

### 4.8 Backup Slides

Before modifying any existing slide, duplicate the original and append it at the end of the deck:
```
[BACKUP - Update YYYY-MM-DD] Original version of slide [N]: [slide title]
```

New slides (gap-fills) do NOT need backup copies — they're new, there's no original to back up.

### 4.9 Pack and Validate

```bash
python scripts/clean.py unpacked/
python scripts/office/pack.py unpacked/ <deck>_updated.pptx --original <deck>.pptx
```

If pack reports XML validation errors, fix them before proceeding.

---

## Phase 5: Verification

### 5.1 Content QA

```bash
python -m markitdown <deck>_updated.pptx > updated_text.md
```

Grep for key terms from each approved change to verify they were applied. Check that skipped
changes were NOT accidentally applied.

### 5.2 Visual QA (MANDATORY)

Convert to images and inspect ALL modified slides:

```bash
python scripts/office/soffice.py --headless --convert-to pdf <deck>_updated.pptx
pdftoppm -jpeg -r 150 <deck>_updated.pdf slide
```

**Use a subagent with fresh eyes** for visual QA — you've been staring at the XML and will see
what you expect, not what's there. The subagent should check:

- Text properly displayed (not cut off, not overflowing)
- No leftover placeholder content from duplicated slides
- No overlapping elements (images through text, shapes stacked)
- Background images preserved (branded header/footer visible)
- Content readable with sufficient contrast
- New slides match the visual theme of surrounding slides

### 5.3 Fix-and-Verify Loop

1. Generate slides → Convert to images → Inspect
2. List issues found (if none found, look again more critically)
3. Fix issues
4. Re-verify affected slides — one fix often creates another problem
5. Repeat until a full pass reveals no new issues

**Do not declare success until you've completed at least one fix-and-verify cycle.**

---

## Phase 6: Deliver

1. **Save to workspace folder** so the user can access the file
2. **Provide a summary**:
   - Total changes approved: X
   - Total changes skipped: Y
   - Changes successfully applied: list each with slide number
   - Any issues or caveats noted during QA
   - Slide count: original → updated
   - Version: old → new
3. **Link to the file** using `computer://` link format

---

## Common Pitfalls (Lessons from Live Editing)

### Duplicated slide cleanup
- Duplicated slides inherit ALL content from the source (images, shapes, ALL text boxes)
- After editing the main content, check for leftover `<p:pic>` elements showing old diagrams
  that overlap your new text — remove them from the XML
- Check the slide's `.rels` file for old hyperlink URLs and image references — update or remove
- **Two-column layouts**: When duplicating a slide with two text columns, BOTH columns inherit
  old content. Update both — the second column is often a separate `<p:sp>` shape that's easy to miss

### Multi-run text editing
- A single visible line of text may be split across multiple `<a:r>` (run) elements with different
  formatting (bold, color, font size)
- When replacing text, you must update ALL runs that form the visible string
- Watch for leftover fragments: if old text was "initialize_agent(tools, llm, agent='zero-shot')"
  split across 3 runs, and you only update the first run to "create_react_agent(llm, tools,",
  the other runs still contain "=llm)" and "agent='zero-shot'" — producing garbled output
- **Strategy**: Read the full XML around the text, identify ALL runs contributing to the visible
  string, then update them as a coordinated set

### Slide ID uniqueness
- When adding multiple new slides via `add_slide.py`, the tool may assign the same default ID
- Always check for duplicate IDs in `<p:sldIdLst>` after adding slides
- Find the max existing ID and assign unique IDs above it

### PDF page numbering vs. slide numbering
- The PDF rendered by LibreOffice may have fewer pages than slides (some slides may not render)
- The page numbers in the PDF don't necessarily match slide file numbers (slide1.xml, slide2.xml)
  because slide ORDER is determined by `<p:sldIdLst>` in presentation.xml, not by filename
- Use `pdftotext -f N -l N` to check specific page content when hunting for a particular slide

### PowerPoint repair warnings
- When python-pptx saves a modified .pptx, PowerPoint may show "couldn't read some content"
- This is common and usually doesn't affect content — verify after repair that slide count and
  key content survived

---

## Dependencies

- `pip install "markitdown[pptx]" python-pptx Pillow --break-system-packages`
- LibreOffice (`soffice`) — for PDF conversion (auto-configured via `scripts/office/soffice.py`)
- Poppler (`pdftoppm`) — for PDF to slide images
- PPTX skill scripts: `unpack.py`, `pack.py`, `add_slide.py`, `clean.py`, `thumbnail.py`
  (located in the pptx skill's `scripts/` directory)
- Web search — for researching accurate content for gap-fill slides
