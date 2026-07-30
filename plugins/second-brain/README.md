# second-brain

The two halves of a personal knowledge-vault workflow, split so each runs where it belongs:

- **`/second-brain:capture`** — run from *any* project. Drops one freeform file into the vault's
  `inbox/`. Deliberately dumb: no filing, no tagging, no hub edits.
- **`/second-brain:process`** — run from *inside* the vault. Walks the `inbox/` queue and promotes
  each capture into a filed, linked, tagged note, confirming the destination with you first.

The split exists because filing needs vault context that a session in some other repo doesn't have.
Capture is cheap and immediate; promotion is a judgment call made later, deliberately.

## The vault model

```
_index.md                   root hub (aliased "Home")
<domain>/_index.md          domain hub
<domain>/<topic>/_index.md  topic hub
<domain>/<topic>/Note.md    a note — one concept
daily/, inbox/, _meta/, _templates/, _attachments/
```

`domain -> topic -> note`, max depth. Every domain and topic folder has exactly one `_index.md` hub
that links its notes. `_meta/Tags.md` is the controlled tag vocabulary — a tag is registered there
before it is used. Links flow one way: `inbox/ -> daily/ -> knowledge`, never back.

The vault's own `CLAUDE.md` is the authority on these conventions; `process` reads it at the start of
every run, so the vault stays the source of truth rather than this plugin.

## Setup

```bash
# 1. Add the marketplace (once)
/plugin marketplace add axklim/claude-plugins

# 2. Install
/plugin install second-brain@axklim
```

Then point `capture` at your vault by setting `SECOND_BRAIN_VAULT` in `~/.claude/settings.json`:

```json
{ "env": { "SECOND_BRAIN_VAULT": "/absolute/path/to/your/vault" } }
```

`capture` refuses to guess a path — if the variable is unset or `$SECOND_BRAIN_VAULT/inbox` is
missing, it stops and tells you. `process` needs no configuration; it runs against the current
directory and pre-flights that it looks like a vault.

## How the pieces fit

```
any project ──/second-brain:capture──> $SECOND_BRAIN_VAULT/inbox/<slug>.md
                                              │
in the vault ──/second-brain:process──────────┘──> <domain>/<topic>/<Note>.md
                                                   + hub bullet in _index.md
                                                   + tags registered in _meta/Tags.md
                                                   - capture deleted from inbox/
```

Neither skill commits. Changes are left in the working tree for you to review — the vault's default
branch is usually `main`, and the commit is yours to make.

## Notes & assumptions

- **It's a Markdown vault, not a code repo** — atomic notes, `[[wikilinks]]`, YAML frontmatter.
  Obsidian is optional; any editor works.
- **Privacy.** Captures are written by you, on purpose, one at a time — nothing is swept up
  automatically. Still, treat the vault as private: it accumulates work context and belongs in a
  private repo.
- **`inbox/README.md`** documents the queue's conventions. `process` never treats it as a capture and
  never deletes it.
