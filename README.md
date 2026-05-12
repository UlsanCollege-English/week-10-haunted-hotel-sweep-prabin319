[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/I7NCKCh8)
# Week 10 Coding #8: Haunted Hotel Sweep

## Summary

This assignment models a haunted hotel as an **undirected graph**, where each area (Lobby, Tower, etc.) is a node and each hallway or door is an edge stored in an adjacency list. **BFS** (Breadth-First Search) sweeps the hotel level by level using a queue, visiting all nearby areas before going deeper — good for finding shortest paths. **DFS** (Depth-First Search) uses a stack to dive as deep as possible down one path before backtracking — good for exhaustive exploration. The `visited` set is essential in both algorithms: without it, cycles in the graph (e.g. Lobby → Hallway → Library → Cellar → Kitchen → Dining Room → Lobby) would cause infinite loops, trapping the guard (and the program) forever.

---

## Approach

- **`get_neighbors`**: Used `dict.get(area, [])` to safely return the neighbor list or an empty list if the area is missing — no KeyError risk.
- **`has_path`**: Checked upfront whether both `start` and `target` exist in the graph. Then ran BFS from `start`, returning `True` the moment `target` is dequeued.
- **`bfs_order`**: Initialised a `deque` with `start`, marked it visited, then repeatedly dequeued the front, appended it to the result, and enqueued any unvisited neighbors.
- **`dfs_order`**: Used a list as a stack. When popping a node, checked visited *at pop time* (not push time) to handle duplicates pushed onto the stack. Pushed neighbors in `reversed(...)` order so the first neighbor in the adjacency list is explored first.
- **`count_reachable_areas`**: Ran standard BFS from `start` and returned `len(visited)` at the end.
- **Preventing repeated visits**: Every node is added to the `visited` set before (BFS) or at (DFS) the moment it is first processed, ensuring no area is visited twice.

---

## Complexity

### `get_neighbors`

- Time: O(1) average
- Space: O(1)
- Why: Dictionary lookup is O(1) average. No extra memory is allocated beyond returning the existing list reference.

### `has_path`

- Time: O(V + E)
- Space: O(V)
- Why: BFS visits each node at most once (V steps) and inspects each edge at most twice in an undirected graph (E steps). The `visited` set and queue each hold at most V entries.

### `bfs_order`

- Time: O(V + E)
- Space: O(V)
- Why: Same reasoning as `has_path` — each node and edge is processed once. The queue and visited set scale with the number of reachable nodes.

### `dfs_order`

- Time: O(V + E)
- Space: O(V)
- Why: Each node is visited once and each edge is examined once. The stack can hold at most V entries in the worst case (a straight-line graph).

### Stretch: `count_reachable_areas`

- Time: O(V + E)
- Space: O(V)
- Why: Identical BFS traversal to `bfs_order`. Returns the size of the visited set rather than a list of nodes.

---

## Edge-Case Checklist

- [x] empty graph — all functions return `[]`, `False`, or `0` immediately via missing-key checks
- [x] missing start area — `get()` returns `[]`; BFS/DFS return `[]`; `has_path` returns `False`
- [x] missing target area — `has_path` returns `False` after the upfront existence check
- [x] `start == target` — BFS in `has_path` dequeues `start` first and immediately matches `target`
- [x] graph with a cycle — the `visited` set prevents re-processing any node
- [x] disconnected graph — BFS/DFS only visit the connected component of `start`; `has_path` returns `False` for nodes in a different component
- [x] area with no neighbors — `graph.get(area, [])` returns `[]`; the inner loop does nothing

**Tricky case:** In `dfs_order`, the visited check must happen at *pop* time, not push time. The same node can be pushed onto the stack multiple times via different neighbors; checking at pop time (with `if current in visited: continue`) cleanly handles all duplicates.

---

## Tests Added

- No additional tests were added. All provided tests pass with the current implementation.

---

## Known Limitations

No known limitations.

---

## Assistance & Sources

AI used? Y

Helped with:
- explanations of BFS vs DFS traversal order
- clarifying why the visited check in DFS belongs at pop time rather than push time
- README write-up and complexity explanations