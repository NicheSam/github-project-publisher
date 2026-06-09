---
name: github-project-publisher
description: Use when the user wants to prepare one or more local project folders for GitHub by copying them into a PushGithub staging area, cleaning upload scope, checking for unsafe files, writing public-facing bilingual READMEs, initializing Git repositories, creating GitHub repos with GitHub CLI, pushing, and reporting final links.
metadata:
  short-description: Prepare and publish local projects to GitHub
---

# GitHub Project Publisher

## Purpose

Prepare local project folders for GitHub without disturbing the original working files, then publish the cleaned projects with `git` and GitHub CLI.

Use this workflow when the user asks to move/copy local projects into a GitHub upload area, clean them, write README files, initialize Git, create GitHub repositories, push, or report published repo links.

## Core Rules

- Do not move or modify the original project unless the user explicitly asks.
- Work from clean copies under a staging folder, usually `PushGithub`.
- Treat public GitHub upload as a security-sensitive operation.
- Never commit secrets, environment files, runtime data, client files, generated outputs, dependency folders, or compiled artifacts unless the user explicitly confirms.
- Ask for missing repo visibility and repo names before creating GitHub repos.
- If GitHub CLI auth is invalid, ask the user to run `gh auth login -h github.com`.
- If sandbox and Windows user Git ownership differ, use narrowly scoped `git safe.directory` entries only for the exact staged project paths.

## Workflow

### 1. Discover And Confirm Scope

1. List candidate project folders.
2. Identify existing Git repos and GitHub remotes.
3. Classify folders:
   - already GitHub-connected
   - suitable source project
   - old version / archive
   - build output / temp folder
   - data or asset library
   - unsafe or unclear
4. Report candidates and risks before copying or publishing.
5. Confirm:
   - which projects to publish
   - repo visibility: public or private
   - repo names, especially when folder names contain spaces or version suffixes

### 2. Create Staged Folder Structure

Create project folders under `PushGithub`.

Preferred structure for versioned projects:

```text
PushGithub/
  ProjectName/
    README.md
    .gitignore
    v0.1/
      README.md
      source files...
```

Use the project's stable name for the repository folder. Put version-specific code in a version subfolder when the user wants future version maintenance.

Examples:

```text
PushGithub/QtoWirePlugin/v0.5
PushGithub/SC REVIT/v0.2-dev
PushGithub/mep_handover_mvp/v0.2
```

### 3. Clean-Copy Project Files

Copy, do not move, from the original project to the staged folder.

Common exclusions:

```text
.git/
node_modules/
.venv/
venv/
__pycache__/
.pytest_cache/
.streamlit/
dist/
build/
bin/
obj/
release/
runtime/
project_outputs/
outputs/
logs/
log/
notion/
*.dll
*.pdb
*.pyc
*.pyo
*.log
*.tmp
*.cache
*.zip
```

Adjust exclusions by project type:

- Python: exclude virtualenvs, caches, outputs.
- Node: exclude `node_modules`, build output, local env files.
- .NET / CAD / Revit: exclude `bin`, `obj`, DLLs, PDBs, release bundles unless release artifacts are intentionally published.
- Desktop apps: exclude packaged installers unless the repo is specifically a release repo.
- Data libraries: do not upload large private libraries directly; consider Git LFS or separate storage.

### 4. Safety Checks

Before committing:

1. Confirm staged file count and size.
2. Check suspicious file names:
   - `.env*`
   - `*secret*`
   - `*credential*`
   - `*token*`
   - `*.pem`
   - `*.p12`
   - `*.pfx`
3. Scan for common token patterns:
   - OpenAI keys
   - GitHub tokens
   - Google API keys
   - Slack tokens
   - private key headers
4. Check for unwanted folders and compiled artifacts.
5. Report "not found" as basic screening only, not a full security audit.

### 5. README Requirements

Write README files for people who have never seen the user's local project.

Do not write README files as internal staging notes. Avoid:

- local paths like `<local-user-project-path>`
- "PushGithub"
- "upload staging folder"
- "my local copy"
- instructions written only for the owner

For public-facing READMEs, include:

- project name
- what problem it solves
- who should use it
- current version and status
- main capabilities
- repository structure
- requirements
- install/build steps
- run/use instructions
- examples or command tables when useful
- output files or data formats when relevant
- limitations and safety notes

When the user asks for bilingual README:

- Write Chinese and English sections, or one primary language plus an "English" / "中文" section.
- Keep technical names unchanged: `GitHub`, `AutoCAD`, `Revit API`, `Streamlit`, `XData`, `DLL`, `CSV`.
- Ensure both languages are useful to a non-local reader; do not make the second language a shallow summary unless the user asks for concise translation.

### 6. Initialize And Commit

For each staged project:

```powershell
git init -b main
git add .
git status -sb
git commit -m "Initial <project> source upload"
```

Use explicit staged project paths. Do not run `git add` in the wrong folder.

If the working tree is mixed or there are unexpected files, stop and report.

### 7. GitHub CLI And Push

Check:

```powershell
gh --version
gh auth status
```

If auth fails, ask the user to run:

```powershell
gh auth login -h github.com
```

If `gh` works only outside the sandbox, request escalation for GitHub CLI and git network operations.

Create GitHub repo:

```powershell
gh repo create <repo-name> --public --description "<description>"
```

or:

```powershell
gh repo create <repo-name> --private --description "<description>"
```

Add remote and push:

```powershell
git remote add origin https://github.com/<owner>/<repo-name>.git
git push -u origin main
```

If `git` reports `dubious ownership`, add only the exact staged project path:

```powershell
git config --global --add safe.directory "<exact/project/path>"
```

Never add a wildcard safe directory.

### 8. Final Report

Report:

- project names
- GitHub URLs
- visibility
- default branch
- local staged paths
- commit hashes
- whether working trees are clean
- projects intentionally skipped
- any remaining risks or manual actions

Keep the final answer concise, but include enough detail for the user to verify the result.
