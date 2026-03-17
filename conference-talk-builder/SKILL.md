---
name: conference-talk-builder
description: >
  Build complete conference talk presentations from a title and abstract. Creates an outline,
  a visually rich slide deck (.pptx), and a full speaker script — all aligned and verified.
  Use this skill whenever the user wants to create a conference talk, conference presentation,
  keynote, tech talk, meetup presentation, summit talk, or any public-speaking slide deck
  intended for a live audience. Also trigger when the user says "build me a talk", "create
  a presentation for [conference name]", "I'm speaking at...", "prepare my talk", "conference
  slides", or references preparing a talk with an abstract or CFP submission. This skill handles
  the full pipeline: research, outline, slides, script, and cross-verification. Do NOT use for
  internal training decks (use training-course-builder instead) or for general slide editing
  (use pptx instead).
---

# Conference Talk Builder

You are helping Brent Laster (Tech Skills Transformations / TechUpSkills) create a complete
conference talk from a title and abstract. The deliverables are: a talk outline, a polished
slide deck, and a speaker script — all verified against each other and the source material.

## Before You Start

1. **Read the pptx skill** — you'll need it for building slides:
   Read the PPTX skill's SKILL.md, then read its `pptxgenjs.md` reference (for creating
   from scratch). The PPTX skill lives at the sibling path: find it with
   `ls ../pptx/SKILL.md` relative to this skill, or search for it.

2. **Read this skill's reference files:**
   - `references/slide-design.md` — Brent's slide conventions, theme, colors, fonts, and
     layout patterns extracted from his existing decks
   - `references/workflow.md` — Detailed step-by-step workflow for the full pipeline

3. **Collect inputs from the user.** You need these before proceeding:

### Required Inputs

Ask the user for these using the AskUserQuestion tool (or gather from conversation context):

**Already provided:**
- Talk title
- Talk abstract

**Must ask:**
- **Time allocation**: "How much time do you have for this talk?" Offer common options:
  20 min, 30 min, 45 min, 60 min, 90 min. This drives slide count and depth.
- **Audience level**: "What's the general audience level for this topic?" Options:
  Beginner, Intermediate, Advanced. This shapes vocabulary, assumed knowledge, and
  how much foundational material to include vs. diving deep.

**Optional (ask if unclear from abstract):**
- Any specific technologies, products, or frameworks to emphasize
- Any demos or live-coding segments planned (affects time budget)
- Conference name and context (helps tailor examples and tone)

---

## Workflow Overview

The full pipeline has 8 phases. Read `references/workflow.md` for detailed instructions
on each phase. Here's the high-level flow:

### Phase 1: Research
Research the current state of the industry related to the talk topic. Look for:
- Recent developments, announcements, and trends (within last 12 months)
- Interesting studies, reports, and statistics (with verifiable citations)
- How-to resources, best practices, and common patterns
- Contrarian views or surprising findings that make the talk memorable
- Real-world case studies and examples

Use web search extensively. Every statistic and claim needs a source URL that you will
verify later. Collect more than you need — you'll curate during outlining.

### Phase 2: Outline
Synthesize research into a structured outline with a clear narrative arc:
- **Opening hook** (1-2 min): A compelling story, surprising stat, or provocative question
- **Context/problem** (10-15% of time): Why this topic matters right now
- **Core content** (60-70% of time): 3-5 key sections with supporting evidence
- **Practical takeaways** (10-15% of time): What the audience should do Monday morning
- **Closing** (1-2 min): Memorable conclusion that ties back to the opening

Target slide counts by duration:
| Duration | Content Slides | Total (with title/end) |
|----------|---------------|----------------------|
| 20 min   | 12-15         | 15-18                |
| 30 min   | 18-22         | 21-25                |
| 45 min   | 25-32         | 28-35                |
| 60 min   | 32-40         | 35-43                |
| 90 min   | 45-55         | 48-58                |

Save the outline as `[talk-slug]-outline.md` in the output directory.

### Phase 3: Build Slides
Create the slide deck using PptxGenJS (from-scratch creation). Follow the conventions
in `references/slide-design.md` exactly — Brent's theme, colors, fonts, and branding.

Key requirements:
- **First slides**: Version slide + Title slide matching Brent's existing branding
- **Last slide**: "That's all - thanks!" slide with contact info and book covers
- **Every slide must be visual** — never just text and bullets. Use diagrams, charts,
  icons, code examples, or stock images. Read `references/slide-design.md` for the
  visual approach hierarchy.
- **Citations on every slide with stats/references** — include source name and year
  as small text at the bottom of the slide
- **Section divider slides** for major topic transitions
- **Varied layouts** — don't repeat the same layout pattern consecutively

### Phase 4: Script
Write a full speaker script in Brent's voice: conversational, knowledgeable, friendly
but authoritative. The script should:
- Be timed to match the allocated duration (roughly 130-150 words per minute)
- Reference specific slides by number
- Include transition phrases between sections
- Note where to pause, where to ask the audience, where to gesture at the screen
- Sound natural when read aloud — use contractions, rhetorical questions, brief asides

Save as `[talk-slug]-script.md` in the output directory.

### Phase 5: Cross-Verification
This is critical. Go through all three deliverables and verify:

1. **Outline ↔ Slides**: Every outline section has corresponding slides. No slides
   exist that aren't in the outline. The narrative flow matches.
2. **Slides ↔ Script**: Every slide is referenced in the script. The script covers
   every slide's content. Timing adds up to the allocated duration.
3. **Citations**: EVERY statistic, claim, or reference in the deck has:
   - A citation on the slide (source name, year)
   - A verified source URL (use web search to confirm the citation is real and accurate)
   - If a citation can't be verified, either find a replacement or remove the claim

### Phase 6: Visual QA
Convert the deck to images and inspect every slide for visual issues. Follow the
PPTX skill's QA process — use subagents for fresh-eyes review. Fix any issues found.

### Phase 7: Deliver Files
Save all deliverables to the output directory:
- `[talk-slug]-outline.md` — The talk outline
- `[talk-slug].pptx` — The slide deck
- `[talk-slug]-script.md` — The speaker script

### Phase 8: Summary
Present the user with a summary: slide count, estimated timing, key narrative arc,
and any notes or suggestions for delivery.

---

## Important Reminders

- **Never skip the citation verification step.** Every stat must be traceable to a real source.
- **Slides should never be text-heavy.** If a slide has more than 5 short bullet points,
  it probably needs to be split or redesigned as a visual.
- **The narrative matters more than individual slides.** The talk should tell a story with
  a clear throughline, not just present a collection of facts.
- **Match the audience level.** Beginner talks need more context and analogies. Advanced
  talks can assume vocabulary and dive into nuance. Intermediate is the hardest — balance
  accessibility with depth.
- **Timing is critical.** A 30-minute talk with 40 slides will feel rushed. A 45-minute
  talk with 15 slides will drag. Use the target counts as guides.
