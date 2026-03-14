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

## When Creating New Slides

1. Start by reading the labs.md to understand what concepts need slide coverage
2. Outline the slide structure: title → concept sections → lab transitions → summary
3. Extract theme from existing .pptx in the repo
4. Create slides using the pptx skill
5. Add speaker notes to every slide
6. Verify alignment: every lab concept has preceding slide coverage

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
