---
name: training-deck-reviewer
description: >
  Comprehensive 9-phase QA reviewer for AI/ML technical training decks (.pptx). Performs deck
  inventory, content accuracy checks (with web research), visual/diagram verification, animation
  testing via Chrome, technology gap analysis, content flow review, report generation, AND applies
  fixes directly to the deck. Produces three deliverables: qa-report.md, a _reviewed.pptx (typos
  fixed, years updated, gap-fill slides added, change summary slide, speaker notes, backup slides),
  and anticipated-qa.md with likely attendee Q&A. Trigger on: "review this deck", "QA the slides",
  "check the training materials", "audit the presentation", "verify the deck is up to date", or any
  request to review, QA, audit, or check a training deck, presentation, or course slides for
  accuracy, completeness, or quality. Also triggers on "review" or "check" in context of a training
  deck. Do NOT use for creating slides from scratch or general slide editing — use pptx or
  training-course-builder instead.
---

# Training Deck Reviewer

You are performing a comprehensive QA review of an AI/ML technical training deck. This is a
systematic 9-phase process that catches content errors, visual issues, missing topics, and
flow problems — then produces BOTH a structured report AND a modified copy of the deck with
fixes applied.

**CRITICAL — This review produces THREE deliverables:**

1. `qa-report.md` — the written QA report (Phase 7)
2. `<deck>_reviewed.pptx` — the modified deck with all changes applied (Phase 8)
3. `anticipated-qa.md` — likely audience questions and suggested answers (Phase 9)

**You are NOT done until Phase 9 is complete and all three files have been saved.**
The report alone is not sufficient. The whole point of this skill is that the deck gets
updated — not just analyzed.

## Before You Start

1. **Read the pptx skill** at the sibling `pptx/SKILL.md` path (same parent directory as this skill).
   You'll need its tools for reading slides, extracting text, converting to images, unpacking XML,
   and editing content.

2. **Get the deck.** The user will provide a .pptx file. Copy it to your working directory so you
   have a local copy to work with.

3. **Ask about time allocation.** Before starting the review, ask the user:
   - "How much total time is allocated for this presentation (including labs)?"
   - If the user mentions labs or the deck contains lab placeholder slides, confirm: "Does that
     include lab time, or is lab time separate?"
   Record the answer. This feeds into the Timing Estimate in Phase 6. If the user doesn't know
   or says "no constraint," skip timing analysis but still produce the estimate as informational.

4. **Pre-flight inspection:**
   - Get total slide count
   - Note the deck version/date if present on the title slide
   - Identify the deck's visual theme — fonts, colors, layout patterns, decorative elements
   - Extract all text with `python -m markitdown <deck>.pptx` for a content overview
   - Generate thumbnails with `python scripts/thumbnail.py <deck>.pptx` for a visual overview
   - **Identify the "end of active deck" boundary** — find the slide with "That's All",
     "Thanks", "Thank You", "Questions?", or similar closing content. Record its slide number.

5. **Scope boundary — post-closing slides:**

   Any slides that appear AFTER the closing/thanks slide are **out of scope for review**. These
   are typically extras, reserves, or leftovers from previous reviews. During Phase 1 inventory,
   note them briefly (slide number, title) but tag them as `post-closing` and **skip them in all
   subsequent phases** — no accuracy checks, no visual review, no animation analysis, no gap
   placement, no flow analysis. They should not appear in issue tables, gap recommendations, or
   action items. If a post-closing slide is relevant to a gap identified in Phase 5, mention it
   as "an existing slide after the closing slide may partially address this" but do not evaluate
   it further.

6. **Proceed through all 9 phases in order.** Each phase builds on the previous ones.
   Phases 1-6 are analysis. Phase 7 writes the report. **Phase 8 applies changes to the deck.**
   **Phase 9 generates the anticipated Q&A document.**
   You are not finished until Phase 9 is complete and all three deliverable files have been saved.

---

## Phase 1: Deck Inventory

Catalog every slide so you have a complete map of the deck before diving into details. Process
in batches of 10-15 slides.

For each slide, record:

- **Slide number**
- **Title text** (from the title placeholder)
- **Content summary** (1-2 sentence description of what's on the slide)
- **Shape types present** (text boxes, images, charts, tables, grouped shapes, etc.)
- **Category**: one of `content` (bullets/text), `diagram` (visual/flowchart/infographic),
  `lab` (hands-on exercise), `section` (section divider/header), `title` (title slide),
  `code` (code samples), `table` (tabular data)

Additionally, during inventory, **scan for year references** across the deck:

- **Title slide**: Look for a date, version date, or copyright year
- **Footers**: Check for copyright text (e.g., "© 2025 Brent C. Laster &")
- **Slide content**: Note any year references in body text that may need updating
- Record every year reference found: slide number, location (title, footer, body), the year found

This year scan feeds directly into the Year/Date Update step (see below).

Group slides into logical sections based on section dividers and topic flow. The output of this
phase is a complete inventory table and a section map showing how the deck is organized.

**Post-closing slides:** When you reach the closing/thanks slide identified in pre-flight, mark
all subsequent slides as `post-closing` in the inventory. List them with slide number and title
but do NOT analyze them further in any subsequent phase. Add a note in the inventory:
"Slides [N+1]-[end] are post-closing and excluded from review scope."

This inventory becomes your reference for every subsequent phase — you'll use it to know which
slides need visual inspection, which have animations, where the lab slides are, etc.

---

## Phase 2: Content Accuracy Audit

Extract every verifiable technical claim from the deck and research it. This is where you catch
outdated version numbers, renamed products, deprecated APIs, and factual errors.

### What to extract

- Library/framework versions (e.g., "LangChain 0.1.x", "Python 3.11")
- Model names and parameter counts (e.g., "GPT-4 has 1.7T parameters")
- Benchmark numbers and statistics
- Dates and timelines
- URLs and links
- Tool/product descriptions
- Attributions and quotes
- Code syntax and API signatures

### How to verify

Use web search against current documentation and authoritative sources. For each claim, search
for the latest information and compare. Don't assume something is correct just because it sounds
reasonable — actually check it.

### Issue categories

Flag each problem using one of these categories:

- **OUTDATED**: Was correct but is no longer current (old version numbers, superseded benchmarks)
- **INACCURATE**: Factually wrong regardless of when it was written
- **DEPRECATED**: Tools, APIs, or methods that have been officially deprecated
- **RENAMED**: Products, organizations, or tools that changed names (e.g., "Facebook" to "Meta",
  "Hugging Face Datasets" to "datasets")
- **MISLEADING**: Technically true but presented in a way that could confuse learners
- **TYPO**: Spelling or typographical errors in technical terms, names, or code

Record the slide number, the specific claim, what's wrong, and what the correct information is.

---

## Phase 3: Visual & Diagram Review

This phase checks that every visual element accurately represents its accompanying content and
looks good. There are two sub-workflows depending on whether slides have animations.

### Static slides (no animations)

1. Identify all diagram/visual slides from your Phase 1 inventory
2. Convert those slides to images using the pptx skill's image conversion approach:
   ```bash
   python scripts/office/soffice.py --headless --convert-to pdf <deck>.pptx
   pdftoppm -jpeg -r 150 <deck>.pdf slide
   ```
3. Inspect each diagram slide image. For each one, cross-reference the visual with its
   accompanying text content. Ask yourself: does this diagram actually show what the text
   describes? Are all components labeled? Are relationships correct?
4. Rate each visual:
   - **Accurate**: Visual correctly represents the described concept
   - **Partial**: Mostly correct but missing elements or has minor inaccuracies
   - **Inaccurate**: Visual contradicts or misrepresents the text content
   - **Unclear**: Visual is ambiguous or difficult to interpret
5. Check for consistent visual style across the deck (fonts, colors, shape styles)
6. Flag broken images, low-resolution graphics, or illegible labels

### Animated slides (click-through verification via Chrome)

For slides with animations, static images only show the initial state. To properly verify these,
you need to click through the animation sequence in a live presentation.

**Workflow:**

1. The deck should be accessible via OneDrive (or the user will tell you where it is)
2. Using Claude in Chrome browser tools:
   - Navigate to the deck on OneDrive
   - Open it in PowerPoint Online's presentation/slideshow mode
   - Navigate to the animated slide
   - Click through each animation step, taking a screenshot after each click
   - Verify that each animation reveal makes sense in context — does the content appear in
     a logical order? Does the build-up tell a coherent story?
   - Note the total number of click steps per slide
3. Record observations for each animated slide: what the animation sequence shows, whether
   the reveal order is logical, and any issues spotted

**Known PowerPoint Online limitations:**

- Slides with very high animation counts (100+ animation sequences) may fail to play animations
  entirely in PowerPoint Online's slideshow mode. Clicks and spacebar presses will have no effect,
  and animated elements will remain hidden. This was observed on slides with 130-185 animation
  sequences.
- Slides with moderate animation counts (under ~50 sequences) work reliably — bullets, diagram
  elements, labels, and arrows all animate correctly on click.
- When a slide's animations fail to play in PowerPoint Online, flag it in the report with the
  note: "Animations too complex for PowerPoint Online — requires desktop PowerPoint click-through."
  Fall back to XML-based analysis (Phase 4) for structural checks, and recommend manual testing
  with the desktop PowerPoint app.

**Prioritization for click-through testing:**

- Focus click-through testing on diagram/visual slides where animation order matters for
  understanding (e.g., neural network build-ups, process flows, architecture diagrams).
- For text-only slides with simple bullet animations, XML analysis (Phase 4) is usually sufficient.
- Start with the Slide Show > From Current Slide option to test specific slides rather than
  clicking through from the beginning.

If Chrome/OneDrive access isn't available, fall back to the XML-based animation analysis in
Phase 4 and flag these slides for manual click-through testing.

---

## Phase 4: Animation Structure Check

This phase examines the raw XML structure of animations for technical issues that visual
inspection might miss.

### What to scan

Unpack the deck and look for animation elements in each slide's XML:

```bash
python scripts/office/unpack.py <deck>.pptx unpacked/
```

Search for `<p:timing>`, `<p:anim>`, `<p:seq>` elements in each `slide*.xml` file.

### For each animated slide, extract:

- **Animation types**: Appear, Wipe, Fade, etc. (from `presetID` / `presetClass` attributes)
- **Trigger types**: `clickEffect`, `afterEffect`, `withEffect`
- **Target shape IDs** and the **number of click-through steps**

### Checks to perform

- **Broken references**: Verify every animation target (`<p:spTgt spid="X"/>`) references a
  shape that actually exists on that slide. Orphaned targets pointing to deleted shapes are
  a common problem when slides get edited — they cause silent failures during presentation.
- **Logical ordering**: Check that animation sequences follow a reasonable content-reveal
  pattern (title first, then points in order, not random jumps around the slide)
- **Section consistency**: Flag sections where some slides have animations and others don't.
  Inconsistent animation use creates awkward pacing for the instructor.
- **Excessive complexity**: Flag slides with more than 20 click effects. These are hard to
  deliver smoothly and should be manually rehearsed.

### Limitations

Animation XML analysis tells you *what* animations exist and *whether they're structurally sound*,
but it can't tell you how they *feel* during delivery. Always recommend manual click-through for
animated slides, even if the XML looks clean.

---

## Phase 5: Technology Gap Analysis

Compare what the deck covers against the current AI/ML landscape. This is where you identify
topics the deck is missing or where coverage is too thin.

### Process

1. **Catalog covered topics**: From your Phase 1 inventory, list every AI/ML topic, tool,
   technique, and concept the deck covers
2. **Research the current landscape**: Use web search to understand what's current in the
   specific areas the deck teaches. What tools have emerged? What techniques are now standard?
   What has the community moved to?
3. **Compare and identify gaps**

### Gap priority levels

- **Critical**: Core concepts expected in any training on this topic that are completely missing.
  Examples: LoRA/QLoRA missing from a fine-tuning section, RAG missing from an LLM applications
  section, prompt engineering techniques missing from a prompt engineering deck.
- **Important**: Significant topics that would meaningfully strengthen the course. The deck
  isn't broken without them, but learners would benefit.
- **Nice-to-have**: Emerging or specialized topics that add value but aren't essential for
  the target audience.

For each gap, suggest a specific placement — which section it belongs in, roughly which existing
slides it should follow. Also note any covered topics that have become obsolete or significantly
less relevant since the deck was last updated.

---

## Phase 6: Content Flow Analysis

Evaluate how well the deck works as a learning experience, independent of whether individual
facts are correct.

### Dimensions to evaluate

- **Learning progression**: Does content move from simple to complex within each section and
  across the entire deck? Or does it jump around in difficulty?
- **Prerequisite ordering**: Are concepts introduced before they're used or referenced?
  A slide about "fine-tuning with LoRA" shouldn't appear before the slide explaining what
  fine-tuning is.
- **Transition quality**: How smoothly does the deck move between sections? Are there logical
  bridges, or does it jump abruptly from one topic to another?
- **Pacing**: Flag sections that are too dense (10+ bullet points per slide, walls of text)
  or too sparse (slides with almost no content that could be merged).
- **Lab placement**: Verify that hands-on exercises appear *after* the concepts they practice.
  A lab on RAG shouldn't come before the slides explaining retrieval and vector databases.
- **Redundancy**: Identify duplicate or near-duplicate content across slides. Some repetition
  is fine for reinforcement, but slides that say the same thing without adding value should
  be flagged.

For each issue, suggest a specific fix — move slide X after slide Y, add a bridge slide
between sections A and B, split this dense slide into two, etc.

### Slide Reduction Candidates

As a separate sub-analysis, identify slides that could be removed or consolidated if the deck
needs to be shortened (e.g., for a tighter time slot or to make room for new content). For each
candidate, explain WHY it's a removal candidate and what, if anything, would be lost.

**What to look for:**

- Slides that repeat information already well-covered on an earlier slide
- Slides with very thin content that could be merged into an adjacent slide
- Slides covering tangential topics that don't directly support the deck's core learning objectives
- Slides where the content is outdated enough that removal is better than rewriting
- Slides that provide background/context most attendees likely already know

**What to IGNORE (never flag as reduction candidates):**

- **Lab intro slides** — slides that introduce a hands-on exercise (typically with a different
  background theme, limited text, and a lab title). These are structural anchors for the course.
- **Section header/divider slides** — slides that mark the beginning of a new topic section.
  These have distinct backgrounds (not the standard white curved-line theme) and serve as
  navigation landmarks during delivery.
- **Title and closing slides** — the deck's opening and "That's All"/"Thanks" slides

**How to identify lab intros and section headers:** These slides typically have visually distinct
backgrounds (colored/gradient fills, images, or patterns) rather than the standard content slide
background (white with subtle decorative curved lines). They usually contain minimal text — just
a title or section name. When in doubt, compare the slide's background to the majority content
slides; if it's visually different, skip it.

For each candidate, provide:
- Slide number and title
- Why it's a candidate (redundant, thin, tangential, etc.)
- Impact if removed (low/medium/high) — what content would be lost
- Suggested alternative if applicable (e.g., "merge key points into slide N")

### Timing Estimate

Using the Phase 1 inventory and the time allocation provided by the user, estimate how long
the deck will take to deliver and whether it fits within the allotted time.

**How to estimate:**

1. **Count content slides** (in-scope only — exclude post-closing slides, section headers,
   and lab intro slides from the per-slide time calculation).
2. **Estimate per-slide time** based on slide density:
   - **Light slides** (title only, single image, 1-3 bullets): ~1 minute
   - **Standard slides** (4-8 bullets, a diagram with explanation): ~2 minutes
   - **Dense slides** (10+ bullets, complex diagrams, code walkthroughs): ~3 minutes
   - **Section header/divider slides**: ~0.5 minutes (brief transition)
3. **Count lab placeholder slides** — each lab intro slide represents a hands-on exercise.
   Assume **11 minutes per lab** unless the user specifies otherwise.
4. **Add buffer time**: title/intro (~3 minutes), closing/Q&A (~5 minutes).
5. **Sum it up**:
   ```
   Total = slide presentation time + (number of labs × 11 min) + buffer
   ```

**Produce a breakdown by section:**

For each major section of the deck, estimate its time contribution:
- Section name
- Number of content slides
- Number of labs in this section
- Estimated time for this section (slides + labs)

**Compare to allocated time:**

- If the estimate is **within 10%** of the allocation → "Fits comfortably"
- If the estimate **exceeds the allocation by 10-25%** → "Tight — may need minor trimming"
- If the estimate **exceeds the allocation by >25%** → "Over time — trimming required"

**If over time**, suggest specific ways to trim. Reference the Slide Reduction Candidates
analysis above, and additionally suggest:
- Sections that could be condensed or delivered at a higher level
- Labs that could be made optional or assigned as take-home exercises
- Content that could be moved to an appendix/extras section after the closing slide

**IMPORTANT:** Do NOT make any changes to the deck based on timing analysis. This is
advisory only — present the estimate and trimming suggestions in the report and let the
author decide what to cut.

---

## Phase 7: Report Generation

Compile everything from Phases 1-6 into a structured `qa-report.md` file.

### Report structure

```markdown
# QA Report: [Deck Title]
**Reviewed**: [date]
**Deck version**: [version/date from title slide if present]
**Total slides**: [count] ([in-scope count] in scope, [post-closing count] post-closing)
**Allocated time**: [time from user, or "Not specified"]
**Overall quality score**: [X/10]

## Executive Summary
[2-3 paragraph high-level assessment. What's strong, what needs work, overall readiness
for delivery.]

## Issues by Category and Priority

### Critical Issues
[Table: Slide # | Phase | Category | Description | Recommended Fix]

### Important Issues
[Table: same format]

### Minor Issues
[Table: same format]

### Informational Notes
[Table: same format]

## Visual Accuracy Ratings
[Table: Slide # | Title | Rating | Notes]

## Animation Inventory
[Table: Slide # | Title | Animation Types | Click Steps | Broken Refs? | Notes]
[List of slides recommended for manual click-through testing]

## Technology Gap Analysis
[Table: Gap | Priority | Suggested Placement | Rationale]
[List of obsolete/declining topics still covered]

## Content Flow Issues
[Table: Issue | Location | Suggested Fix]

## Slide Reduction Candidates
[If the deck needs to be shortened, these slides are candidates for removal or consolidation.
Lab intro slides and section headers are excluded from this analysis.]
[Table: Slide # | Title | Reason | Impact if Removed | Alternative]

## Timing Estimate
**Allocated time**: [time provided by user, or "No constraint specified"]
**Estimated delivery time**: [total estimate]
**Verdict**: [Fits comfortably / Tight / Over time]

### Breakdown by Section
[Table: Section | Content Slides | Labs | Est. Time]

### Trimming Suggestions (if over time)
[Specific suggestions referencing slide reduction candidates, optional labs, etc.
Note: These are recommendations only — no changes are made to the deck based on timing.]

## Year/Date Updates Applied
[Table: Location | Old Value | New Value | Update Method (master/slide/python-pptx)]

## Changes Applied (Phase 8)
[Comprehensive list of ALL modifications made to the deck during Phase 8. This section must be
added after Phase 8 is complete and should include:]

### New Slides Created
[Table: Slide # | Title | Positioned After | Gap Addressed | Visual Elements Used]

### Year/Date Updates
[Table: Slide # | Location | Old Value | New Value]

### Backup Slides
[Table: Backup Slide # | Original Slide # | Original Title]

### Speaker Notes Added
[Table: Slide # | Note Summary]

### Other Modifications
[Any typo fixes, formatting corrections, etc.]

## Slide-by-Slide Quick Reference
[Condensed per-slide summary from Phase 1 with any issues flagged inline]

## Prioritized Action Items

### Immediate (fix before next delivery)
- [Critical errors, broken content, misleading information]

### Short-term (next revision)
- [Important improvements, missing content, inconsistencies]

### Long-term (future updates)
- [Nice-to-haves, emerging topics, structural improvements]
```

Use these severity indicators in the tables:
- Red circle: **Critical** — factual errors, broken content, misleading information
- Yellow circle: **Important** — missing content, inconsistencies, outdated references
- Green circle: **Minor** — typos, formatting nits, style inconsistencies
- Info: **Informational** — observations, recommendations, nice-to-haves

Save the report to `qa-report.md` in the working directory, then copy it to the outputs folder
so the user can access it.

---

## Phase 8: Apply Changes to Deck

**This phase is MANDATORY.** After generating the report, you must now apply all identified
changes to a copy of the actual .pptx file. The report tells the author what was found; this
phase actually fixes the deck.

### Step-by-step workflow

1. **Make a working copy** of the original deck:
   ```bash
   cp <deck>.pptx <deck>_reviewed.pptx
   ```
   All modifications go into the `_reviewed` copy. Never overwrite the original.

2. **Apply auto-fixes** to the deck (see Auto-Fix Policy below for the full list):
   - Fix typos and spelling errors found in Phase 2
   - Fix formatting inconsistencies (double spaces, punctuation)
   - Update stale copyright/version years (see Year/Date Update section below)
   - For EACH fix: add a speaker note documenting the change (see Change Tracking below)
   - For EACH modified slide: duplicate the original version and append it at the end of
     the deck as a backup (see Backup Slides below)

3. **Create new slides** for technology gaps identified in Phase 5:
   - Follow the New Slide Guidelines section below for theme matching and positioning
   - Add speaker notes documenting each new slide
   - New slides do NOT need backup copies (they're new, there's no original to back up)

4. **Update year/date references** across the deck:
   - Update slide master footers (preferred — one change fixes all slides)
   - Update title slide version date and bump minor version number
   - Update any stale presentation-year references in body text
   - Do NOT change historical/factual year references
   - See the Year/Date Update section below for detailed technical approaches

5. **Create a Change Summary slide** and insert it as **slide 2** (immediately after the title
   slide) so the reviewer/instructor sees it first:
   - Title: "QA Review — Changes Applied" (or similar)
   - Date of review
   - Bulleted list of all changes: year/date updates, new slides added (with titles and positions),
     backup slides appended, any typo/formatting fixes
   - Use the same visual theme as the rest of the deck (match fonts, colors, background)
   - Create using the `add_slide.py` duplicate-and-edit workflow (see New Slide Guidelines)
   - Add a speaker note: "This slide summarizes all changes made during the QA review. Remove
     before delivery if desired."

6. **Verify the modified deck**:
   - Re-extract text from the modified deck and confirm changes were applied
   - Check slide count matches expected (original + summary slide + new gap slides + backup slides)
   - Open a few modified slides to verify formatting wasn't broken

7. **Save the final deck** to the outputs folder:
   ```bash
   cp <deck>_reviewed.pptx /path/to/outputs/<deck>_reviewed.pptx
   ```

### What gets modified vs. what gets flagged

| Action | Phase 8 does it? | Notes |
|--------|-------------------|-------|
| Fix typos/spelling | ✅ Yes | Auto-fix with backup + speaker note |
| Fix formatting | ✅ Yes | Auto-fix with backup + speaker note |
| Update stale years | ✅ Yes | Auto-fix via slide master or direct edit |
| Create gap-fill slides | ✅ Yes | New slides with speaker notes |
| Create change summary slide | ✅ Yes | Inserted as slide 2, lists all changes |
| Technical content rewrites | ❌ No | Flagged in report for author |
| Reorder/remove slides | ❌ No | Flagged in report for author |
| Diagram changes | ❌ No | Flagged in report for author |
| Content restructuring | ❌ No | Flagged in report for author |

### Required output

At the end of Phase 8, you MUST have saved all three:
- `qa-report.md` — from Phase 7
- `<deck>_reviewed.pptx` — the modified deck with all changes applied
- `anticipated-qa.md` — from the Anticipated Q&A step below

**If you have not saved a modified .pptx file, the review is incomplete. Go back and do it.**

---

## Phase 9: Anticipated Q&A

**This phase is MANDATORY.** After completing the deck modifications, generate a document of
likely audience questions and suggested answers. This serves as an instructor preparation aid —
attendees will inevitably ask questions that go beyond what's on the slides, and having prepared
answers helps the instructor deliver confidently.

### How to generate the Q&A

1. **Review the full deck content** — use the text extraction from Phase 1 and your content
   knowledge from Phases 2 and 5.
2. **For each major section of the deck**, identify 3-5 questions a learner might ask. Think about:
   - **Clarification questions**: "What's the difference between X and Y?" where X and Y are
     related concepts mentioned on nearby slides
   - **Depth questions**: "How does this work under the hood?" for concepts presented at a
     high level
   - **Practical questions**: "When would I use X vs Y in production?" or "What are the trade-offs?"
   - **Troubleshooting questions**: "What if I get error X when running the lab?" or "Why isn't
     my model converging?"
   - **Current landscape questions**: "What's the latest on X?" for fast-moving topics like
     model releases, framework updates, or best practices
   - **Scope/boundary questions**: "Is X covered in Day 2?" or "How does this relate to Y
     which we haven't discussed yet?"
3. **Write clear, concise answers** for each question. Answers should be:
   - 2-5 sentences (enough to be helpful, not so long they become a lecture)
   - Technically accurate (use your Phase 2 research)
   - Tied back to the deck content where possible ("As we'll see on slide N...")
   - Honest about limitations ("This is beyond our scope today, but...")
4. **Include questions about the new slides** you added in Phase 8, since those cover emerging
   topics that attendees are especially likely to ask about.

### Output format

Save as `anticipated-qa.md` in the outputs folder:

```markdown
# Anticipated Q&A: [Deck Title]
**Generated**: [date]
**Deck version**: [version]

## Section: [Section Name] (Slides X-Y)

**Q: [Question]?**
A: [Answer]

**Q: [Question]?**
A: [Answer]

## Section: [Next Section] (Slides X-Y)
...

## General / Cross-Cutting Questions

**Q: [Question]?**
A: [Answer]
```

### Guidelines

- Aim for **25-40 questions total** across the entire deck
- Weight questions toward sections that are conceptually dense or cover newer/evolving topics
- Include at least 2-3 questions per new slide added in Phase 8
- Include a "General / Cross-Cutting" section for questions that span multiple topics
- Don't include questions whose answers are directly and obviously stated on a slide — focus
  on questions that go *beyond* the slide content
- If a question's best answer is "we'll cover that in Day 2/3", say so explicitly

---

## Year/Date Update

If any year references in the deck are not the current year, update them automatically. This is
a common issue when decks are reused across semesters or years.

### Where to look

1. **Title slide** — version date, copyright year, presentation date
2. **Footer text on every slide** — typically contains "© [YEAR] [Author Name]"
3. **Slide master / layout footers** — the footer may be defined in the slide master rather than
   on individual slides, so changing it there propagates to all slides at once
4. **Body text** — any year references in slide content (e.g., "As of 2025, ...")

### How to update

**Approach 1: Slide master footer (preferred for footers)**

The footer copyright text is typically inherited from the slide master or slide layout. Updating
it there fixes every slide at once.

```bash
# Unpack the deck to access XML
python scripts/office/unpack.py <deck>.pptx

# Look for footer text in slide masters
grep -r "© " <deck>_unpacked/ppt/slideMasters/
grep -r "© " <deck>_unpacked/ppt/slideLayouts/

# Also check slide-level overrides
grep -r "© " <deck>_unpacked/ppt/slides/
```

In the XML files, footer text lives inside `<a:t>` elements within `<p:sp>` shapes that have
placeholder type `ftr` (footer) or `dt` (date). Find the `<a:t>` element containing the old
year and replace it with the current year.

After editing the XML:

```bash
# Repack the deck
python scripts/office/pack.py <deck>_unpacked <deck>_updated.pptx
```

**Approach 2: Direct slide-level text replacement**

If the footer/year is embedded in regular text boxes rather than master placeholders, you'll need
to update the individual slide XML files directly. Use the same unpack/grep/edit/pack workflow,
but target the specific `<ppt/slides/slideN.xml>` files.

**Approach 3: python-pptx library**

For programmatic updates across many slides:

```python
from pptx import Presentation
from pptx.util import Inches, Pt
import datetime

prs = Presentation('<deck>.pptx')
current_year = str(datetime.datetime.now().year)
old_year = '2025'  # detected from Phase 1 scan

for slide in prs.slides:
    for shape in slide.shapes:
        if shape.has_text_frame:
            for paragraph in shape.text_frame.paragraphs:
                for run in paragraph.runs:
                    if old_year in run.text:
                        run.text = run.text.replace(old_year, current_year)

# Also check slide masters
for master in prs.slide_masters:
    for shape in master.shapes:
        if shape.has_text_frame:
            for paragraph in shape.text_frame.paragraphs:
                for run in paragraph.runs:
                    if old_year in run.text:
                        run.text = run.text.replace(old_year, current_year)

# And slide layouts
    for layout in master.slide_layouts:
        for shape in layout.shapes:
            if shape.has_text_frame:
                for paragraph in shape.text_frame.paragraphs:
                    for run in paragraph.runs:
                        if old_year in run.text:
                            run.text = run.text.replace(old_year, current_year)

prs.save('<deck>_updated.pptx')
```

### Important notes

- **Only update copyright/version years**, not years that are part of historical content (e.g.,
  "Transformers were introduced in 2017" should NOT be changed).
- Distinguish between: **presentation year** (should be current) vs. **factual year** (leave alone).
- Common patterns that SHOULD be updated:
  - `© 2025` → `© 2026`
  - `v2.2 11/30/25` → update version date to current
  - `Workshop - December 2025` → update to current delivery date
- Common patterns that should NOT be updated:
  - "The Transformer architecture was introduced in 2017"
  - "GPT-3 was released in June 2020"
  - "As of 2023, the MTEB benchmark shows..."
- When updating the title slide version date, bump the minor version number if one exists
  (e.g., v2.2 → v2.3) to indicate the deck has been revised.
- **Always use the change tracking and backup slide approach** (see Auto-Fix Policy below)
  for any year updates made to slide content. Slide master changes don't need backups since
  they only affect footer rendering.

---

## Auto-Fix Policy

The skill can fix certain things during the review, but leaves substantive changes to the author.

### Auto-fix (do these during the review)

- Typos and spelling errors
- Double spaces and extra whitespace
- Formatting inconsistencies (punctuation, capitalization patterns)
- Obvious punctuation errors
- **Stale copyright/version years** — update to current year (see Year/Date Update section above)

### Ask first (flag in report, don't change)

- Technical rewrites or version number updates
- Adding, removing, or reordering slides
- Diagram changes
- Content restructuring

### Change tracking

Every slide that gets modified must have a note added to its **speaker notes** section with:

```
[QA Review - YYYY-MM-DD HH:MM] Changed: <short description of what was fixed>
```

This creates an audit trail so the author knows exactly what was touched.

### Backup slides

Before modifying any slide, **duplicate the original slide and append it at the end of the deck**
as a backup. Add a speaker note to the backup slide:

```
[BACKUP - QA Review YYYY-MM-DD] Original version of slide [N]: [slide title]
```

This way the author can always compare or revert if they disagree with a fix.

For newly created slides (gap-filling), no backup is needed — just add a speaker note explaining
what was added and why.

---

## New Slide Guidelines

When creating new slides to fill content gaps identified in Phase 5:

1. **Match the existing theme.** Study 2-3 nearby slides to extract exact font sizes, font
   families, colors, and layout patterns. The new slide should look like it was always part of
   the deck.
2. **Include visual design elements** — colored rounded-rectangle cards, comparison boxes, or
   two-column layouts with callout banners. Don't create text-only slides. Use the slide deck's
   existing visual vocabulary (e.g., if the deck uses 3-column card layouts, replicate that
   pattern). Aim for full-width use of the slide area; avoid layouts where content is clustered
   on one side leaving large empty areas.
3. **Position logically.** Place the new slide where it fits in the content flow, not just
   appended at the end.
4. **Add speaker notes** documenting the addition:
   ```
   [QA Review - YYYY-MM-DD] NEW SLIDE: Added to address [gap description].
   Placed after slide [N] to maintain logical flow.
   ```

### Critical: Slide Master Matching

**DO NOT use `python-pptx` to create new slides.** The `prs.slide_layouts[N]` API often selects
the wrong slide master, causing new slides to have completely different backgrounds, colors, and
fonts from existing content slides. Decks with multiple slide masters (common when imported from
Google Slides) are particularly prone to this.

**Instead, use the `add_slide.py` duplicate-and-edit workflow:**

1. **Identify the correct template slide** — find an existing content slide that uses the layout
   and visual style you want to replicate. Render thumbnails to compare.
2. **Duplicate it** using `add_slide.py`:
   ```bash
   python scripts/add_slide.py <deck_unpacked>/ <source_slide_number> <new_slide_number>
   ```
   This copies the slide XML, all relationships (including background image refs), and ensures
   the new slide uses the same slide layout and slide master as the source.
3. **Edit the duplicated XML directly** — replace titles, body text, and shapes in the new
   `slideN.xml` file while preserving the background image reference (typically `rId3` for the
   full-slide background PNG) and the relationship file structure.
4. **Insert into presentation.xml** — add a `<p:sldId>` entry at the correct position in the
   `<p:sldIdLst>` to control where the slide appears in the deck order.

**Why this matters:** Many training decks (especially those converted from Google Slides) have
multiple slide masters. Master 0 might be a generic white theme while Master 1 has the branded
background, colors, and footer elements. Using `python-pptx` defaults to Master 0, producing
slides that look completely out of place. The duplicate-and-edit approach guarantees visual
consistency because the new slide inherits everything from its source.

### Background Image Awareness

Many decks use full-slide background PNGs (covering the entire 12192000 x 6858000 EMU slide area)
referenced as `rId3` in the slide XML. These background images contain the branded header, footer,
decorative curves/lines, and color scheme. When editing slide XML:

- **Always preserve the background `<p:pic>` element** that references the background image
- Place your content shapes ON TOP of this background
- Check the slide's `.rels` file to confirm which `rId` maps to the background image

### python-pptx Is Safe For

- **Adding speaker notes** (does not affect slide master assignment)
- **Updating text in existing shapes** (text replacement within existing runs)
- **Year/date replacements** across slide masters, layouts, and slides

But **NOT for creating new slides or duplicating slides** — always use `add_slide.py` for that.

### PowerPoint Repair Warnings

When python-pptx saves a modified .pptx file, PowerPoint may show a repair warning on open
("PowerPoint couldn't read some content"). This is common and usually does not affect content.
It happens because python-pptx may introduce minor XML schema differences (e.g., missing
`xml:space` attributes, slightly different namespace declarations). Always verify after repair
that all content survived by checking slide count and key slide content.

---

## Dependencies

This skill relies on tools from the **pptx skill** (sibling directory). Make sure these are
available:

- `python -m markitdown` — text extraction from slides
- `python scripts/thumbnail.py` — visual thumbnail grid
- `python scripts/office/unpack.py` / `pack.py` — XML access for animation analysis
- `python scripts/office/soffice.py` — PDF conversion
- `pdftoppm` — PDF to slide images
- Claude in Chrome browser tools — for OneDrive animation click-through (Phase 3)
- Web search — for content accuracy verification (Phase 2) and gap analysis (Phase 5)
- `python-pptx` — for programmatic slide modifications (Phase 8, Year/Date Update)

---

## Final Checklist — DO NOT SKIP

Before telling the user you're done, verify ALL of these are true:

- [ ] `qa-report.md` has been saved to the outputs folder
- [ ] `<deck>_reviewed.pptx` has been saved to the outputs folder (modified deck with fixes applied)
- [ ] All auto-fix changes (typos, formatting, years) have been applied to the .pptx file
- [ ] New slides have been created for each technology gap identified in Phase 5
- [ ] New slides use the CORRECT slide master (verified via duplicate-and-edit workflow)
- [ ] New slides include visual design elements (cards, boxes, diagrams), not just text
- [ ] New slides use full slide width (no large empty areas on one side)
- [ ] A Change Summary slide has been inserted as slide 2 listing all modifications
- [ ] Every modified slide has a speaker note documenting the change
- [ ] Every modified slide has a backup copy appended at the end of the deck
- [ ] Stale year references in footers/title slide have been updated to the current year
- [ ] The modified deck has been verified (text re-extracted, slide count confirmed)
- [ ] Visual QA: new slides rendered to images and inspected for correct background, layout, and readability
- [ ] The `qa-report.md` includes a "Changes Applied" section documenting all Phase 8 modifications
- [ ] `anticipated-qa.md` has been generated with 25-40 questions covering all major sections
- [ ] Q&A includes questions about new slides added in Phase 8
- [ ] Timing estimate has been calculated and included in the report
- [ ] If estimated time exceeds allocated time, trimming suggestions have been provided (no changes made)

**If any checkbox above is not checked, you are not done. Go back and complete the missing steps.**

The report without the modified deck is like a code review without the code changes — useful
analysis, but the author still has to do all the work. The whole value of this skill is that
the deck comes back ready to use.
