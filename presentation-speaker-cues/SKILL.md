---
name: presentation-speaker-cues
description: "Add speaker memory aids to presentation decks paired with scripts. Takes a .pptx slide deck and a matching speaker script (.md), then adds concise speaker notes to every slide, optionally inserts new 'cue slides' with visual/text memory aids, adjusts the script when slides are added, and produces a change report with memorization tips. Trigger on: 'add speaker notes', 'presentation cues', 'help me remember my talk', 'speaker hints', 'memory aids for slides', 'presentation memory helpers', 'cue slides', 'I can't memorize this script', or any request to add notes, cues, or memory aids to a deck+script pair. Also trigger when the user mentions they need help remembering what to say during a presentation, or wants notes added to slides to support delivery of a talk."
---

# Presentation Speaker Cues Skill

Add memory aids, speaker notes, and optional cue slides to a presentation deck so the speaker can deliver their talk without memorizing a full script.

## When to Use

Use this skill whenever someone has:
- A `.pptx` slide deck AND a matching speaker script (`.md` file)
- The script sections are delimited by slide markers (e.g., `[SLIDE N ...]` or `**SLIDE N ...**`)
- They want help remembering what to say during presentation delivery

## Overview of What This Skill Produces

1. **Updated deck** (new copy) with speaker notes on every slide containing:
   - Condensed bullet-point talking points (not the full script)
   - Key phrases and trigger words to jog memory
   - Mnemonics for lists, sequences, or statistics
   - Transition cues ("Next: move to retrieval...")
   - Timing hints where available

2. **Optional new "cue slides"** inserted into the deck that:
   - Display key statistics, lists, or frameworks that would be hard to remember
   - Follow the same visual theme as the rest of the deck
   - Are non-distracting (they look like natural content slides)
   - Help the speaker by putting hard-to-memorize content on screen

3. **Updated script** (new copy) — only modified if new slides were added:
   - New slide delimiter sections are inserted to match added slides
   - The surrounding script text is redistributed so each slide section matches what the speaker says while that slide is showing
   - The delimiter format matches whatever the original script uses (`[SLIDE ...` or `**SLIDE ...`)

4. **Change report** (.md) documenting:
   - Every note added and what it contains
   - Every new slide added and why
   - Mnemonics explained in detail
   - General tips for remembering the talk

## Step-by-Step Process

### Phase 1: Analyze

1. Read the pptx skill's SKILL.md first (for deck manipulation guidance)
2. Read both the deck (via `markitdown`) and the full script
3. Map each slide number to its script section
4. Identify the slide delimiter format used in the script (`[SLIDE` vs `**SLIDE` etc.)
5. Catalog per slide:
   - Key talking points from the script
   - Statistics, names, dates, or specific facts that are hard to memorize
   - Lists or sequences (candidates for mnemonics)
   - Transitions to the next topic
   - Stage directions (gestures, pauses, polls)

### Phase 2: Design Speaker Notes

For each slide, write concise speaker notes following these principles:

**Format:**
- Use short bullet points, not full sentences
- Lead with a trigger keyword or phrase in CAPS or bold
- Include the first few words of key sentences (the speaker's brain fills in the rest)
- Add transition cue as the last bullet: "NEXT: [topic of next slide]"

**Memory Aids:**
- For lists of N items, create a mnemonic acronym or phrase
  - Example: Six Pillars = "I R M F C D" -> "I Remember My First Conference Day"
- For statistics, pair the number with a vivid association
  - Example: "40% = nearly half" or "+10.6% = a full letter grade"
- For before/after comparisons, use a one-word contrast: "Generic vs. Targeted"
- For sequences, number them explicitly: "Step 1/4, 2/4, 3/4, 4/4"

**What NOT to put in notes:**
- The full script text (defeats the purpose)
- Overly detailed explanations
- Anything longer than ~8 bullet points per slide

### Phase 3: Identify Cue Slide Opportunities

Add a new slide when:
- The script contains a list of 4+ items that the speaker must recite from memory AND those items are NOT already visible on the current slide
- There is a complex framework, decision tree, or multi-step process described verbally
- There are specific statistics or research citations the speaker needs to quote accurately
- A visual summary would naturally fit the presentation flow without feeling forced

Do NOT add a cue slide when:
- The information is already on the existing slide
- It would break the narrative flow
- It would feel like filler or repetition

New slides should:
- Use the same visual theme, colors, and fonts as surrounding slides
- Look like intentional content slides (not cheat sheets)
- Display the hard-to-remember content in a clean, visual format

### Phase 4: Build the Deck

Use the pptx editing workflow:
1. Unpack the deck: `python scripts/office/unpack.py input.pptx unpacked/`
2. **Determine presentation order** (critical — see warning below)
3. Add speaker notes to each slide's XML (in `<p:notes>` / notes slide files)
4. If adding new slides, use `python scripts/add_slide.py` to duplicate a thematically similar slide, then edit its content
5. Update `<p:sldIdLst>` ordering if slides were added
6. Clean: `python scripts/clean.py unpacked/`
7. Pack: `python scripts/office/pack.py unpacked/ output.pptx --original input.pptx`

**CRITICAL — Slide File Numbers vs. Presentation Order:**
Slide file names (slide1.xml, slide2.xml, ...) do NOT necessarily match the order slides appear in the presentation. For example, `slide38.xml` might be displayed at position 12. The actual display order is defined by the `<p:sldIdLst>` in `ppt/presentation.xml`, where each `<p:sldId>` entry has an `r:id` that maps to a slide file via `ppt/_rels/presentation.xml.rels`.

You MUST build a presentation-order-to-file mapping before assigning notes:
1. Read `ppt/presentation.xml` to get the ordered list of `r:id` values from `<p:sldIdLst>`
2. Read `ppt/_rels/presentation.xml.rels` to map each `r:id` to a slide filename
3. This gives you: position 1 → slideX.xml, position 2 → slideY.xml, etc.
4. Assign notes based on POSITION (what the audience sees), writing them to whichever file is at that position

If you skip this step and assume file numbers match display order, notes will be misaligned for any deck where slides were inserted, reordered, or deleted during authoring.

**Speaker Notes XML Pattern:**
Notes are stored in `ppt/notesSlides/notesSlideN.xml`. If a notes slide doesn't exist for a given slide, you need to create one by duplicating an existing notes slide and updating the relationship in the slide's `.rels` file.

When creating a new rels file for a slide that previously had none, check whether the slide XML already references rIds for images (e.g., `<a:blip r:embed="rId2"/>`). If so, use a higher rId number for the notes relationship to avoid conflicting with those existing references. For example, if the slide uses rId1 through rId6 for images, assign the notes relationship to rId7.

### Phase 5: Update the Script (if new slides were added)

For each new slide inserted:
1. Identify the slide delimiter format (detect from existing script):
   - `## [SLIDE N — Title]` format
   - `## **SLIDE N — Title**` format
   - Other variants
2. Insert a new section at the correct position
3. Move the relevant portion of the surrounding script text into the new section
4. Renumber all subsequent slide references if needed (or use letter suffixes like "11b" to avoid renumbering)
5. Preserve all stage directions, timing notes, and formatting

### Phase 6: Create the Change Report

Write a markdown report containing:

1. **Summary of Changes** — how many notes added, how many slides added
2. **Per-Slide Notes Summary** — for each slide, what the notes contain
3. **New Slides Added** — for each new slide, what it shows and why
4. **Mnemonics Guide** — every mnemonic used, fully explained, so the speaker can practice
5. **General Memory Tips** for this specific talk:
   - Suggested practice routine
   - Key anchor phrases to memorize (the rest flows naturally)
   - Story arc summary (beginning → middle → end in 3 sentences)
   - "If you forget everything else, remember these 5 things"

### Phase 7: Verify

- Run `markitdown` on the output deck to confirm notes are present
- **Verify notes-to-slide alignment**: For each slide in the `markitdown` output, check that the slide title/content matches the notes content. Specifically spot-check slides in the middle and end of the deck, since those are most likely to be misaligned if file-order vs. presentation-order was handled incorrectly.
- Confirm slide count matches expectations
- Confirm script slide sections match deck slide count
- Review notes for conciseness (trim any that exceed 8 bullets)
- If any notes are misaligned, go back to Phase 4 and rebuild the position-to-file mapping
