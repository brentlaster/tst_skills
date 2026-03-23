---
name: conference-talk-improver
description: >
  Improve a conference talk based on an evaluation report. Takes an abstract, speaker script, slide deck (.pptx), and eval report (markdown). Assesses each recommendation, applies agreed changes to slides and script, generates real-world code demos, and produces a change report. Trigger on: "improve my talk", "apply evaluation feedback", "revise the presentation", "fix the talk based on feedback", "incorporate review suggestions", "apply eval report", "conference talk feedback". Do NOT use for creating talks from scratch (use conference-talk-builder) or general slide editing (use pptx).
---

# Conference Talk Improver

You are improving an existing conference talk based on an evaluation report. The evaluation
report contains structured feedback — your job is to critically assess each item, apply changes
you agree with, generate real-world examples and code demos where called for, and produce a
comprehensive change report documenting everything.

## Before You Start

1. **Read the PPTX skill** — you'll need it for modifying slides:
   - Read the PPTX skill's SKILL.md (sibling path: find with `ls ../pptx/SKILL.md` or search)
   - For editing existing slides: read `editing.md`
   - For creating new slides from scratch: read `pptxgenjs.md`

2. **Read the conference-talk-builder skill's design references** — these define Brent's
   slide conventions, branding, and visual approach:
   - `references/slide-design.md` in the conference-talk-builder skill
   - `references/branding-templates.md` for standard slide templates

3. **Read this skill's reference files:**
   - `references/improvement-workflow.md` — Detailed step-by-step workflow for each phase
   - `references/example-generation.md` — How to create real-world code demos and examples

4. **Collect inputs from the user.** You need all four before proceeding:

### Required Inputs

| Input | Format | Purpose |
|-------|--------|---------|
| **Talk abstract** | Text or .md file | Context for what the talk is about |
| **Speaker script** | .md file | The current spoken narrative to revise |
| **Slide deck** | .pptx file | The current slides to modify |
| **Evaluation report** | .md file | Structured feedback with recommendations |

If any input is missing, ask the user for it before proceeding.

### Optional Context (ask if not obvious)

- **Talk duration** — the timeframe allocated for the talk (important for pacing decisions)
- **Conference name and audience** — helps calibrate depth and tone
- **Audience level** — Beginner / Intermediate / Advanced
- **Any items the user wants to prioritize or skip**

---

## Workflow Overview

The improvement process has 8 phases. Read `references/improvement-workflow.md` for detailed
instructions on each phase. Here's the high-level flow:

### Phase 1: Inventory & Context

Before touching anything, build a complete picture of the current state:

1. **Extract slide content** — use `python -m markitdown` on the .pptx to get text
2. **Read the script** — understand the narrative flow and timing
3. **Read the abstract** — understand the talk's core promise to the audience
4. **Read the evaluation report** — scan the full document to understand its structure,
   sections, and the overall tone/direction of feedback
5. **Note the talk duration** — this constrains how much can be added or changed
6. **Generate slide thumbnails** — convert to images for visual reference

Produce an internal inventory:
- Total slide count, section breakdown
- Script word count and estimated duration
- List of evaluation report sections and item count per section

### Phase 2: Evaluate Each Report Item

Go through the evaluation report section by section, item by item. For EACH recommendation:

1. **Understand the item** — what exactly is being suggested and why?
2. **Cross-reference with current materials** — find the specific slide(s) and script
   section(s) the item refers to
3. **Form an independent opinion** — do you agree this is a valid improvement? Consider:
   - Does the suggestion align with the talk's abstract and core narrative?
   - Is it factually accurate and current?
   - Does it fit within the time constraints?
   - Would it genuinely improve the audience experience?
   - Is it feasible to implement well?
4. **Classify your decision:**
   - **AGREE + APPLY** — the suggestion is valid and you'll implement it
   - **AGREE + MODIFY** — the suggestion is valid but you'll implement it differently
   - **DISAGREE + SKIP** — explain why you're not making this change
   - **DEFER** — flag for user input (e.g., subjective preference, requires speaker judgment)

Record your assessment for every single item — this feeds the final change report.

### Phase 3: Plan Changes

Before making any changes, create a structured change plan:

1. **Group related changes** — items that affect the same slides or script sections
2. **Order changes logically** — structural changes (add/remove slides) before content
   edits, content edits before visual polish
3. **Identify dependencies** — some changes may conflict or overlap
4. **Estimate impact on timing** — will additions push over the time limit? Will removals
   create gaps?
5. **Flag any examples/demos needed** — these take the most effort, plan them first

### Phase 4: Generate Examples & Demos

For any evaluation item that calls for examples, demos, code, or use cases:

1. **Research real-world use cases** — use web search to find current, relevant examples
2. **Write complete, runnable code** — not pseudocode, not fragments. Full working demos.
3. **Include setup instructions** — dependencies, environment, how to run
4. **Test the code mentally** — walk through execution step by step
5. **Create slide-ready versions** — condensed code for slides (8-12 lines max) plus
   full versions for handout/repo

Read `references/example-generation.md` for detailed guidance on creating high-quality
examples.

### Phase 5: Apply Changes to Slides

Follow the PPTX skill's editing workflow for modifications and PptxGenJS for new slides:

**For existing slide edits:**
- Unpack → Edit XML → Clean → Pack
- Preserve all formatting, backgrounds, and branding
- Match the existing visual style exactly

**For new slides:**
- Use PptxGenJS following Brent's slide design conventions
- Every slide must be visually interesting (never just text + bullets)
- Include proper citations for any new statistics or claims
- Place new slides in the correct narrative position

**For slide removal:**
- Remove from `<p:sldIdLst>` in presentation.xml
- Verify the narrative flow still works without the removed slide
- Update the script to bridge the gap

**Visual requirements for all changes:**
- Follow the color palette from slide-design.md
- Use varied layouts — don't repeat consecutive patterns
- Include visual elements on every content slide
- Citations on every slide with stats/references
- Speaker notes on every modified or new slide

### Phase 6: Apply Changes to Script

Update the speaker script to match slide changes:

- **Modified slides** — revise the corresponding script sections
- **New slides** — write new script sections in Brent's voice
- **Removed slides** — remove script sections and smooth transitions
- **Reordered slides** — reorder script sections and update transitions
- **New examples/demos** — add spoken walkthroughs and setup instructions
- **Timing** — recalculate word count and verify against talk duration

Maintain Brent's voice: conversational, knowledgeable, friendly but authoritative.
Use contractions, rhetorical questions, and natural transitions.

### Phase 7: Visual QA & Verification

This is non-negotiable. Every modified or new slide must be visually inspected.

1. **Convert to images** and inspect every changed slide
2. **Check for:** overlapping elements, text overflow, wrong backgrounds,
   inconsistent styling, low contrast, leftover placeholder content
3. **Cross-verify:** slides ↔ script alignment, citation accuracy, timing
4. **Fix issues** and re-verify — repeat until clean

### Phase 8: Produce Deliverables

Generate three output files:

1. **Updated slide deck** — `[talk-slug]_improved.pptx`
2. **Updated speaker script** — `[talk-slug]_improved-script.md`
3. **Change report** — `[talk-slug]_change-report.md`

The change report is the most important documentation artifact. It must contain:

```markdown
# Change Report: [Talk Title]
**Date:** [date] | **Duration:** [X] minutes | **Original slides:** [N] | **Updated slides:** [N]

## Summary
[2-3 paragraph overview of what was changed and why]

## Changes Applied

### [Eval Report Section Name]

#### Item: [item description]
- **Evaluation recommendation:** [what was suggested]
- **Decision:** AGREE + APPLY | AGREE + MODIFY | DISAGREE + SKIP
- **What was changed:** [specific changes made]
- **Affected slides:** [slide numbers]
- **Rationale:** [why this change improves the talk]

[...repeat for every item...]

## Changes NOT Made

### [Item description]
- **Evaluation recommendation:** [what was suggested]
- **Decision:** DISAGREE + SKIP
- **Rationale:** [detailed explanation of why this wasn't implemented]

## New Examples & Demos Added
[List each new example with a brief description and where to find the full code]

## Timing Analysis
| Section | Original Duration | Updated Duration |
|---------|------------------|-----------------|
| ... | ... | ... |
| **Total** | **X min** | **X min** |

## Notes for the Speaker
[Any delivery suggestions, transition tips, or callouts about the changes]
```

---

## Important Reminders

- **Evaluate every item independently.** Don't rubber-stamp the entire report — think
  critically about each suggestion. Some may be wrong, redundant, or impractical.
- **Time constraints matter.** If the talk is 30 minutes, you can't add 15 minutes of
  new content. Every addition should be balanced by tightening elsewhere.
- **Examples must be real and runnable.** No placeholder code, no "imagine this works"
  demos. Every code example should be complete enough to actually execute.
- **Visual quality is non-negotiable.** Slides must look professional and match Brent's
  existing branding. Never create plain text-only slides.
- **The change report documents your reasoning.** It's not just a list — it explains the
  thinking behind every decision, including things you chose NOT to change.
- **Preserve the narrative arc.** Changes should strengthen the talk's story, not fragment it.
  After all changes, the talk should flow better than before, not feel patched together.
