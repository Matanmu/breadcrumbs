# breadcrumbs

A Claude Code skill that solves session continuity.

After `/clear` or a new session, Claude knows what you were doing — without you
re-explaining every time.

---

## The problem

Claude Code sessions are stateless. Every `/clear` or new terminal means
starting from scratch: re-explaining the project, what you were working on,
where you left off. For active projects, this adds up to minutes of overhead
every day.

## The fix

Two files, ~700 tokens total, loaded automatically:

- **`CLAUDE.md`** at project root — static context (stack, architecture,
  conventions). Claude Code auto-loads this every session.
- **`.claude/SESSION.md`** — dynamic state (current focus, where you stopped,
  next step). Breadcrumbs reads this when you return.

Say "where were we" or "let's continue" and you get:

```
📍 Last: Adding Stripe webhook handling for subscription lifecycle events
⏸️  Stopped at: src/webhooks/stripe.ts:84 — handleSubscriptionCanceled, proration decision pending
➡️  Next: Check Stripe docs on proration, then write tests for all three handlers
```

Three lines. No preamble. You're back in context immediately.

---

## Install

Clone directly into your Claude Code skills directory:

```bash
git clone https://github.com/matanmu/breadcrumbs.git ~/.claude/skills/breadcrumbs
```

Verify:

```bash
ls ~/.claude/skills/breadcrumbs/SKILL.md
```

**Hot-reload note:**
- If `~/.claude/skills/` already existed, the skill is active immediately —
  no restart needed.
- If `~/.claude/skills/` did **not** exist before this install, restart
  Claude Code once to start watching the directory.

Update later:

```bash
cd ~/.claude/skills/breadcrumbs && git pull
```

**No git?** Download the ZIP from GitHub, then:

```bash
unzip breadcrumbs-main.zip
mv breadcrumbs-main ~/.claude/skills/breadcrumbs
```

---

## Use

### First time on a project

Just say "let's continue" or "where were we" in any Claude Code session.

If the project doesn't have breadcrumbs set up yet, the skill will offer to
scan and draft the files for you:

> "This project doesn't have breadcrumbs set up. Want me to scan it and draft
> CLAUDE.md + SESSION.md? (yes/no)"

Say yes, answer 1-2 questions, approve the draft. Done.

### Ongoing usage

Breadcrumbs updates SESSION.md automatically at natural breakpoints:
meaningful commits, completed features, architectural decisions, winding-down
signals ("I'm out", "done for today").

You can also trigger it explicitly:
- "checkpoint" / "save state" / "update session"
- "before clear"
- "done for today" / "wrapping up"

When returning to a project:
- "where were we" / "let's continue" / "catch me up" / "resume"
- Or just start your first message — if it implies picking up prior work,
  breadcrumbs activates.

Works in any language. Say "המשך" or "איפה היינו" — it understands.

---

## .gitignore recommendation

`SESSION.md` is per-developer state, not team-shared. Add it to `.gitignore`:

```
.claude/SESSION.md
```

`CLAUDE.md` **should** be committed — it's shared project knowledge.

---

## Example lifecycle

**Day 1, morning** — new project, first session:
- Say "let's continue"
- Skill offers bootstrap, you approve
- `CLAUDE.md` and `.claude/SESSION.md` created

**Day 1, evening** — wrapping up:
- Say "done for today"
- Skill updates SESSION.md with current focus, stopped position, next steps
- "Updated session."

**Day 2, morning** — new session:
- Say "where were we"
- Skill reads SESSION.md:
  ```
  📍 Last: ...
  ⏸️  Stopped at: ...
  ➡️  Next: ...
  ```
- You're in context. Start working.

---

## Philosophy

**Minimal.** Two files, ~700 tokens total overhead per session. If something
is obvious from `package.json` or the file tree, it doesn't go in CLAUDE.md.

**Opinionated.** No configuration. The format is fixed. The token budget is
fixed. Users who want something different — fork it.

**Cheap.** The whole point is to cost less than re-explaining context. If the
files get bloated, they defeat themselves. Aggressive minimalism is a hard
constraint, not a preference.

---

## Contributing

Bug reports and install issues: open an issue.

Feature requests: read the philosophy section first. If your feature adds
configuration, a new file type, or grows the token budget — it won't be merged.
Breadcrumbs is intentionally small. Fork if you need more.

---

## License

MIT — see [LICENSE](LICENSE).
