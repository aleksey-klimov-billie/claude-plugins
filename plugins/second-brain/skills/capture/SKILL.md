---
name: capture
description: Use when the user, while working in some other project, asks to capture, save, jot, or drop an insight / note / snippet / link into their personal knowledge vault — aka "second brain", "my vault", "my Obsidian vault", or "the inbox". Examples: "capture this to my second brain", "save this to my vault", "drop this in my brain inbox". NOT for authoring, filing, or promoting notes inside the vault itself.
---

# Capture to Second Brain

Drop raw material into the vault's **inbox** from whatever project you're in. That's the whole job.

## Core principle

A capture is a **dumb inbox drop** — one freeform file, nothing else. You are NOT authoring a
knowledge note and you are NOT in the vault's authoring flow. Filing, tagging, linking, and hub
edits happen **later**, via `second-brain:process` in a dedicated vault session that has the vault
context you lack here. Doing that work now means guessing the wrong home and breaking the vault's
linking rules.

The inbox is designed for exactly this: no frontmatter or naming rules are required there. Keep it
cheap.

## Locate the vault

The vault path comes from the `SECOND_BRAIN_VAULT` environment variable.

```bash
[ -d "$SECOND_BRAIN_VAULT/inbox" ] && echo ok
```

If it is unset or that directory is missing, **stop and tell the user** — wrong machine, or the
vault moved. Do not create directories, and do not guess a path.

## What a capture IS (the recipe)

Exactly one new file at `$SECOND_BRAIN_VAULT/inbox/<slug>.md`:

```
---
type: inbox
captured: <YYYY-MM-DD from `date +%F`>
source: <repo name> (<branch or ticket>)
---

# <Short Title Case title> (to process)

Why: <one line — the durable insight in a sentence + a guess at its likely home, e.g. "feeds the payments domain">

<the raw material: the insight in full, any code snippet, the reasoning, links>
```

- `<slug>`: short kebab-case, **no date** in the filename (the date lives in frontmatter).
- Include enough raw material that future-you can promote it **without the original session** —
  paste the snippet and the reasoning, don't just leave a pointer.

## Steps

1. Get today's date: `date +%F`.
2. Build `source` from the current project: repo name
   (`basename "$(git rev-parse --show-toplevel)"`) + branch (`git branch --show-current`) or ticket.
3. Choose a short kebab-case slug and a Title Case heading.
4. Write the file per the recipe above. **Check first that `inbox/<slug>.md` doesn't already
   exist — if it does, pick a more specific slug; never overwrite.**
5. Confirm to the user: the path written and a one-line summary. Tell them it's staged in the inbox
   to promote next time they're in the vault.

## Stay a capture (the boundary)

The temptation is to be "helpful" and file it properly. Don't — that's the one thing this skill
exists to prevent.

- **Only `inbox/`.** Never create a `type: concept | howto | reference` note; never place the file
  in a domain/topic folder.
- **No hub edits.** Never touch any `_index.md` or add a hub bullet.
- **Frontmatter is exactly the three keys above.** No `domain/` / `topic/` / `status/` tags, no
  `related:` links, no aliases.
- **Don't follow the vault's `CLAUDE.md` authoring guide.** It governs promotion, not capture. If
  you find yourself reading it, stop.
- **No commit, no push.** Leave the file untracked for the user to review and promote.

## Red flags — you've left "capture" and started "promotion"

- Editing an `_index.md`, or adding a `related:` / hub bullet
- Setting `type:` to anything but `inbox`, or adding `domain/`/`topic/`/`status/` tags
- Reading the vault's `CLAUDE.md` to "file it correctly"
- Running `git add` / `git commit` in the vault

All of these mean: stop, delete the extra work, leave a single `inbox/<slug>.md`.
