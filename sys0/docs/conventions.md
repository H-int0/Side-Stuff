# Conventions

*This document outlines the rules and standards followed in this repo covering:*

- commit messages
- branch naming
- git workflow
- versioning

*Each convention is explained alongside the reasoning behind it, so that the rules are easy to follow along and understand rather than just memorize.*

*Also, don't think that these are super rigid rules, they're fluid and exceptions may exist.*

---

## 1. Commit Messages

### Format

Every commit message follows this structure:

```cmd
type: short description in lowercase
```

Examples:

```cmd
feat: add codecamp p7 tribute page
fix: correct broken link in odin p11 index
docs: update README with codecamp p32 entry
chore: add placeholder to exercism p18 folder
```

---

### Types

| Type | Used for |
| --- | --- |
| `feat` | Adding something new like a new page, project, section, or file. |
| `fix` | Correcting a bug, broken link, typo, or anything that was wrong. |
| `docs` | Changes to documentation only. |
| `refactor` | Restructuring or reorganizing existing code without changing what it does. |
| `chore` | Maintenance work config changes, folder setup, `.gitignore` updates, perhaps adding files, etc. |
| `style` | Formatting, whitespace, or styling changes that do not affect logic or content. |

### Why tho?

- Well, without a consistent format, a git log quickly becomes a mess of vague messages like *"update stuff"* or *"fix"* that tell you nothing.

- The `type:` prefix forces you to think about **what kind of change** you're making before you commit, and it makes scanning the log much faster, especially when looking for when a specific bug that was introduced or when a project was added.

- The lowercase convention is purely for visual consistency across all messages.
  
**Overall** it just makes filtering out commits much convenient and easier.

---

## 2. Branch Naming

### Permanent Branches

| Branch | Purpose |
| --- | --- |
| `main` | Production-ready code only. Always stable. Never committed to directly. |
| `dev` | Integration branch. All work flows through here before going to `main`. |

---

### Feature / Lab Branches

Named after the platform and project number:

```cmd
platform-pN
```

Examples:

```cmd
odin-p37
codecamp-p24
codecademy-p19
exercism-p74
```

- Where `platform` is a short identifier for the learning platform.
- And, `pN` is the project number with `p` as a prefix indicating it is a project.

---

### Release Branches

```cmd
release-vX.Y.0
```

Examples:

```cmd
release-v1.5.0
release-v2.0.0
```

---

### Hotfix Branches

```cmd
hotfix-vX.Y.Z
```

Examples:

```cmd
hotfix-v1.5.1
hotfix-v1.5.2
```

### Why-tho?

- Because, consistent branch names mean you can tell exactly what a branch contains and what role it plays just from its name no need to check the log or ask.

- The `platform-pN` pattern mirrors the folder structure of the repo (`lab-odin/p1`), making the connection between branches and files obvious.

- Release and hotfix branches carry version numbers in their name so the version being prepared is always visible without having to look up tags.

---

## 3. Git Workflow

### Overview

```cmd
dev  → feature branch  →  dev  →  release branch  →  main
                                                 ↘  dev

main  →  hotfix branch  →  main
                       ↘  dev
```

---

### Feature/Lab Branches

- Branch off: `dev`.
- Merge into: `dev` ONLY.
- Testing: surface-level the focus here is on building the project, not exhaustive verification.
- **Merge command: ALWAYS use `--no-ff`**

Feature branches are where the actual project work happens. Each new learning project gets its own branch so that work-in-progress code is fully isolated from `dev` and never risks polluting the integration branch mid-build.

---

### Direct Commits to `dev`

Changes to `sys0/` (repo-level docs, core pages) and `sys1/container/` (shared assets, error pages) can be committed directly to `dev` without a separate feature branch. These are not project-specific additions, they are repo-level maintenance changes, so the overhead of a separate branch is unnecessary.

---

### Release-Branches

- Branch off: `dev` (after the feature branch has been merged into `dev`)
- Merge into: both `main` and `dev`
- Merge command: always `--no-ff`
- This is where:
  - Final, thorough testing and error correction takes place
  - `index.html` (root workspace page) is updated to include the new project
  - `README.md` is updated with the new project entry
  - Any other relevant documentation is updated

The release branch acts as a final gate before anything reaches `main`. Separating this from the feature branch means that the build work and the verification + documentation work are cleanly distinct. Nothing lands on `main` until it has been through this stage. Merging back into `dev` as well ensures that `dev` stays up to date with all corrections made during the release process.

---

### Hotfix-Branches

- Branch off: `main`
- Merge into: both `main` and `dev`
- Merge command: always `--no-ff`
- Testing standards: same as release, super extensive and thorough before merging into `main`
- Used for: bugs that slipped into `main`, things forgotten during the release process, and other minor post-release corrections

Hotfixes branches off of `main` rather than `dev` because the issue being fixed exists specifically in `main` and not in whatever state `dev` is currently in. Merging back into both ensures the fix is reflected everywhere and `dev` does not fall behind `main`.

---

### Testing Standards

| Branch type | Testing standard |
| --- | --- |
| Feature / lab | Surface-level: is the project building correctly? |
| `dev` | Second priority: integration level |
| Release / hotfix | Highest priority: thorough, extensive, production-ready |

Any branch that will eventually be merged into `main` is held to the highest testing standard. `main` must always be clean and stable.

---

### The `--no-ff` Rule

Every merge in this repo uses the `--no-ff` flag (no fast-forward). This forces git to always create a merge commit even when a fast-forward would be possible.

Without `--no-ff`, git sometimes merges by simply moving the branch pointer forward, which means the branch history disappears into a flat line. With `--no-ff`, every merge is recorded as an explicit event in the log and you can always see that a branch existed, what commits it contained, and exactly when it was merged. This makes the history honest and readable, and makes it much easier to revert an entire branch if something goes wrong.

---

## 4. Versioning

This repo follows Semantic Versioning with the format:

```cmd
vMAJOR.MINOR.PATCH
```

---

### When to bump each number

| Version part | Bump when... |
| --- | --- |
| `PATCH` | A hotfix is merged into `main` |
| `MINOR` | A completed project is merged into `main` through a release branch |
| `MAJOR` | A significant repo-wide overhaul is completed e.g. a comprehensive redesign, a new technology applied across the whole workspace |

---

### Tags

A version tag is applied to `main` on every release or hotfix merge. Tags are never applied to `dev` or feature branches.

---

### What counts as a major version?

A major version bump marks a repo-wide significant overhaul a change in scope or capability that touches the whole project rather than adding one new thing to it. As an example, going from plain HTML across the board to a version where CSS and styling have been applied throughout the entire repo would be a major change. This is a deliberate threshold, not a routine bump minor and patch versions handle all regular progress.

---

### But why tho?

Having a versioning system even on a personal learning repo builds the habit of thinking about changes in terms of their scope and impact. It also makes the git tag history meaningful: looking at tags on `main` tells the story of how the repo has grown over time.

Tying version bumps to specific events (project completion, hotfix, overhaul) removes ambiguity about when to bump and keeps the version number honest about what it represents.

---

~*H_int*
