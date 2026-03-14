# Course Repository Structure

This document details the standard repository layout for TechUpSkills training courses.

## Directory Layout

```
[course-name]/
├── .devcontainer/
│   └── devcontainer.json
├── .github/
│   └── copilot-instructions.md
├── .gitignore
├── LICENSE
├── README.md
├── README-Codespace.md          (optional, for Codespace-specific setup)
├── LOCAL_SETUP.md               (optional, for local Docker setup details)
├── requirements.txt
├── labs.md
├── [course-slides].pptx         (named descriptively, e.g., ae-2.pptx)
├── images/
│   └── *.png                    (screenshots referenced in labs.md)
├── extra/
│   └── *_complete.txt           (completed code versions for diff-merge labs)
├── scripts/
│   ├── pysetup.sh               (Python venv setup)
│   ├── startup_ollama.sh        (service startup, if applicable)
│   ├── startOllama.sh           (service re-attach, if applicable)
│   ├── shutdown_ollama.sh       (service shutdown, if applicable)
│   └── warmup.py                (model warmup, if applicable)
├── tools/
│   └── *.py                     (utility scripts: indexers, search tools, etc.)
├── [topic-dir-1]/
│   └── *.py                     (lab code files for this topic)
├── [topic-dir-2]/
│   └── *.py
└── [infrastructure-dir]/        (optional: Docker, database configs)
    ├── Dockerfile_*
    ├── *.sh
    └── data*/
```

## Component Details

### .devcontainer/devcontainer.json

Controls the VS Code Dev Container environment. Key sections:
- **image**: Base container image (typically `mcr.microsoft.com/devcontainers/base:bookworm`)
- **remoteEnv**: Environment variables available in the container
- **hostRequirements**: Minimum CPU, memory, storage for the host machine
- **features**: Dev container features to install (Docker, GitHub CLI, Python, Node if needed)
- **customizations.vscode.settings**: VS Code settings, importantly:
  - Copilot is disabled (students learn hands-on)
  - Markdown files open in preview by default
  - Terminal uses bash with login shell
- **customizations.vscode.extensions**: VS Code extensions to install
- **postCreateCommand**: Runs once when container is created (env setup, dependency install)
- **postAttachCommand**: Runs each time VS Code attaches to container (restart services)

### .github/copilot-instructions.md

Provides an "Explain-this-app" template for AI assistants to help students understand code.
Even though Copilot is disabled by default, students may enable it or use other AI tools.
The template structures explanations into: what the app does, high-level flow, key building blocks,
data flow, reading map, common gotchas, safe experiments, and debug checklist.

### images/

All screenshots referenced in labs.md. Naming convention:
- `[course-prefix][number].png` (e.g., `ae4.png`, `arag24.png`)
- `[course-prefix]-[topic]-[number].png` (e.g., `aia-1-3.png`)
- Screenshots capture terminal output, VS Code views, web UIs, and application output

### extra/

Completed code files for the diff-merge lab pattern:
- Named to match their skeleton counterpart: `lab10_eval_complete.txt`
- Use `.txt` extension to prevent accidental execution
- Also contains other supplementary files: change logs, full outputs, reference data

### scripts/

Shell scripts for environment management:
- `pysetup.sh` - Creates Python venv, activates it, installs requirements
- Service startup/shutdown scripts as needed (Ollama, databases, etc.)
- `warmup.py` - Optional script to pre-load models or warm up services

### tools/

Utility Python scripts that support the labs but aren't the focus of a specific lab:
- Indexers (e.g., `index_pdfs.py`, `index_code.py`)
- Search utilities (e.g., `search.py`)
- Data loaders and helpers

### Topic Directories

Code organized by training topic. Each directory contains the Python files students work with
in the labs. Examples:
- `llm/` - Neural networks, tokenization, embeddings, transformers
- `rag/` - RAG implementations, evaluation, hybrid search
- `neo4j/` - Graph database setup and configs
- `ft/` - Fine-tuning examples

### Infrastructure Directories

For courses needing external services (databases, containers):
- Dockerfiles for service containers
- Setup scripts
- Data directories with schema files, seed data, configs

### README.md

Structure:
1. Course title, day/session, topic
2. Setup instructions with Dev Container "Open" button
3. What to expect during setup (timing, what it looks like)
4. How to open labs.md
5. Prerequisites section (system requirements, required software with install links)
6. Alternative setup approach (manual clone + reopen in container)
7. Troubleshooting section
8. License and attribution

### requirements.txt

Format:
```
# [Section description]
package-name>=minimum.version

# [Next section description]
another-package>=minimum.version
```

Group packages by purpose with comment headers. Use `>=` for minimum version pinning.
Include notes for non-pip dependencies (e.g., Ollama install commands).

### labs.md

The primary student-facing document. See `references/lab-format.md` for complete specification.

### .gitignore

Standard Python gitignore plus:
- IDE files (.vscode/, .idea/)
- Python artifacts (__pycache__/, *.pyc, .venv/)
- Database files (chroma_db/, *.db)
- OS files (.DS_Store)
- Environment files (.env)
