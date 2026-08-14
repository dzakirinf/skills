# Cluster reconciliation checklist

Run after `getConfluencePageDescendants` on the overview, on the returned **metadata only** (titles/IDs/depth/links — no page bodies, so this is cheap over the whole subtree). These are the defects you only see treating the cluster as one unit — not page by page. Report them with the map; fix them within the approved content-audit scope (offer out-of-scope ones with their level).

- [ ] **Index ↔ descendants.** Does the overview's "Pages in this space" list match the *actual* direct children? Flag children missing from the index and index entries with no page.
- [ ] **Up-links.** Does **every** non-top page link back to its parent? Missing up-links are systematic — usually all leaves at once, one cluster-wide fix.
- [ ] **Sibling mesh.** Do related siblings link each other with real links (not a plain-text name-drop in a table cell)? Flag any island leaf.
- [ ] **Orphaned grandchildren.** Did a leaf grow its own child (a depth-2 page) that nothing links down to? Either index it from its parent (the parent is now also a hub) or, if it's a procedure, recognise it belongs out of the cluster (a runbook) and link to it.
- [ ] **Duplicated content.** Is a child's material also pasted into the parent? The parent should link down, not duplicate. Decide which page **owns** the content.
- [ ] **Supersession.** If the cluster supersedes/extends another page, are the links reciprocal and the relationship stated on both ends?

Each item is a single cluster-level task even when it touches many pages — record it once, not N times.
