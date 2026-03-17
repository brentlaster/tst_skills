# Detailed Workflow

This document provides step-by-step instructions for each phase of the conference talk
building pipeline.

---

## Phase 1: Research (Thorough)

The research phase is the foundation. A talk is only as good as its content, and conference
audiences expect current, accurate information with real-world relevance.

### What to Research

Given the talk title and abstract, search for:

1. **Recent developments** (last 6-12 months):
   - New product releases, framework updates, major announcements
   - Industry shifts, adoption trends, emerging patterns
   - Use queries like: "[topic] 2025 trends", "[topic] latest developments"

2. **Statistics and data**:
   - Adoption rates, market size, growth projections
   - Survey results from reputable sources (Stack Overflow, Gartner, McKinsey, etc.)
   - Performance benchmarks, comparison data
   - EVERY stat needs a source URL — collect these meticulously

3. **Case studies and real-world examples**:
   - Companies using the technology successfully (and unsuccessfully)
   - Before/after stories, migration stories, lessons learned
   - Open-source projects, community examples

4. **Contrarian or surprising angles**:
   - Common myths vs. reality
   - Unexpected findings from studies
   - Things the audience probably believes that aren't quite right
   - These make talks memorable and spark conversation

5. **How-to resources and best practices**:
   - Official documentation, getting-started guides
   - Community-developed patterns, anti-patterns
   - These inform code examples and practical slides

6. **Related talks and content** (to differentiate):
   - What have other speakers said about this topic recently?
   - What angle hasn't been covered well?
   - How can Brent's talk add unique value?

### Research Output

Compile findings into a structured research document (internal, not a deliverable).
Organize by theme/section. For each finding, record:
- The fact or insight
- The source (org/author, year, URL)
- Potential use: which part of the talk it supports
- Confidence level: verified, likely accurate, needs checking

---

## Phase 2: Outline Construction

### Narrative Arc

Every talk needs a story. The outline is not just "Topic A, Topic B, Topic C" — it's a
narrative that takes the audience on a journey.

**Structure template:**

```markdown
# [Talk Title]

## Opening Hook (1-2 min)
[Compelling story / surprising stat / provocative question]
- Goal: Capture attention, establish relevance
- The audience should think: "I need to hear this"

## Context: Why This Matters Now (X min)
[The problem or opportunity that makes this topic timely]
- Current state of the industry
- Pain points the audience recognizes
- Why the old way isn't working anymore (or what new possibilities exist)

## Core Section 1: [Name] (X min)
[Key concept or finding]
- Supporting evidence / data
- Example or case study
- Visual: [what diagram/chart/image will illustrate this]
- Slide count: N slides

## Core Section 2: [Name] (X min)
[Continue building the narrative...]

## Core Section 3: [Name] (X min)
[The "aha moment" — this is usually where the most impactful content lives]

## Practical Takeaways (X min)
[What should the audience do with this knowledge?]
- 3-5 concrete, actionable items
- "When you get back to your desk on Monday..."
- Resources and next steps

## Closing (1-2 min)
[Tie back to the opening hook — bookend the talk]
- Memorable final thought
- Call to action
- Contact info
```

### Time Budget

Allocate time to each section explicitly. Write the minute count next to each section.
The total must match the allocated talk time. Leave 2-3 minutes of buffer for audience
reaction, transitions, and running slightly over.

### Audience Level Adjustments

**Beginner:**
- More context and background in the early sections
- Define technical terms when first introduced
- Use analogies to familiar concepts
- More "why" and "what", less "how" in early sections
- Code examples should be simple and well-commented

**Intermediate:**
- Can skip basic definitions but establish shared vocabulary
- Balance "why" and "how"
- Include some "gotchas" and edge cases
- Code examples can be more realistic

**Advanced:**
- Minimal background — get to the substance quickly
- Focus on nuance, trade-offs, and hard-won lessons
- Include advanced patterns, performance considerations
- Assume the audience has tried this and hit walls
- The value is in the unique perspective, not the introduction

### Save the Outline

Save as `[talk-slug]-outline.md` using this format:

```markdown
# [Talk Title] — Outline
**Duration:** [X] minutes | **Audience:** [Level] | **Conference:** [Name if known]

## Opening Hook (X min)
...

## Section Name (X min)
- Key point 1
  - Supporting detail
  - Visual: [description]
  - Slide(s): [count]
- Key point 2
  ...

[...continue for all sections...]

## Closing (X min)
...

---
### Timing Summary
| Section | Duration | Slides |
|---------|----------|--------|
| Opening | X min | N |
| ... | ... | ... |
| **Total** | **X min** | **N** |

### Key Citations
| Claim | Source | URL | Verified |
|-------|--------|-----|----------|
| ... | ... | ... | ✓/✗ |
```

---

## Phase 3: Slide Construction

### Setup

Read the PPTX skill and its pptxgenjs.md reference for the technical how-to.
Read `slide-design.md` for Brent's specific conventions.

### Slide Order

1. **Version slide** — "Version 1.0 | [date]"
2. **Title slide** — Talk title, Brent's branding
3. **About Me slide** (optional, include for conferences)
4. **Content slides** — following the outline exactly
5. **Ending slide** — "That's all - thanks!" with contact info

### Building Each Slide

For each content slide:

1. Determine the best layout from the options in `slide-design.md`
2. Write the slide title (concise, meaningful — not generic)
3. Create the visual element FIRST — decide what diagram, chart, icon set, or image
   best communicates the point
4. Add text content — keep it minimal. The slide supports the speaker, not the other way around
5. Add citation if the slide contains any referenced data
6. Check that it follows the color/font conventions

### Visual Generation

**Diagrams and process flows:**
Generate using JavaScript canvas/SVG, save as PNG, embed in slide.
Match the color palette. Keep clean and readable.

**Charts:**
Use PptxGenJS native charts with custom colors from the palette.
Style: `chartColors: ["16A085", "003366", "00A79D", "002060"]`

**Icons:**
Use react-icons rendered to PNG via sharp. Place in colored circles
(`16A085` fill, white icon, or white fill with `16A085` icon).

**Code blocks:**
Monospace font on a tinted rectangle (`F0F0F0` fill). Keep to 8-12 lines.
Add brief annotations.

**Stock images:**
Search for relevant images. Describe what to search for if automated image
retrieval isn't available — the user can supply images later.

### Speaker Notes

Add speaker notes to every content slide. These aren't the full script — they're
brief reminders: key points to hit, transition cues, timing markers.
Include full citation URLs in the notes for reference slides.

---

## Phase 4: Script Writing

### Voice

Brent's presentation style is conversational expert: friendly, knowledgeable,
and authoritative. Think "experienced colleague explaining something cool at
a team meeting" — not "professor lecturing" or "salesperson pitching."

**Voice characteristics:**
- Uses contractions naturally ("Here's the thing...", "You'd think...")
- Asks rhetorical questions ("So why does this matter?", "Sound familiar?")
- Uses brief asides and personal observations ("I've been working with this for
  a couple years now and the thing that always surprises me is...")
- References real experience without being boastful
- Acknowledges complexity without being academic
- Makes the audience feel smart, not talked down to
- Uses humor naturally (dry, observational) but doesn't force jokes
- Transitions smoothly between topics with bridging phrases

**Avoid:**
- Corporate jargon: "leverage", "synergize", "paradigm shift"
- Filler phrases: "So basically...", "Like I said..."
- Overly formal language: "One should consider..."
- Reading the slides verbatim — the script adds context and story the slides don't have

### Script Format

```markdown
# [Talk Title] — Speaker Script
**Duration:** [X] minutes | **Target pace:** ~140 words/min

---

## [SLIDE 1 — Version]
*[Skip — just advance]*

## [SLIDE 2 — Title]
*[Wait for audience to settle]*

Good [morning/afternoon], everyone. Thanks for being here. I'm Brent Laster, and today
we're going to talk about [topic]...

[Continue with opening hook from outline]

## [SLIDE 3 — About Me]
A quick bit about me — [brief intro, keep to 30 seconds]

## [SLIDE N — Section Start]
*[TRANSITION: bridging sentence from previous section]*

[Full spoken content for this slide. Include everything Brent would say while this
slide is visible.]

*[PAUSE — let the diagram sink in]*
*[GESTURE at the right side of the slide]*

[Continue...]

---

## Timing Checkpoints
| Slide | Elapsed Time | Section |
|-------|-------------|---------|
| 5 | ~5 min | End of intro |
| 15 | ~20 min | Midpoint |
| ... | ... | ... |
```

### Timing

Calculate word count per section. At ~140 words/min:
- 20 min talk ≈ 2,800 words
- 30 min talk ≈ 4,200 words
- 45 min talk ≈ 6,300 words
- 60 min talk ≈ 8,400 words
- 90 min talk ≈ 12,600 words

Include timing checkpoints in the script so Brent can pace himself during delivery.

---

## Phase 5: Cross-Verification

This is the quality gate. Do not skip any of these checks.

### Check 1: Outline ↔ Slides
- Open the outline and the slide content side by side
- For each outline section, verify matching slides exist
- For each slide, verify it maps to an outline section
- Check the narrative flow: does the slide order tell the same story as the outline?
- Flag any orphaned slides or missing outline sections

### Check 2: Slides ↔ Script
- For each slide, find the corresponding script section
- Verify the script covers the slide's content (not just repeats it — adds value)
- Check that no slides are skipped in the script
- Verify timing: word count per section matches time allocation

### Check 3: Citation Audit
This is the most critical check. For EVERY citation in the deck:

1. Find the claim on the slide
2. Find the source name and year on the slide
3. Look up the source URL (should be in speaker notes)
4. **Web search to verify the citation is real and accurate**
   - Does the source exist?
   - Does it say what the slide claims?
   - Is the year correct?
   - Is the number/stat accurate?
5. If verification fails:
   - Try to find a replacement source that supports the same claim
   - If no replacement exists, remove the claim or reword as opinion
   - Document what was changed and why

### Verification Report

Create a brief internal verification log:
```
Citation: "78% of organizations use AI" — Source: Vena Solutions, 2025
Status: ✓ VERIFIED — https://www.venasolutions.com/blog/ai-statistics
```

Fix any issues found before delivering files.

---

## Phase 6: Visual QA

Follow the PPTX skill's QA process:

1. Convert slides to images:
   ```bash
   python scripts/office/soffice.py --headless --convert-to pdf [deck].pptx
   pdftoppm -jpeg -r 150 [deck].pdf slide
   ```

2. Use a subagent (or inspect yourself if no subagents available) to review every
   slide image for:
   - Overlapping elements
   - Text overflow or cut-off
   - Low contrast text
   - Inconsistent spacing
   - Missing visual elements
   - Citation text that's too small to read
   - Color inconsistencies with the palette

3. Fix issues and re-verify affected slides

---

## Phase 7: Deliver Files

Save all deliverables to the output directory (workspace folder):
- `[talk-slug]-outline.md`
- `[talk-slug].pptx`
- `[talk-slug]-script.md`

Use a slug derived from the talk title (lowercase, hyphens, no special chars).
Example: "AI Agents in Enterprise" → `ai-agents-in-enterprise`

---

## Phase 8: Summary

Present the user with:
- Total slide count
- Estimated talk duration based on script word count
- The narrative arc in 2-3 sentences
- Any caveats (e.g., "I used placeholder descriptions for 3 stock images — you may want
  to substitute your own")
- Suggestions for delivery (e.g., "Consider doing a brief live demo after slide 15")
