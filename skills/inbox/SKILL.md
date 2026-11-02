---
name: inbox
description: Record the owner's request, bug report or idea in the loop's inbox with a severity, so the loop picks it up. Use whenever the owner asks for a change, reports a problem or shares an idea in a project that runs the self-improving agent loop.
---

# Add to the inbox

When the owner asks for a change, reports a problem or shares an idea, do not start editing code. Record it in `docs/product/inbox.md` under **Open**, newest at the bottom:

```
- **YYYY-MM-DD · <urgent|next|later>** — <the request, in the owner's words> · *from: <chat | PR #n | …>*
```

| Severity | Means | What happens |
|---|---|---|
| `urgent` | broken or wrong in production | fix it now in a small PR (an `engineer` brief and a `code-reviewer` pass), then move the entry to `docs/product/history/inbox-done.md` with the PR link |
| `next` | a change to something already built | the manager routes it before the next north-star slice |
| `later` | a new idea or feature | the manager routes it once no `next` item or slice waits |

**Quote the owner.** Mark anything you add as yours. Never narrow the request: a restriction the owner did not state is a question, not part of the item.

If the severity is unclear, ask once; otherwise decide and say which.

The owner may write `PARKED` at the start of an item (they are planning it) or ask for a hold: the loop skips it until the owner removes the word.

Then tell the owner in one line where it went and what happens next. If the loop's routine is paused, say so: nothing in the inbox is picked up until it runs.

**Not everything is the loop's.** A request about something outside this project, or about the loop itself rather than the product, does not go in this inbox.
