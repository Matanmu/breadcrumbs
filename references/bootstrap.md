# Bootstrap Instructions

Loaded only when the user approves onboarding for a project with no
breadcrumbs files. Follow these steps exactly.

---

## Step 1: Scan the project

Read these sources in order:

1. `README.md` (if exists) — purpose, audience, high-level description
2. `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod` / `composer.json`
   (whichever exists) — name, dependencies, scripts
3. Top-level file tree — `ls` one level deep, note key directories
4. `git log --oneline -10` — recent activity, current focus area

Do not read deep into source files yet. Surface-level scan only.

---

## Step 2: Apply aggressive minimalism

> **Aggressive minimalism.** If a fact is obvious from `package.json`,
> `README.md`, or the file tree (e.g., "this is a Node.js project",
> "uses Express"), do NOT write it in CLAUDE.md. Write only what a developer
> wouldn't learn in 30 seconds of looking at the project.
> Target: 15-25 lines total. If a section has nothing worth saying,
> omit the section entirely.

Examples of what NOT to write in CLAUDE.md:
- "This is a Node.js project" (obvious from package.json)
- "Uses React" (obvious from dependencies)
- "Has a src/ directory" (obvious from file tree)
- "Written in TypeScript" (obvious from file extensions)

Examples of what IS worth writing:
- "Auth tokens stored in Redis, not the DB — intentional for revocation speed"
- "API responses always wrapped in `{ data, error, meta }` envelope"
- "Migrations run automatically on deploy via `postinstall` script"
- Non-obvious architectural constraints or team conventions

---

## Step 3: Ask gap-filling questions

After scanning, ask the user 1-2 targeted questions to fill gaps that scanning
couldn't answer. Keep it tight — one message, bullet list:

- What is this project's current focus or what are you actively working on?
- (Only if genuinely unclear from scan) What's the project's main purpose?

Do not ask more than 2 questions. Do not ask things you can answer from scan.

---

## Step 4: Draft and show

Draft both files and show them to the user before writing anything.

Present as:

```
Here's what I'd write:

**CLAUDE.md** (XX lines):
---
[content]
---

**.claude/SESSION.md**:
---
[content]
---

Looks good, or want changes?
```

For SESSION.md, use the current date/time for `Updated`. Set Focus based on
the user's answer to the gap-filling question. Set `Stopped at` and `Next`
based on recent git log if possible, otherwise leave a placeholder.

---

## Step 5: Write on approval

When the user approves (any affirmative: "yes", "looks good", "go", "write
it", etc.):

1. Write `CLAUDE.md` at project root.
2. Create `.claude/` directory if it doesn't exist.
3. Write `.claude/SESSION.md`.
4. Say one line: "Breadcrumbs set up. SESSION.md is gitignored — add
   `.claude/SESSION.md` to your `.gitignore` if you haven't already."

Do not write anything else. Do not explain the files. Trust the user to read.

---

## CLAUDE.md template (fill in, delete empty sections)

```markdown
# <Project name>
<One sentence: what it does, for whom>

## Stack
- <name version>
- <name version>

## Architecture
- <Non-obvious pattern>
- <Non-obvious pattern>

## Conventions
- <Non-default convention>
- <Non-default convention>
```

## SESSION.md template

See `references/session-template.md`.
