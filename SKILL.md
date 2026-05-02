---
name: breadcrumbs
description: |
    Maintains session continuity for Claude Code. Activate when the user says
    "continue", "where were we", "let's continue", "what were we working on",
    "catch me up", "resume", "checkpoint", "save state", "update session",
    "before clear", "done for today", "let's stop here", "wrapping up",
    "I'm out", "tomorrow", "let's continue tomorrow", "summarize what we did",
    or any equivalent in any language (e.g. Hebrew: "המשך", "איפה היינו",
    "שמור מצב", "סיימנו להיום"). Also activate at session start when the
    user's first message implies picking up prior work.
---

# Breadcrumbs Skill

Solves session continuity. After `/clear` or a new session, read SESSION.md
and give a 3-line summary so the user doesn't re-explain context.

---

## Starting a session

When the skill activates at session start (user says "continue", "where were
we", or similar), do this immediately:

1. Check for `.claude/SESSION.md` in `cwd`.
2. If found, check the `Updated` date. If older than 30 days, add stale warning
   (see edge cases).
3. Read SESSION.md and output **exactly** this format — no preamble, no
   epilogue:

```
📍 Last: <one line from Focus>
⏸️  Stopped at: <one line from Stopped at>
➡️  Next: <one line — first item from Next>
```

Match the user's language in the content. Emoji anchors stay as-is.
**Never more than 3 lines.** Never say "Here's your summary:" or anything
before or after the 3 lines.

4. If SESSION.md does not exist:
   - If CLAUDE.md also does not exist and the project looks real (has
     `package.json`, `pyproject.toml`, `Cargo.toml`, `.git`, or similar):
     offer full bootstrap (see Bootstrap section).
   - If CLAUDE.md **already exists**: offer in one line: "No SESSION.md found.
     Want me to create one? (yes/no)" — if yes, ask 1 question ("What's your
     current focus?"), then write `.claude/SESSION.md` using
     `references/session-template.md`. Skip scanning/drafting CLAUDE.md.

---

## Update triggers

### Automatic (update without asking — announce only "Updated session." in one line)

- **After every `git commit` or `git push`** that is not trivial — update
  SESSION.md immediately, without being asked. Do not wait for the user to
  request it. This is the most important trigger. If you just ran `git commit`
  or `git push` and the commit message is not "fix typo", "formatting", or
  similarly trivial, write SESSION.md right now.
- Completed feature or significant bug fix
- Architectural decision made (also update CLAUDE.md directly)
- User signals winding down: "I'm out", "tomorrow", "let's continue tomorrow",
  "done for today", "let's stop here", "wrapping up"

### Explicit (update immediately, no confirmation needed)

- "update session" / "checkpoint" / "save state"
- "before clear"
- "done for today" / "let's stop here" / "wrapping up"
- "summarize what we did"

### Not triggers (do not update)

- Code edits (happens constantly)
- Regular questions or explanations
- Trivial commits ("fix typo", "formatting", single-word messages)

---

## SESSION.md structure

Always read the file before updating. Preserve manual additions. Modify only
the relevant sections — never overwrite the whole file.

```markdown
# Session

**Updated:** YYYY-MM-DD HH:MM

## Focus
<1-2 sentences>

## Stopped at
<File:line or task. One line.>

## Next
- <Action>
- <Action>

## Open
- <Blocker, if any>

## Recent context
- <Bullet, one line>
- <Bullet, one line>
- <Bullet, one line>
```

**FIFO rule for Recent context:** Maximum 3 bullets, each one line. On every
update, if already 3 bullets, drop the oldest before adding the new one.
Never accumulate beyond 3. Never write multi-line bullets.

---

## CLAUDE.md updates

Update CLAUDE.md (not SESSION.md) when:
- An architectural decision is finalized
- A dependency is added/removed that changes the stack
- A convention is established that future sessions should know

CLAUDE.md reflects **current state** always. SESSION.md reflects
**work in progress**. If a migration is in progress, SESSION describes it.
Once done, update CLAUDE.md and remove the mention from SESSION.

When bootstrap creates CLAUDE.md, target 15-25 lines maximum. Structure:

```markdown
# <Project name>
<One sentence: what it does, for whom>

## Stack
<3-5 bullets, names+versions only>

## Architecture
<2-4 bullets, only non-obvious patterns>

## Conventions
<2-4 bullets, only non-default things>
```

If a section has nothing worth saying, omit it entirely.

---

## Bootstrap flow

Activates when: skill triggers in a directory with no `CLAUDE.md` and no
`.claude/SESSION.md`, but it has markers of a real project.

1. Offer in one line: "This project doesn't have breadcrumbs set up. Want me
   to scan it and draft CLAUDE.md + SESSION.md? (yes/no)"

2. If yes: load `references/bootstrap.md` for full instructions, then execute
   the scan, draft, question, show, and write flow described there.

3. If no: do nothing. Continue normally without creating any files.

**Important:** Never read from `examples/`. That directory is documentation
only. Only read from `references/`.

---

## Edge cases

### Stale session (>30 days old)

Append this block after the 3-line summary:

```
⚠️  SESSION is from <date> (<X> days ago) — may be stale. Want a fresh bootstrap?
```

### User edited SESSION.md manually

Always read current file before writing. Preserve what's there. Only
modify/append relevant sections.

### CLAUDE.md already exists with different structure

Respect it. Do not reformat. Bootstrap structure applies only when creating
from scratch.

### Corrupted or malformed SESSION.md

Be tolerant. Read what you can. Show the user what was understood. Normalize
on next update. Never delete user content without confirmation.

### CLAUDE.md vs SESSION.md conflict

CLAUDE.md = present state. SESSION.md = work in progress. They describe
different things and don't conflict by definition.

---

## Principles

- **No asking which project.** Use `cwd`. Claude Code already auto-loads
  CLAUDE.md from there.
- **3-line ceiling.** The opening summary is never more than 3 lines. Ever.
- **No ceremony.** No "Here's your summary:", no trailing "Let me know if
  you have questions." Just the 3 lines.
- **No configuration.** Opinionated by design. Users who want different — fork.
- **Token budget:** CLAUDE.md ~300-400 tokens, SESSION.md ~300-400 tokens.
  Total session overhead ~700 tokens. Keep both files lean.
- **Announce updates quietly.** When auto-updating, say only "Updated session."
  on one line. Don't explain what changed.
