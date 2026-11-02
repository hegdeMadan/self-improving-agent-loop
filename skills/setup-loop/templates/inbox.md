# Inbox

The owner's requests, ideas and reports, in their own words, waiting for the loop. Any session that receives one writes it here (the `inbox` skill). The loop reads this file; it never sees chat.

```
- **YYYY-MM-DD · <urgent|next|later>** — <the request, quoted> · *from: <chat | PR #n | …>*
```

| Severity | Means | Who handles it |
|---|---|---|
| `urgent` | broken or wrong in production | the receiving session fixes it in a small PR; never queued |
| `next` | a change to something already built | the manager, before the next slice |
| `later` | a new idea | the manager, once no `next` item or slice waits |

An item beginning `PARKED` is the owner's: the loop skips it until the owner removes the word.

When an item is routed or fixed it moves, unchanged, to the top of `history/inbox-done.md` with ` → <queue/NN-slug or PR link>`.

## Open
