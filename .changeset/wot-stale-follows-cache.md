---
---

fix(wot): ignore stale reads when caching follows

In `wot_graph_service.rs`, `get_follows` reads contact lists from storage
with an `.await` and previously wrote the cache unconditionally. If a newer
contact list was committed and cached via `update_follow_list` while the
storage read was in flight, the older read would overwrite the newer cache
entry, leaving the graph with stale trust relationships.

Cache entries now track a per-pubkey generation counter incremented by
`update_follow_list`, and `get_follows` refuses to overwrite any entry
whose generation changed during the read.
