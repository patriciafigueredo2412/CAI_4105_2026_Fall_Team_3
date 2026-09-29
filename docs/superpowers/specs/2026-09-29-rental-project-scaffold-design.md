# Rental Prediction Team Project Scaffold Design

## Purpose

Prepare the existing repository for a student team to collaborate on a rental price prediction assignment. This setup provides a clean Python project structure, basic environment instructions, and a GitHub branch and pull request workflow. It does not complete any part of the assignment.

## Current Context

The local repository has no tracked project files or commits. It has an `origin` remote configured, but no `origin/main` branch is available locally. The scaffold will be created locally; adding collaborators or publishing commits to GitHub is outside this setup.

## Design

- Add a Python `.gitignore` for virtual environments, caches, generated files, and notebook checkpoints/outputs that should not be versioned.
- Add a starter `README.md` with repository purpose placeholders, environment setup instructions, a concise branch/PR workflow, and guidance for keeping notebook diffs manageable.
- Add a minimal `requirements.txt` containing the baseline data-science and notebook packages: pandas, numpy, scikit-learn, and jupyter.
- Add `data/`, `notebooks/`, and `src/` directories. Give each a short README explaining what belongs there; do not add data, notebooks, or implementation code.
- Keep generated deliverables such as `predictions.csv` out of the initial scaffold.

## Constraints

- Do not perform analysis, train models, write prediction outputs, or draft assignment conclusions.
- Do not include datasets or commit secrets.
- Do not push, create GitHub collaborators, or change remote repository settings.
- Keep files plain and editable so teammates can adapt the scaffold to course-specific requirements.

## Acceptance Checks

- The expected scaffold files and directories exist.
- README setup commands refer to the declared requirements and use a Python virtual environment.
- The ignore rules cover local environments and common notebook-generated clutter.
- The scaffold contains no assignment solution artifacts.
