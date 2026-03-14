# training-lab-reviewer

A Claude skill for automated QA testing of training labs by running them end-to-end inside GitHub Codespaces through Chrome.

## What It Does

This skill turns Claude into an automated lab tester. Given a GitHub repository URL, it will:

1. **Open the repo in Chrome** and launch a new GitHub Codespace
2. **Wait for full setup** — container build, post-create scripts, model downloads, service startup
3. **Read and parse labs.md** — extracts every command, builds a complete execution plan, and maps merge-before-run dependencies
4. **Execute every lab step** — runs terminal commands, handles diff-merge patterns (copies complete code over skeletons), manages multiple terminals for server/client labs, and interacts with interactive programs
5. **Log results in real time** — captures pass/fail status, error output, and screenshots for each step
6. **Generate a detailed test report** — markdown report with lab-by-lab results, error details, suggested fixes, and an overall assessment of whether the course is ready for students

## When It Triggers

The skill activates on phrases like "test this lab", "run through the labs", "QA the codespace labs", "validate training materials", "test the lab exercises", or when you provide a GitHub URL and want the labs tested end-to-end.

## Requirements

- **Chrome browser access** — the skill drives a Chrome tab via the Claude in Chrome MCP
- **GitHub account** — with permissions to create Codespaces on the target repository
- A repository containing a `labs.md` file and `.devcontainer/devcontainer.json`

## Key Features

- **Execution plan building** — reads the entire labs.md upfront and builds an ordered execution plan before running anything, preventing missed steps
- **Merge-before-run verification** — cross-references every `python <file>` command against `code -d` merge steps to ensure skeleton files get completed code before execution
- **Error classification** — distinguishes hard errors, soft errors, environmental issues, tester mistakes, and expected skeleton states
- **Background server management** — handles labs requiring multiple servers running simultaneously
- **Connection recovery** — handles Chrome MCP disconnects and Codespace connection drops gracefully
- **Benign warning filtering** — ignores ONNX runtime warnings, pip version notices, and other harmless output

## Report Output

The skill produces a markdown report with:

- Summary statistics (passed/failed/warnings/skipped)
- Overall assessment of course readiness
- Per-lab results table with step-by-step status
- Detailed error descriptions with commands, expected vs. actual output, likely causes, and suggested fixes
- Recommendations for systemic improvements

## Installation

Install the `.skill` file through Claude's Settings → Skills, or copy the `training-lab-reviewer/` folder into your skills directory.
