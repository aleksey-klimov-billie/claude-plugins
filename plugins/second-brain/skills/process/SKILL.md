---
name: process
description: Use when working inside the second-brain vault and the user wants to process, promote, file, triage, or clear the inbox — e.g. "process my inbox", "promote these captures", "file the inbox", "clear the inbox queue", or naming a single inbox item to turn into a knowledge note. NOT for dropping new material into the inbox from another project.
---

# Process the inbox

Promote raw `inbox/` captures into properly filed knowledge notes, one at a time, confirming each
placement with the user before writing.

## Core principle

Filing is a **judgment call, not a transformation**. Placement, tags, and hub edits are hard to
unpick once they land in the graph, so the user approves each item's destination *before* anything
is written. You do the reading, searching, and drafting; they approve the home.

## Step 0 — Pre-flight

Confirm you are in the vault, then list the queue:

```bash
[ -f Home.md ] && [ -f _meta/Tags.md ] && [ -d inbox ] && echo "vault ok"
ls -1 inbox/ | grep -vx 'README.md'
```

- Not the vault? **Stop** and ask the user to `cd` there. Never process from elsewhere.
- Queue empty? Say "inbox is empty — nothing to process" and **stop**.
- **`inbox/README.md` is documentation, not a capture.** It is never processed and never deleted.

Read the vault's `CLAUDE.md` and `_meta/Tags.md` before the first item — they are the authority on
structure and the tag vocabulary. Then announce the queue and work items one at a time.

## Per item — the loop

### 1. Read and search

Read the capture in full. Then **search before proposing** — the top rule of the vault is never to
duplicate an existing note:

```bash
grep -ril "<key term>" --include='*.md' . | grep -v '^./inbox/'
```

Search two or three of the item's key terms. A near-miss changes the outcome: prefer **merging into
an existing note** over creating a near-duplicate.

### 2. Decide the destination

| Situation | Destination |
|---|---|
| An existing note already covers it | Merge into that note; bump its `updated:` |
| A topic folder fits | New note in that topic |
| A domain fits but no topic does | New note loose in the domain |
| Nothing fits | New note loose at root, or leave in `inbox/` |
| ~3+ loose notes in a domain share its theme | Crystallize a new topic (folder + `_index.md`) and move them in |
| Not durable knowledge (a link to read, a stale scrap) | Propose discarding, or filing a source into `_attachments/` |

Never nest topics — max depth is `domain/topic/note`.

### 3. Propose, then wait

Show the user: the destination path, the note `type`, the exact tag list, the owning hub, and — if
merging or discarding — what gets changed or lost. **Wait for approval.** If they redirect, follow
their placement, not yours.

## The filing contract

An item is only "processed" when **every** one of these is done. Missing one leaves the vault
inconsistent — an orphan note, an unregistered tag, or a stale queue.

1. **New tags registered first.** Any `domain/` or `topic/` tag not already in `_meta/Tags.md` is
   added there *before* it is used in a note.
2. **The note exists**, based on `_templates/Knowledge Note.md`, at `<dest>/<Title Case>.md` — no
   date in the filename. Frontmatter: `title`, `created` (today), `updated` (today), `type`
   (`concept` | `howto` | `reference`), `tags`, `aliases: [<title>]`, `related`. Tag order follows
   the vault: `topic/<t>`, `domain/<d>`, `context/work|personal`, optional `status/`. A note in a
   topic inside a domain carries both the `topic/` and `domain/` tags. Keep the one-sentence
   summary blockquote under the H1.
3. **It links up** — `part of [[<owning hub>]]` via the hub's alias, plus any related notes.
4. **Its hub lists it** — a bullet under the right section of that `_index.md` (`Core concepts`,
   `How-to / Runbooks`, `Deep dives`, …), and the hub's `updated:` is bumped. A new topic's
   `_index.md` is also linked from its parent hub.
5. **The capture is deleted** from `inbox/`. The inbox trends toward empty.
6. **One concept per note.** A capture holding two unrelated insights becomes two notes.

### One-way linking — never violate

Links flow `inbox/ -> daily/ -> knowledge`, one way only. **Never** write a link from a knowledge
note or hub into `daily/` or `inbox/` — not in the body, not in a hub bullet, not in `related:`.
Promote the content instead of citing the ephemeral file. (Obsidian's automatic backlinks are fine;
those aren't authored links.)

## Step N — Report

When the queue is done, report per item: destination, hub updated, tags added, and anything
discarded or merged. List anything you deliberately left in `inbox/` and why.

**Do not commit.** Leave the changes staged in the working tree for the user to review — the vault's
default branch is usually `main`, and the commit is theirs to make.

## Red flags — stop and re-read the contract

- About to write a note without having grepped for an existing one
- Using a `domain/` or `topic/` tag that isn't in `_meta/Tags.md` yet
- Note created but its hub `_index.md` untouched — an orphan
- Capture still sitting in `inbox/` after its note landed
- A `related:` entry or hub bullet pointing at `inbox/` or `daily/`
- Deleting or rewriting `inbox/README.md`
- Filing everything in one silent pass without the user approving placements
