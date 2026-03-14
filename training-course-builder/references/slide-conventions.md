# Slide Conventions

Guidelines for creating and updating PowerPoint presentations for TechUpSkills training courses.

## General Principles

### Template & Theme
- Always extract and reuse the theme from Brent's existing .pptx files in the course repo
- When an existing .pptx is available, use it as the style reference for colors, fonts, layouts
- Maintain visual consistency across all slides in a deck

### Content Philosophy
- Slides provide the **conceptual framework**; labs provide the **hands-on practice**
- Avoid walls of text — use visuals, diagrams, and minimal bullet points
- Each slide should convey ONE main idea
- Use the "tell, show, do" pattern: slides tell and show, labs do

### Slide-Lab Alignment
- Slide sections should map directly to lab sections
- When possible, include a "transition slide" before each lab that summarizes what students will do
- Concept slides should appear BEFORE the lab that exercises that concept

## Content Structure

### Title Slide
- Course full name
- Day/session number and topic
- Instructor name: Brent Laster
- Organization: TechUpSkills / Tech Skills Transformations
- Revision number and date

### Section Dividers
- Use a consistent divider slide layout between major topics
- Include the section title and optionally a brief description

### Concept Slides
- **Diagrams over text**: Architecture diagrams, flow charts, comparison tables
- **Code snippets**: Keep them short (5-10 lines max), highlight the key parts
- **Key terms**: Bold or highlight new terminology when introduced
- **Real-world context**: Explain why this matters in enterprise settings

### Summary/Transition Slides
- Brief recap of concepts covered
- What students will do next in the lab
- Key things to look for

## Formatting Standards

### Text
- Use the deck's built-in font (extract from existing template)
- Title text: prominent, clear hierarchy
- Body text: readable from the back of a room (aim for 24pt minimum)
- Code snippets: monospace font, syntax-appropriate formatting

### Diagrams
- Use consistent colors for the same concepts across slides
- Label all components clearly
- Use arrows to show data flow or process direction
- Keep diagrams clean — avoid overcrowding

### Speaker Notes
- Include talking points that expand on slide content
- Note timing guidance ("spend ~5 min on this slide")
- Include transition cues ("After this, students will do Lab 3")
- Add extra context or examples the instructor might want to mention
- Note any demo instructions if the instructor shows something live

## Animations

Animations are a major part of instructional slide design — progressive reveal of diagrams,
step-by-step build-ups, and controlled pacing all improve learning. Follow these guidelines:

### When to use animations
- **Architecture build-ups**: Progressively reveal components of a system diagram so students
  see how pieces fit together (e.g., add the database, then the API layer, then the frontend)
- **Process flows**: Reveal steps one at a time to walk through a pipeline
- **Comparison slides**: Show option A, then reveal option B for comparison
- **Code walkthroughs**: Highlight different sections of code as you explain each part

### Animation best practices
- **Use "On Click" triggers** for instructor-controlled pacing — avoid "After Previous" timers
  in training decks since instructor pacing varies
- **Keep click counts under 20 per slide** for smooth delivery. Slides with more than 20 click
  effects are hard to deliver without losing track of where you are
- **Follow a logical reveal order**: title first, then points in order, not random jumps around
  the slide. The build-up should tell a coherent visual story
- **Be consistent within sections**: If some slides in a section have animations and others don't,
  it creates awkward pacing for the instructor. Either animate all slides in a section or none
- **Test in PowerPoint Online**: Slides with very high animation counts (100+ sequences) may fail
  to play animations entirely in PowerPoint Online's slideshow mode. If your deck will be shared
  electronically, keep animation complexity moderate

### Animation pitfalls to avoid
- **Orphaned animation targets**: When editing slides, deleting a shape without removing its
  animation entry creates a silent failure during presentation. Always check animation XML if
  shapes are removed
- **Excessive complexity**: A slide with 130+ animation sequences is nearly impossible to deliver
  smoothly. Consider splitting into multiple slides instead
- **"After Previous" chains**: Long chains of auto-timed animations are fragile — if one timing
  is off, everything cascades. Prefer manual click-through for training delivery

## When Creating New Slides

1. Start by reading the labs.md to understand what concepts need slide coverage
2. Outline the slide structure: title → concept sections → lab transitions → summary
3. Extract theme from existing .pptx in the repo
4. Create slides using the pptx skill — **but see "Slide Master Matching" below**
5. Add speaker notes to every slide
6. Verify alignment: every lab concept has preceding slide coverage

### CRITICAL: Slide Master Matching

**DO NOT use `python-pptx` to create new slides.** The `prs.slide_layouts[N]` API often selects
the wrong slide master, producing slides with completely different backgrounds, colors, and fonts.
This is especially common with decks that have multiple slide masters (frequent when imported from
Google Slides — Master 0 might be a generic white theme while Master 1 has the branded design).

**Instead, use the `add_slide.py` duplicate-and-edit workflow:**
1. **Identify the correct template slide** — find an existing content slide with the layout and
   visual style you want. Render thumbnails to compare.
2. **Duplicate it**: `python scripts/add_slide.py <deck_unpacked>/ <source_slide> <new_slide>`
   This copies the slide XML, all relationships (including background image refs), and ensures
   the new slide uses the same slide layout and slide master as the source.
3. **Edit the duplicated XML directly** — replace titles, body text, and shapes while preserving
   the background image reference (typically `rId3` for the full-slide background PNG) and the
   relationship file structure.
4. **Insert into presentation.xml** — add a `<p:sldId>` entry at the correct position in
   `<p:sldIdLst>` to control where the slide appears in the deck order.

**python-pptx IS safe for:**
- Adding speaker notes (does not affect slide master assignment)
- Updating text in existing shapes (text replacement within existing runs)
- Year/date replacements across slide masters, layouts, and slides

**python-pptx is NOT safe for:**
- Creating new slides or duplicating slides — always use `add_slide.py`

### Background Image Awareness

Many decks use full-slide background PNGs (covering the entire slide area) referenced as `rId3`
in the slide XML. These contain the branded header, footer, decorative elements, and color scheme.
When editing slide XML:
- Always preserve the background `<p:pic>` element
- Place content shapes ON TOP of this background
- Check the slide's `.rels` file to confirm which `rId` maps to the background image

### Visual Design Requirements for New Slides

New slides must include visual design elements — colored rounded-rectangle cards, comparison boxes,
two-column layouts with callout banners, or diagrams. Do NOT create text-only slides. Use the
deck's existing visual vocabulary (e.g., if the deck uses 3-column card layouts, replicate that
pattern). Aim for full-width use of the slide area; avoid layouts where content is clustered on
one side leaving large empty areas.

## When Updating Existing Slides

1. Read the existing .pptx to understand current structure
2. Identify what needs to change:
   - Version numbers (in text, code snippets, diagrams)
   - API/library name changes
   - New concepts added to labs that need slide coverage
   - Outdated diagrams or architectures
3. Make targeted updates, preserving the existing layout and theme
4. Update speaker notes to reflect changes
5. If output formats changed, flag screenshots/diagrams for refresh

## Common AI/ML Slide Topics

For Brent's AI/ML courses, slides typically cover:
- Neural network fundamentals (architectures, training, activation functions)
- Tokenization and embeddings (subword tokens, vector spaces, similarity)
- Transformer architecture (attention, encoder/decoder, key models)
- LLM capabilities (translation, classification, sentiment, generation)
- RAG pipeline (retrieval, augmentation, generation)
- Vector databases (ChromaDB, embedding storage, similarity search)
- Knowledge graphs (Neo4j, Cypher, graph traversal)
- Hybrid RAG (combining semantic + graph search)
- RAG evaluation (relevance, groundedness, completeness, hallucination)
- Advanced RAG patterns (CRAG, re-ranking)
- Fine-tuning concepts and approaches
- Enterprise considerations (security, evaluation, deployment)
