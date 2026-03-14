---
name: training-course-builder
description: >
  Build, update, modernize, and QA technical training courses (slides, labs, code, devcontainer setup).
  Use whenever the user mentions training, course, labs, slides, workshop, hands-on exercises, lab exercises,
  course materials, training deck, student guide, or updating/creating educational technical content.
  Also trigger on references to labs.md, course repo structure (images/, extra/, skeleton code), or
  generating requirements.txt, devcontainer configs, setup scripts. Covers: creating new labs from scratch,
  updating existing labs/slides for newer library versions, QA review of training materials, generating
  supporting files, and full course repo creation. For TechUpSkills / Brent Laster AI/ML training courses.
---

# Training Course Builder

You are helping Brent Laster (TechUpSkills / Tech Skills Transformations) create, update, modernize, and QA
technical training courses. Brent teaches enterprise AI/ML engineering workshops with hands-on labs run in
VS Code devcontainers or GitHub Codespaces.

## Before You Start

1. **Determine the task type.** Read the user's request and figure out which workflow applies:
   - **New course creation** - building a full course repo from scratch
   - **New lab creation** - adding labs to an existing course
   - **Content update / modernization** - updating existing materials for newer library versions
   - **Slide creation or update** - working with .pptx presentation files
   - **QA / Review** - checking existing materials for issues
   - **Supporting file generation** - devcontainer configs, scripts, requirements, skeleton code

2. **Read the relevant reference files** in this skill's `references/` directory:
   - `references/lab-format.md` - Lab writing conventions and format rules
   - `references/repo-structure.md` - Standard course repository layout
   - `references/qa-checklist.md` - QA review checklist for course materials
   - `references/slide-conventions.md` - Slide creation and formatting guidelines

3. **If working with an existing course**, read its `labs.md`, `README.md`, and browse the repo structure first
   to understand the current state before making changes.

4. **If creating or updating slides**, also read the `pptx` skill (`/sessions/keen-determined-keller/mnt/.skills/skills/pptx/SKILL.md`)
   since you'll need its techniques for PowerPoint manipulation.

---

## Course Repository Structure

Every course follows this standard layout. When creating a new course, generate all of these components.
When updating, preserve this structure.

```
[course-name]/
├── .devcontainer/
│   └── devcontainer.json        # Dev container config (VS Code + Docker)
├── .github/
│   └── copilot-instructions.md  # AI assistant instructions for students
├── images/                      # Screenshots referenced in labs.md
├── extra/                       # Completed code versions for diff-merge labs
├── scripts/
│   ├── pysetup.sh              # Python environment setup
│   ├── startup_ollama.sh       # Service startup (if needed)
│   └── startOllama.sh          # Service re-attach
├── [topic-dirs]/               # Code organized by topic (e.g., llm/, rag/, neo4j/)
│   └── *.py                    # Lab code files
├── tools/                      # Utility scripts (indexers, search tools)
├── labs.md                     # THE main lab document students follow
├── README.md                   # Setup instructions + prerequisites
├── README-Codespace.md         # Alternative codespace setup (if applicable)
├── requirements.txt            # Python dependencies
├── LICENSE                     # License file
└── .gitignore                  # Standard Python + IDE ignores
```

See `references/repo-structure.md` for full details on each component.

---

## Lab Writing

Labs are the heart of the training. They live in a single `labs.md` file that students follow step-by-step.

### Core Principles

- **Guided discovery**: Students learn by doing, not by reading. Each step should have them execute something
  and observe the result. Explanations come through what they see, not walls of text.
- **Progressive complexity**: Labs build on each other. Early labs cover fundamentals, later labs combine concepts.
- **Skeleton + completed pattern**: For complex labs, provide a skeleton file (with gaps/TODOs) that students
  work with, and a completed version in `extra/` that they can diff-merge or reference.
- **Visual confirmation**: After each significant step, tell students what they should see and reference a
  screenshot image. Students need to know they're on track.

### Format Rules

Read `references/lab-format.md` for the complete format specification. Key points:

- Each lab starts with a bold title and a **Purpose** statement
- Steps are numbered sequentially within each lab
- Code blocks use triple backticks (no language specifier unless needed for syntax highlighting)
- Screenshots are referenced as `![alt text](./images/filename.png?raw=true "tooltip")`
- Use `<br><br>` between steps for spacing
- Each lab ends with `<p align="center">**[END OF LAB]**</p></br></br>`
- Optional/advanced steps are clearly marked
- The document ends with copyright notice

### When Creating New Labs

1. Understand the topic and what concepts need to be taught
2. Identify what code files are needed (create them in appropriate topic directories)
3. For each lab:
   - Write a clear purpose statement
   - Create numbered steps that have students run commands and observe output
   - Generate the code files (both skeleton and completed versions where appropriate)
   - Note where screenshots will be needed (use descriptive placeholder names like `./images/ae-new-1.png`)
   - Include discussion points or "what to notice" guidance
   - Add an estimated completion time in the lab header or purpose statement
   - For steps with known harmless warnings, add a note: "You may see warnings about [X] — these are harmless"
4. Ensure labs build logically on each other
5. **Run the Lab Verification Pass** (see below) to catch structural issues before they reach students

### Lab Verification Pass (CRITICAL)

After writing or updating labs.md, perform this self-verification to catch structural issues. This
methodology comes from live automated testing where missed steps caused cascading failures.

**Step 1: Extract every command from labs.md:**
```bash
grep -n '^\(```\|code \|python \|cd \|pip \|npm \|cat \|cp \|kill \|curl \)' labs.md
```
Also search specifically for `code -d` merge commands — these are the most commonly missed:
```bash
grep -n 'code -d' labs.md
```

**Step 2: Map every command to its lab and step.** Build a structured execution plan:
```
Lab 2:
  Step 1 (line 190): cd agents                                        [CD]
  Step 2 (line 205): code supervisor_budget_agent.py                  [VIEW]
  Step 3 (line 211): code -d ../extra/supervisor_budget_agent.txt ... [MERGE]
  Step 4 (line 229): python supervisor_budget_agent.py                [RUN]
```

**Step 3: Classify each step by type:**
- `[MERGE]` — `code -d` commands that copy complete code over skeleton files
- `[RUN]` — `python`, `node`, `bash`, etc. commands that execute programs
- `[VIEW]` — `code <file>` commands (just viewing/verifying file exists)
- `[CD]` — directory changes
- `[SETUP]` — `pip install`, `npm install`, environment setup
- `[INPUT]` — steps where students type into an interactive prompt
- `[INFO]` — discussion/observation steps, no action needed
- `[OPTIONAL]` — steps marked optional

**Step 4: Cross-reference dependencies:**
- Verify every `[RUN]` step has its prerequisite `[MERGE]` step listed before it. This is the
  single most common lab failure: a student (or tester) runs a skeleton file without first merging
  in the complete code. Never place a `[RUN]` step between a `[VIEW]` step and its `[MERGE]` step.
- Verify every `cd` leaves students in the right directory for subsequent commands
- Verify every file referenced in a command actually exists in the repo
- Verify no step references a file in a directory the student hasn't `cd`'d into yet

**Step 5: Check timing and pacing:**
- Steps involving model downloads, large pip installs, or LLM inference should include approximate
  wait times so students know if something is stuck vs. just slow
- No single lab should be dramatically longer than others without good reason
- Typical timing expectations: Codespace setup (3-5 min), Ollama model warmup (1-3 min),
  local LLM queries (30s-2+ min), server startup (2-5s)

### When Updating Existing Labs

1. Read the current labs.md thoroughly
2. Identify what needs to change (usually API calls, model names, library imports, output formatting)
3. Update both the lab instructions AND the corresponding code files
4. Update both skeleton and completed versions if both exist
5. Flag any screenshots that may need to be retaken (output format changes, UI changes)
6. Update requirements.txt if dependency versions changed

---

## Slide Creation & Updates

Slides accompany the labs and provide the conceptual framework. Use the `pptx` skill for the actual
PowerPoint file manipulation.

### Slide Conventions

- Extract the theme/template from Brent's existing .pptx files in the course repo
- Slides should be conceptual and visual, not walls of text
- Use diagrams and flow charts to explain architectures
- Include speaker notes with talking points and additional context
- Slides should map to lab content (students see the concept, then do the lab)

See `references/slide-conventions.md` for detailed formatting guidelines.

### CRITICAL: Slide Master Matching

**DO NOT use `python-pptx` to create new slides.** The `prs.slide_layouts[N]` API often selects
the wrong slide master, causing new slides to have completely different backgrounds, colors, and
fonts from existing content slides. Decks with multiple slide masters (common when imported from
Google Slides) are particularly prone to this.

**Instead, use the `add_slide.py` duplicate-and-edit workflow:**
1. Identify an existing content slide with the layout/style you want
2. Duplicate it using `python scripts/add_slide.py <deck_unpacked>/ <source_slide> <new_slide>`
3. Edit the duplicated XML directly — replace text while preserving background image refs (typically `rId3`)
4. Insert into `presentation.xml` at the correct position

**python-pptx IS safe for:**
- Adding speaker notes (does not affect slide master assignment)
- Updating text in existing shapes (text replacement within existing runs)
- Year/date replacements across slide masters, layouts, and slides

**python-pptx is NOT safe for:**
- Creating new slides or duplicating slides — always use `add_slide.py`

**Background image awareness:** Many decks use full-slide background PNGs referenced as `rId3`.
When editing slide XML, always preserve the background `<p:pic>` element and place content shapes
on top of it.

### When Updating Slides

1. Read the existing .pptx to understand current content and structure
2. Identify slides that reference outdated versions, APIs, or concepts
3. Update content while preserving the existing theme and layout
4. Update speaker notes to reflect changes
5. Update year/date references (see Content Modernization > "Update dates and versions")
6. Add a speaker note audit trail to each modified slide:
   `[Update - YYYY-MM-DD] Changed: <description>`
7. Duplicate the original version of modified slides as backups at the end of the deck
8. Flag any diagrams that need to be redrawn

---

## Content Modernization

When updating a course for newer library/API versions:

1. **Research current versions**: Check what has changed since the course was last updated.
   Look at changelogs, migration guides, and updated documentation.
2. **Audit dependencies**: Review `requirements.txt` and identify which packages need version bumps.
   Check for breaking changes in each updated package.
3. **Topic coverage analysis (gap identification)**: Don't just update what's there — identify
   what's missing. Catalog all topics the course covers, research the current landscape for the
   course's domain, and identify gaps at three priority levels:
   - **Critical**: Core concepts expected in any training on this topic that are completely missing
     (e.g., LoRA/QLoRA missing from fine-tuning, prompt engineering missing from an LLM course)
   - **Important**: Significant topics that would meaningfully strengthen the course
   - **Nice-to-have**: Emerging or specialized topics that add value but aren't essential
   For each gap, suggest where it fits in the course flow. Also flag topics that have become
   obsolete or significantly less relevant since the last update.
4. **Update code files**: Modify Python files to use current APIs. Common changes include:
   - Import path changes (e.g., LangChain reorganizations)
   - Deprecated function replacements
   - New parameter names or signatures
   - Model name updates (e.g., new Ollama model tags)
5. **Update labs.md**: Adjust any instructions that reference changed APIs, outputs, or behaviors.
6. **Update slides**: Refresh any slides that show code snippets, architecture diagrams, or version numbers.
7. **Update dates and versions** across all materials:
   - Title slide version date and bump minor version number (e.g., v2.2 → v2.3)
   - Copyright year in labs.md footer, README.md, and slide footers
   - labs.md revision header
   - **Important**: Only update copyright/presentation years, NOT historical/factual years
     (e.g., "Transformers were introduced in 2017" should NOT be changed)
   - Common patterns to update: `© 2025` → `© 2026`, version dates, "Workshop - Month Year"
   - Common patterns to leave alone: "GPT-3 was released in June 2020", "As of 2023, the MTEB..."
8. **Update devcontainer**: Adjust base images, features, or post-create commands if needed.
9. **Content flow verification**: After all updates, verify the learning progression:
   - Does content move from simple to complex within each section and across the course?
   - Are concepts introduced before they're used or referenced? (prerequisite ordering)
   - Are there logical bridges between sections, or abrupt jumps?
   - Are any sections now too dense or too sparse after changes?
   - Do labs still appear after the slides that teach those concepts? (lab placement)
   - Has any content become redundant after the updates?
10. **Run the Lab Verification Pass**: Use the structured verification process (see "Lab
    Verification Pass" above) — don't just mentally walk through. Extract every command, build
    the execution plan, and cross-reference merge-before-run dependencies.

### Update Audit Trail

When modifying existing materials, maintain a record of what changed so the instructor can
review and revert if needed:

- **labs.md**: Update the `## Revision [X.Y] - [MM/DD/YY]` header line with the new version and date
- **Code files**: Add a comment at the top noting what changed:
  `# Updated [DATE]: Migrated from LangChain 0.1.x to 0.3.x API`
- **Slides**: Add a speaker note to each modified slide:
  `[Update - YYYY-MM-DD] Changed: <short description of what was fixed>`
- **Slides backup**: Before modifying any existing slide, duplicate the original and append it at the
  end of the deck as a backup with a speaker note:
  `[BACKUP - Update YYYY-MM-DD] Original version of slide [N]: [slide title]`
- **Consider a CHANGELOG.md** in the repo root for significant updates, documenting what changed,
  why, and which files were affected

---

## QA / Review

When reviewing existing materials, use the checklist in `references/qa-checklist.md`. Key areas:

- **Consistency**: Do labs.md instructions match the actual code files?
- **Completeness**: Are all referenced files present? Are all code paths covered?
- **Currency**: Are library versions current? Are APIs still valid?
- **Correctness**: Do step numbers flow correctly? Are there duplicate or missing steps?
- **Screenshots**: Are all referenced images present in the images/ directory?
- **Links**: Do internal links (to code files, external sites) work?
- **Skeleton/complete sync**: Do skeleton files and their completed counterparts in extra/ match up?
- **Environment**: Does requirements.txt include all needed packages? Does devcontainer config work?

---

## Supporting File Generation

### devcontainer.json

Standard pattern for AI/ML training courses:

```json
{
    "image": "mcr.microsoft.com/devcontainers/base:bookworm",
    "remoteEnv": {
        "OLLAMA_MODEL": "llama3.2:3b"
    },
    "hostRequirements": {
        "cpus": 4,
        "memory": "16gb",
        "storage": "32gb"
    },
    "features": {
        "ghcr.io/devcontainers/features/docker-from-docker:1": {},
        "ghcr.io/devcontainers/features/github-cli:1": {},
        "ghcr.io/devcontainers/features/python:1": {}
    },
    "customizations": {
        "vscode": {
            "settings": {
                "python.terminal.activateEnvInCurrentTerminal": true,
                "python.defaultInterpreterPath": ".venv/bin/python",
                "github.copilot.enable": { "*": false },
                "github.copilot.enableAutoComplete": false,
                "editor.inlineSuggest.enabled": false,
                "workbench.startupEditor": "readme",
                "workbench.editorAssociations": { "*.md": "vscode.markdown.preview.editor" },
                "terminal.integrated.defaultProfile.linux": "bash",
                "terminal.integrated.profiles.linux": {
                    "bash": { "path": "bash", "args": ["-l"] }
                }
            },
            "extensions": ["mathematic.vscode-pdf", "vstirbu.vscode-mermaid-preview"]
        }
    },
    "postCreateCommand": "bash -i scripts/pysetup.sh py_env && bash -i scripts/startup_ollama.sh",
    "postAttachCommand": "bash scripts/startOllama.sh"
}
```

Adjust `remoteEnv`, `features`, `postCreateCommand`, and extensions based on course needs.
Copilot is intentionally disabled in training environments so students learn hands-on.

### pysetup.sh

Standard Python environment setup script:

```bash
#!/usr/bin/env bash
PYTHON_ENV=$1
python3 -m venv ./$PYTHON_ENV \
    && export PATH=./$PYTHON_ENV/bin:$PATH \
    && grep -qxF "source $(pwd)/$PYTHON_ENV/bin/activate" ~/.bashrc \
    || echo "source $(pwd)/$PYTHON_ENV/bin/activate" >> ~/.bashrc
source ./$PYTHON_ENV/bin/activate
if [ -f "./requirements.txt" ]; then
    pip3 install -r "./requirements.txt"
elif [ -f "./requirements/requirements.txt" ]; then
    pip3 install -r "./requirements/requirements.txt"
fi
```

### requirements.txt

Group dependencies by purpose with comments explaining each group.
Pin minimum versions (>=) rather than exact versions for flexibility.

### Skeleton Code Pattern

When creating skeleton + completed code pairs:
- The skeleton file goes in the topic directory (e.g., `rag/lab10.py`)
- The completed version goes in `extra/` (e.g., `extra/lab10_eval_complete.txt`)
  - Use `.txt` extension for completed versions so they don't execute accidentally
- Labs instruct students to use `code -d` to open a diff view between the two files
- Students merge code segments from the completed version into the skeleton

**Skeleton File Rules (from live testing lessons):**

These rules prevent the most common lab failures found during automated QA testing:

1. **Skeleton files MUST be syntactically valid Python** — they should parse without syntax errors
   even if they can't run successfully. No bare indentation errors, unclosed brackets, or missing
   colons. Students who accidentally try to run the skeleton before merging should get a clean
   runtime error (like a `NotImplementedError` or missing function), not a confusing `SyntaxError`.

2. **Use clear `# TODO` or placeholder comments** where code will be merged in. This makes the
   diff view obvious and helps students understand what needs to change.

3. **Never place a "run this file" step between a "view" step and its merge step.** The correct
   order is always: view skeleton → diff/merge → run. If students are asked to run a file, the
   merge MUST have happened first. This is the single most common mistake in lab authoring.

4. **The diff between skeleton and complete should be clean** — only the intentional gaps should
   differ. No unrelated whitespace changes, import reordering, or comment differences. A clean
   diff makes the merge obvious for students.

5. **Explicitly tell students the file won't run yet** if a skeleton is shown before its merge step.
   A simple note like "Note: this file is incomplete — we'll merge in the working code in the next
   step" prevents confusion.

6. **Test the diff** by running `diff skeleton.py ../extra/complete.txt` to verify it shows only
   the intended changes. Unexpected differences confuse students during the merge process.

### Multi-Process Lab Patterns

Some labs require multiple concurrent processes (e.g., a server in one terminal and a client in
another, or an auth server + application server + client). These labs need careful structuring
to avoid confusion. Guidance from live testing:

**Terminal management:**
- When a lab needs a server running, explicitly tell students to open a new terminal and how
  (click the `+` button in the VS Code terminal panel, or use `Ctrl+Shift+\``)
- Label which terminal each command runs in: "In Terminal 1 (server):", "In Terminal 2 (client):"
- After creating a new terminal, remind students to `cd` to the correct directory — new terminals
  open at the repo root, not where they left off

**Server lifecycle:**
- Include a startup confirmation step: "You should see output like `Running on http://127.0.0.1:5000`"
- Include an explicit "stop the server" step at the end (with the exact keystroke: `Ctrl+C`)
- If Lab N starts a server that Lab N+1 doesn't need, include a cleanup step between labs
- Document the port number so students can troubleshoot "address already in use" errors

**Background execution alternative:**
For simpler cases where students don't need to see server output, consider using background
execution with `&`:
```bash
python auth_server.py &
python secure_server.py &
sleep 2  # wait for servers to start
python client.py
```

**Cleanup between labs:**
```bash
kill %1 %2                    # kills background jobs by job number
kill $(lsof -t -i:5000)      # kills process on specific port
```

**Interactive programs:**
- When a Python script runs an interactive loop (`input()` prompt), tell students to use `Ctrl+C`
  to exit, not "quit" or "exit" — many programs will try to process those as valid input
- For LLM-powered interactive programs, note that responses may take 30s-2+ minutes on a
  4-core Codespace. Add: "This may take a minute or two — be patient while the model generates
  its response."

### copilot-instructions.md

Include a `.github/copilot-instructions.md` with the "Explain-this-app" template that helps students
understand code files through a structured explanation format (what it does, high-level flow,
key building blocks, data flow, safe experiments, debug checklist).

### README.md

The README should include:
- Course title and day/session info with revision number
- Setup instructions (Dev Containers button, prerequisites)
- System requirements
- Alternative setup approaches
- Troubleshooting section
- License and attribution

---

## Instructor Preparation: Anticipated Q&A

When creating a new course or performing a major update, generate an `anticipated-qa.md` document
to help the instructor prepare for likely student questions. This is especially valuable for
AI/ML courses where the landscape changes rapidly.

### How to generate

1. Review the full course content — labs, slides, and code
2. For each major section, identify 3-5 questions a learner might ask:
   - **Clarification questions**: "What's the difference between X and Y?"
   - **Depth questions**: "How does this work under the hood?"
   - **Practical questions**: "When would I use X vs Y in production?"
   - **Troubleshooting questions**: "What if I get error X when running the lab?"
   - **Current landscape questions**: "What's the latest on X?"
   - **Scope/boundary questions**: "Is X covered in Day 2?" or "How does this relate to Y?"
3. Write clear, concise answers (2-5 sentences) tied back to course content where possible
4. Include troubleshooting Q&A specific to the lab environment (Codespace issues, model loading,
   package errors, common warnings)

### Output format

```markdown
# Anticipated Q&A: [Course Title]
**Generated**: [date]
**Course version**: [version]

## Section: [Section Name] (Labs X-Y)

**Q: [Question]?**
A: [Answer]

## General / Cross-Cutting Questions

**Q: [Question]?**
A: [Answer]
```

Aim for 25-40 questions total. Weight toward conceptually dense sections and newer/evolving topics.
Include timing estimates per lab and suggested break points as an appendix.

---

## Copyright & Attribution

All materials should include:
- License file in repo root
- Copyright notice at the end of labs.md:
  ```
  <p align="center">
  <b>For educational use only by the attendees of our workshops.</b>
  </p>
  <p align="center">
  <b>(c) [YEAR] Tech Skills Transformations and Brent C. Laster. All rights reserved.</b>
  </p>
  ```
- License section in README.md referencing TechUpSkills / Brent Laster
