# Depth First Search — Complete Study Notes

*Compiled from: DFS basics, Iterative DFS, DFS in Directed Graphs, Cut Vertices*

---

## 1. What DFS Actually Is

DFS is a **systematic exploration of a graph** — it works on both **undirected** and **directed** graphs. The core idea: from a vertex, plunge as deep as possible along one path before backing up.

Every vertex is either:
- **Unvisited** — never touched by the DFS.
- **Visited** — DFS has started exploring from it.

### 1.1 Vanilla (informal) version

```
DFS(G, u):
    Mark u visited.
    for each v in Adj(u):
        if v is NOT visited:
            DFS(G, v)
```

This is the whole idea in four lines. Everything else in this document is refinement of this skeleton — extra bookkeeping (colors, timestamps, parent pointers) layered on top to answer richer questions (is the graph connected? is there a cycle? which edges are "tree" edges? which vertex is a cut vertex?).

### 1.2 Driver routine — DFS over the *whole* graph

A single call `DFS(G,u)` only explores the **component reachable from u**. To cover a possibly disconnected graph, wrap it:

```
DFS(G):
    for each u in V:
        mark u unvisited          // "White"
    for each u in V:
        if u is unvisited:
            DFS(G, u)
```

**Why loop twice?** You must mark *everything* unvisited first — otherwise a half-initialized array could be misread as "visited" partway through setup.

---

## 2. Tree Edges vs Non-Tree Edges

When DFS moves from a **visited** vertex `u` to an **unvisited** neighbor `v` (i.e., it makes the recursive call `DFS(G, v)`), the edge `(u, v)` is a **tree edge**.

When DFS looks at an edge `(u, v)` where `v` is **already visited**, that edge is a **non-tree edge** — DFS does *not* recurse along it, but the edge still exists in G.

```
(u,v):  visited → NOT visited   ⇒  Tree edge
(u,v):  visited → visited       ⇒  Non-Tree edge
```

### 2.1 DFS Tree / DFS Forest

- The **tree edges** discovered during one call `DFS(G,u)` form a **DFS tree** rooted at `u`.
- If `G` is **connected**, one call to DFS from any vertex visits everything → a **single DFS tree**.
- If `G` is **disconnected** (several components), the outer driver loop launches DFS multiple times (once per component) → the tree edges collectively form a **DFS forest** (one tree per component).

**Worked adjacency-list example** (vertices a, b, c, d, all mutually connected — like K4):
```
a → b → c → d
b → a → c → d
c → a → b → d
d → a → b → c
```
Starting DFS at `a`: visit `a`, go to `b` (tree edge a–b), from `b` go to `c` (tree edge b–c, since `a` is already visited when b scans it), from `c` go to `d` (tree edge c–d). All other edges (a–c, a–d, b–d, etc.) become **non-tree edges** because by the time they're examined, both endpoints are visited.
Result: DFS tree is the simple path `a – b – c – d`, and it's an "in-tree"/simple chain shape here, though in general a DFS tree branches (an **"out-tree"**, i.e., edges point away from the root, since parent discovers child).

### 2.2 Undirected graphs: only tree & back edges

**Key structural fact for undirected graphs**: every non-tree edge in an undirected DFS is a **back edge** — it connects a vertex to one of its **ancestors** in the DFS tree. This is because in an undirected graph, if `(u,v)` is examined from `u` and `v` is already visited, `v` must currently be "live" (an ancestor still on the recursion stack) — it cannot be a fully-finished, unrelated vertex, because if it were finished, the edge `(u,v)` would already have been seen from `v`'s side (when `v` explored its own adjacency list and looked at `u`) — forcing `u` to have been visited at that time too, i.e., `u` would be a descendant, contradiction is avoided only when `v` is still active. (No forward/cross edges possible in undirected DFS — see §4 for the directed contrast.)

---

## 3. The Three-Color Scheme (White / Gray / Black)

This is the standard refinement of "visited/unvisited" that gives DFS a precise, three-state model of *how far along* a vertex's exploration is.

| Color | Meaning |
|---|---|
| **White** | Not visited yet |
| **Gray**  | Visited and **live** — currently on the DFS recursion stack, some descendant's exploration still in progress |
| **Black** | Completed the visit — all of `u`'s neighbors processed; DFS has "left for its parent" |

### 3.1 Parent pointers and discovery/finish times

- `v.π` (or `v.p`) = the **parent** of `v` in the DFS tree. If `u.π = v` is wrong direction — convention: `v.π = u` means `u` is **the** parent of `v`, and `v` is **a** child of `u`. The edge `(v.π, v)` is the tree edge ending at `v`.
- `v.d` = **discovery time** — the `Time` counter value when `v` turns White → Gray.
- `v.f` = **finish time** — the `Time` counter value when `v` turns Gray → Black.

`Time` is a single global counter incremented **every time any vertex changes color** (White→Gray or Gray→Black). With `n` vertices, `Time` runs from `1` to `2n`, so every vertex gets a distinct pair `[v.d, v.f]` with `v.d < v.f`, and these `2n` timestamps are all-distinct consecutive integers.

Visualize each vertex as occupying an **interval** on the timeline `[v.d, v.f]`:
```
1                                   2n
|---- W ----[u.d]---- Gray/Active ----[u.f]---- B ---->
```
- Before `u.d`: unvisited (White)
- Between `u.d` and `u.f`: active/Gray
- After `u.f`: finished (Black)

### 3.2 Full pseudocode (recursive, with colors & times)

```
DFS(G):
    for each u in V:
        u.color = White
        u.π = NULL
    Time = 0
    for each u in V:
        if u.color == White:
            DFS(G, u)

DFS(G, u):
    Time = Time + 1
    u.d = Time
    u.color = Gray
    for each v in Adj(u):
        if v.color == White:
            v.π = u
            // (u,v) is a Tree edge
            DFS(G, v)
        else:
            // (u,v) is a Non-Tree edge
            ... (see §4 for directed-graph classification)
    Time = Time + 1
    u.f = Time
    u.color = Black
```

### 3.3 The parenthesis / interval theorem (crucial for classifying edges)

For any two vertices `u, v`:
- Their `[d, f]` intervals are either **nested** (one inside the other) or **completely disjoint** — never partially overlapping (like crossed lines "✗", which is *impossible*).
- If `u.d < v.d < v.f < u.f` → `v` is a **descendant** of `u` in the DFS tree (nested interval).
- If the intervals are disjoint → neither is an ancestor of the other.

This "no partial overlap" property is what makes discovery/finish times such a powerful tool for classifying edges and proving graph properties (e.g., cut vertices, SCCs).

---

## 4. DFS on Directed Graphs — Four Types of Edges

In an **undirected** graph, non-tree edges are always back edges. In a **directed** graph, DFS produces up to **four** categories of edges relative to the DFS forest, because direction breaks the symmetry that ruled out forward/cross edges above.

| Type | Symbol | Definition |
|---|---|---|
| **Tree edge** | — | `(u,v)`: `u` visited, `v` White → becomes tree edge, DFS recurses |
| **Back edge** | `B` | `(u,v)`: `v` is Gray (still active) when `u` examines it → `v` is an **ancestor** of `u` |
| **Forward edge** | `F` | `(u,v)`: `v` is Black, but `v.d > u.d` → `v` is a **descendant** of `u` (an "ancestor-to-descendant" shortcut edge, skipping over tree edges) |
| **Cross edge** | `C` | `(u,v)`: `v` is Black **and** `v.f < u.d` → `v` is **neither ancestor nor descendant** of `u` — could go between different subtrees of the same DFS tree, or between two entirely different DFS trees in the forest |

### 4.1 Classification pseudocode (the `else` branch of §3.2, directed case)

```
else:   // (u,v) is a non-tree edge
    if v.color == Gray:
        // (u,v) is a BACK edge
    else if v.f < u.d:
        // (u,v) is a CROSS edge
    else:
        // (u,v) is a FORWARD edge
```

**Intuition for why this works:**
- **Gray** target ⇒ target is an active ancestor ⇒ **back edge**.
- **Black** target with `v.f < u.d` ⇒ `v`'s entire subtree finished **before** `u` was even discovered ⇒ `v`'s interval lies entirely to the left of `u`'s interval on the timeline ⇒ disjoint intervals ⇒ **cross edge** (no ancestor/descendant relation).
- **Black** target with `v.f > u.d` (equivalently `v.d < u.d`, since v is already black) ⇒ `v`'s interval **contains** `u`'s interval isn't right either — actually here `v.d < u.d < u.f < v.f`? No — re-derive: since `v` is Black already and `u` is currently Gray (active, examining its own adjacency list), and the only remaining case is `v.f` is **not** less than `u.d`, meaning `v` was discovered *before* `u` finished, so `v.d < u.d` and `v` finished after `u` started ⇒ `u.d < v.f` — combined with `v` already Black at this point in `u`'s exploration, the intervals nest with `u` inside `v`... 

  **Simpler restatement (matches the notes exactly):** ℓ = (u, v). If `u` is an ancestor and `v` a descendant reached via a non-tree shortcut → **Forward edge**. If `v` is an ancestor and `u` a descendant → this is caught by the Gray check → **Back edge**. If neither → **Cross edge**.

### 4.2 Visual summary

```
Ancestor ──F──> Descendant     (Forward: skips tree edges, same branch, downward)
Descendant ──B──> Ancestor     (Back: points back up the same branch)
Unrelated  ──C──> Unrelated    (Cross: between different branches/subtrees or different trees)
```

### 4.3 Big application: Strongly Connected Components (SCCs)

The tree/back/forward/cross classification of directed-DFS edges is exactly the machinery used to **find the strongly connected components of a directed graph** (e.g., Kosaraju's / Tarjan's algorithms build directly on discovery/finish times and back-edge detection). The key fact that makes this work: a directed graph has a cycle **iff** DFS on it produces at least one **back edge**.

---

## 5. Iterative (Non-Recursive) DFS

Recursive DFS implicitly uses the **call stack** to remember "where I was" in each vertex's adjacency list when I recursed deeper. Iterative DFS makes this **explicit** using our own stack.

### 5.1 Data structures

- Graph `G` represented as an **adjacency list**: `u → Adj(u)`, a linked list where each box is:
  ```
  [ Vertex | Next ]
  ```
  `Vertex` = the neighbor, `Next` = pointer to the next box in the list. `u.first` points to the head box of `u`'s list; `NULL` marks the end.

- `ptr` = a **pointer into an adjacency list**, indicating a specific position (which neighbor we've reached so far).
- The pair `(u, ptr)` = "we are at vertex `u`, and the neighbor-position we're currently exploring/about-to-explore is `ptr`."
- `S` = an explicit **stack** containing pairs `(u, ptr)` — each pair represents the position of the next neighbor of `u` still to be explored. This stack is exactly what "remembers" partially-explored vertices, replacing the recursion call stack.

### 5.2 Full pseudocode

```
Non-Recursive-DFS(G, u):
    S = ∅                              // stack of (vertex, ptr) pairs

    Time = Time + 1
    u.d = Time
    u.color = Gray
    Push[(u, u.first), S]

    while (S ≠ ∅):
        (u, ptr) = POP(S)

        if (ptr ≠ NULL):
            Push((u, ptr.next), S)     // remember: come back to u's NEXT neighbor later
            w = ptr.vertex

            if (w.color == White):
                w.π = u
                Time = Time + 1
                w.color = Gray
                Push[(w, w.first), S]  // dive into w
            else:
                // (u,w) is a non-tree edge
                ... do computations related to non-tree edge (u,w) ...

        else:   // ptr == NULL  ⇒  all neighbors of u have been visited
            u.color = Black
            Time = Time + 1
            u.f = Time
```

### 5.3 Why it's built this way — line-by-line intuition

- **`(u, ptr) = (u, NULL)` signals**: all neighbors of `u` were already visited/pushed → so visiting at `u` can now be **finished** (blacken it, record finish time). This mirrors the recursive version's "after the for-loop ends, blacken u."
- **`Push((u, ptr.next), S)` immediately after popping**: after we finish handling the *current* neighbor position `(u, ptr)`, we must eventually continue with `(u, ptr.next)` — the *next* neighbor in `u`'s list. That's why it's pushed right away, **before** we dive into `w`. This re-queues "the rest of u's work" underneath whatever `w` pushes on top of it — exactly recreating the stack-frame behavior of recursion.
- **General principle**: *keep pushing the positions for which explorations are still to be done into the stack.* Every pop either (a) advances to a new unvisited neighbor and dives deeper (push `w`'s own start position), or (b) discovers a non-tree edge and just continues in the same frame, or (c) finishes off `u` entirely.

### 5.4 Complexity

Same as the recursive version: **O(n + m)**, where `n = |V|`, `m = |E|`. Every vertex is pushed/popped a bounded number of times and every adjacency-list box is visited exactly once across the whole run.

---

## 6. Cut Vertices (Articulation Points)

### 6.1 Definitions

- Let `G` be a **connected** graph. A vertex `v` is a **cut vertex** if **removing** `v` (and all edges incident to it) **disconnects** `G` — i.e., `G − {v}` has more connected components than `G` did (splits the graph, e.g., a graph that was one piece becomes two: `d` is a cut vertex if removing `d` splits the remainder into pieces like `{a,b,c}` and `{e,f,g}`, i.e. `G − {d}` falls apart).

### 6.2 Graph-theoretic characterization (mathematical, but computationally infeasible directly)

**A vertex `v` is a cut vertex iff there exists a pair of vertices `x, y` (both ≠ `v`) such that every path between `x` and `y` passes through `v`.**

This is a perfectly correct definition — but checking it directly means testing **all pairs** `(x,y)` and **all paths** between them, which is computationally infeasible for large graphs (exponentially many paths in general). We need an efficient, DFS-based algorithm instead.

### 6.3 DFS-based idea: `Low(u)`

Run a single DFS from any vertex, building a DFS tree. Recall from §2.1 that for an **undirected** graph, every non-tree edge is a **back edge**, connecting a descendant to one of its (not-immediate-parent) ancestors.

Define:

```
Low(u) = min{  u.d,
               min{ v.d : (u,v) is a back edge },
               min{ Low(w) : w is a child of u in the DFS tree } }
```

In words: **`Low(u)` is the smallest discovery time reachable from `u`** by going down zero or more tree edges **and then at most one back edge** at the very end.

- Equivalently, `Low(u)` = the smallest `w.d` over all vertices `w` reachable from `u` using tree edges followed by a back edge.
- `Low(u)` gets **updated** — it starts at `u.d` and is revised downward as we discover back edges from `u` or as children report their own `Low` values back up (once a child `v` of `u` finishes its DFS, we update `u.Low = min(u.Low, v.Low)`).

### 6.4 Root special case

The **root** of the DFS tree is a cut vertex **iff it has more than one child** in the DFS tree. (Intuition: each subtree hanging off the root can only reach the rest of the tree by going back up through the root, since the root has no ancestors of its own — so ≥2 children means ≥2 mutually-unreachable-without-root pieces.)

### 6.5 Non-root cut-vertex condition

A **non-root** vertex `u` is a cut vertex **iff** it has some child `v` in the DFS tree such that:

```
Low(v) ≥ u.d
```

**Why:** `Low(v) ≥ u.d` means that from `v`'s subtree, using tree edges plus **at most one** back edge, you can never reach any vertex discovered **strictly before** `u` (i.e., you can never climb past `u` to `u`'s ancestors). So `v`'s entire subtree is "trapped" below `u` — remove `u`, and `v`'s subtree becomes disconnected from the rest of the graph. Conversely if `Low(v) < u.d` for every child `v`, every subtree has an escape route (a back edge) around `u`, so removing `u` doesn't disconnect anything.

### 6.6 Full pseudocode

```
DFS_LOW(G):
    for each u in V:
        u.color = White
        u.π = NULL
        u.d = 0
        u.Low = 0
    Time = 0
    DFS_LOW(G, u)          // u is arbitrary — the chosen root

DFS_LOW(G, u):
    Time = Time + 1
    u.d = Time
    u.Low = u.d
    u.color = Gray
    for each v in Adj(u):
        if (v.color == White):
            v.π = u
            DFS_LOW(G, v)
            u.Low = min(u.Low, v.Low)          // ★ control point ①: after recursing into a child
        else if (v ≠ u.π):
            u.Low = min(u.Low, v.d)            // ★ control point ②: back edge (u,v), v ≠ parent — exclude the trivial "back edge" to your own immediate parent along the same tree edge
    Time = Time + 1
    u.f = Time
    u.color = Black
end
```

**The two "control points"** referenced in the notes (where `Low` gets updated) are exactly:
1. **After a recursive call returns** (child `v` is fully explored): `u.Low = min(u.Low, v.Low)` — child propagates its reachability up to the parent.
2. **When a back edge `(u,v)` is encountered** (`v` already visited and `v` isn't `u`'s own direct parent): `u.Low = min(u.Low, v.d)`.

(A third bookkeeping point — checking the cut-vertex conditions themselves, `Low(v) ≥ u.d` for children, and "root has ≥ 2 children" — is typically layered on top of this same traversal, incrementing a counter each time a new child of the root is started.)

### 6.7 Iterative version

The notes flag that an **iterative version** of this Low-value computation also exists (built the same way as §5 — using an explicit stack of `(u, ptr)` pairs, with `Low` updates happening at the appropriate "pop" points instead of naturally falling out of the recursive call structure). The core logic (control points ① and ②) stays identical; only the bookkeeping of "where am I in u's adjacency list" changes from implicit (call stack) to explicit (our own stack).

---

## 7. Quick Reference Cheat-Sheet

| Concept | One-liner |
|---|---|
| Tree edge | visited → unvisited (DFS recurses) |
| Non-tree edge (undirected) | visited → visited ⇒ always a **back edge** |
| Back edge (directed) | target is Gray (active ancestor) |
| Forward edge (directed) | target is Black, descendant (`v.f > u.d`, not caught by cross condition) |
| Cross edge (directed) | target is Black, `v.f < u.d` (unrelated / finished-before-u-started) |
| White / Gray / Black | unvisited / active-on-stack / fully finished |
| `v.d`, `v.f` | discovery time, finish time; global `Time` runs 1 → 2n |
| Parenthesis theorem | any two `[d,f]` intervals are nested or disjoint, never crossing |
| DFS Tree vs Forest | one tree if `G` connected; forest (multiple trees) if disconnected |
| Iterative DFS core trick | explicit stack of `(vertex, adjacency-list-pointer)` pairs replaces the call stack |
| Cut vertex (graph-theoretic) | ∃ x, y ≠ v : every x–y path passes through v (correct but infeasible to test directly) |
| Root is a cut vertex iff | it has ≥ 2 children in the DFS tree |
| Non-root u is a cut vertex iff | some child v has `Low(v) ≥ u.d` |
| `Low(u)` | min discovery time reachable via tree edges + at most one trailing back edge |
| DFS complexity (both versions) | O(n + m) |

---

## 8. Quiz — Test Yourself

Work through these before checking the explanations. They're ordered to build from basics → directed graphs → iterative DFS → cut vertices.

---

**Q1.** In an *undirected* graph, why can a non-tree edge never be a "cross edge" (an edge between two unrelated, already-finished branches)?

<details><summary>Answer & Explanation</summary>

Because edges are symmetric in an undirected graph. Suppose `(u,v)` were examined from `u`'s side while `v` is Black (finished) and unrelated. But `v` being finished means `v`'s *own* adjacency-list scan already happened, and during that scan `v` would have examined the edge `(u,v)` too (undirected ⇒ appears in both lists). At that time, if `u` were still White, `v` would have made it a tree edge and `u` would become `v`'s descendant — contradicting "unrelated." If `u` were already visited when `v` scanned it, `u` must have been Gray (active) at that time (can't be finished, since finished means done scanning, and `u` hasn't scanned `(u,v)` yet by assumption) — meaning `v` is a descendant of `u`, i.e., they *are* related after all. Either way, "unrelated + finished" is impossible — so the only non-tree edges possible are **back edges** (edges to a still-active ancestor).
</details>

---

**Q2.** What do the discovery time `u.d` and finish time `u.f` actually represent, and why does `Time` run from `1` to `2n` rather than `1` to `n`?

<details><summary>Answer & Explanation</summary>

`u.d` is the value of the global `Time` counter at the moment `u` turns White→Gray (starts being explored); `u.f` is the value when `u` turns Gray→Black (finishes being explored — all its neighbors processed). `Time` is incremented on **every** color change, and each of the `n` vertices undergoes **two** color changes (White→Gray and Gray→Black), giving `2n` total increments, hence timestamps `1` through `2n`, all distinct.
</details>

---

**Q3.** Two vertices `u` and `v` have discovery/finish intervals `[u.d, u.f]` and `[v.d, v.f]`. Which of these interval relationships is *impossible* during a DFS, and what does it mean when `u.d < v.d < v.f < u.f`?

<details><summary>Answer & Explanation</summary>

**Impossible:** partial overlap — e.g., `u.d < v.d < u.f < v.f` (intervals "crossing" like an X) can never happen. Intervals are always either fully nested or fully disjoint.

`u.d < v.d < v.f < u.f` (v's interval nested inside u's) means **`v` is a descendant of `u`** in the DFS tree — `v` was discovered after `u` and finished before `u`, i.e., `v`'s entire exploration happened "inside" `u`'s.
</details>

---

**Q4.** You're running DFS on a **directed** graph. You're currently at vertex `u`, examining edge `(u, w)`. `w` is colored **Black**. What two sub-cases are possible, and how do you tell them apart?

<details><summary>Answer & Explanation</summary>

Since `w` is Black, `(u,w)` is definitely a non-tree edge and *not* a back edge (back edges require the target to be Gray). The two sub-cases:
- **Cross edge**: `w.f < u.d` — `w` finished entirely *before* `u` even started. Their intervals are disjoint, so `w` is unrelated to `u` (different subtree, or different DFS tree in the forest).
- **Forward edge**: `w.f ≥ u.d` (equivalently `w` is a proper descendant of `u` — `w.d > u.d`, nested inside `u`'s interval) — `w` was discovered and finished *while `u` was still active*, meaning `w` lies inside `u`'s own subtree; `(u,w)` is a "shortcut" edge from ancestor `u` straight down to descendant `w`, skipping intermediate tree edges.
</details>

---

**Q5.** Why does the presence of a **back edge** in directed-graph DFS signal a cycle, while a **forward edge** or **cross edge** does not?

<details><summary>Answer & Explanation</summary>

A back edge `(u,w)` exists exactly when `w` is Gray — i.e., `w` is an **ancestor** of `u`, still active on the current DFS path. That means there's already a directed tree-edge path `w → ... → u` (from the ancestor down to `u`), and the back edge `(u,w)` closes it into a cycle `w → ... → u → w`. Forward edges (`u` → its own descendant) don't create new cycles because the descendant path already existed via tree edges — a forward edge is redundant, not cycle-forming. Cross edges go between unrelated (already-finished or different-branch) vertices, with no path back from `w` to `u` implied, so no cycle is guaranteed either.
</details>

---

**Q6.** In the iterative (non-recursive) DFS, why is `Push((u, ptr.next), S)` executed **before** (i.e., underneath, in stack terms) pushing `(w, w.first)` for a newly discovered white neighbor `w`?

<details><summary>Answer & Explanation</summary>

Because after we finish exploring everything reachable from `w` (i.e., after `w`'s subtree is fully popped off the stack), control must logically "return" to `u` and continue with `u`'s *next* neighbor — exactly like a recursive call returning to the next iteration of the `for` loop. By pushing `(u, ptr.next)` first and then `(w, w.first)` on top, the stack naturally pops `w`'s work first (LIFO), and only once `w`'s entire exploration is exhausted does `(u, ptr.next)` resurface — correctly resuming `u`'s adjacency-list scan. This is precisely how the implicit call stack behaves in the recursive version.
</details>

---

**Q7.** What does `(u, ptr) = (u, NULL)` signify when popped in the iterative DFS, and what two actions does the algorithm take in response?

<details><summary>Answer & Explanation</summary>

It signifies that **every neighbor of `u` has already been examined** — there's nothing left in `u`'s adjacency list to explore. In response: (1) `u.color` is set to **Black** (visit completed), and (2) `Time` is incremented and recorded as `u.f` (finish time). This is the iterative equivalent of the recursive version's code *after* its `for each v in Adj(u)` loop ends.
</details>

---

**Q8.** State the graph-theoretic (mathematical) definition of a cut vertex, and explain precisely why it is *computationally infeasible* to use directly.

<details><summary>Answer & Explanation</summary>

`v` is a cut vertex iff there exist vertices `x, y` (both ≠ `v`) such that **every** path from `x` to `y` passes through `v`. It's infeasible directly because verifying it requires checking *all* pairs `(x,y)` and, for each pair, *all* possible paths between them — the number of simple paths in a graph can be exponential in the number of vertices, making brute-force checking intractable for anything but tiny graphs. This is exactly why we need the efficient `Low(u)`-based DFS algorithm instead.
</details>

---

**Q9.** Define `Low(u)` precisely. If `u` has children `v1, v2` in the DFS tree with `Low(v1) = 3` and `Low(v2) = 7`, and `u` itself has `u.d = 5`, plus a back edge from `u` to some ancestor with discovery time `2`, what is `u.Low`?

<details><summary>Answer & Explanation</summary>

`Low(u) = min( u.d, min over back edges (u,v) of v.d, min over children w of Low(w) )` — the smallest discovery-time reachable from `u` via zero or more tree edges followed by at most one back edge.

Here: `min(u.d=5, back-edge target d=2, Low(v1)=3, Low(v2)=7) = min(5, 2, 3, 7) = 2`. So **`u.Low = 2`**.
</details>

---

**Q10.** The root `r` of a DFS tree has exactly one child `c`, and `c` in turn has two children. Is `r` a cut vertex? Justify using the special-case rule for roots.

<details><summary>Answer & Explanation</summary>

**No**, `r` is *not* necessarily a cut vertex from this information alone — the rule is: the root is a cut vertex **iff it has more than one child** in the DFS tree. Here `r` has only **one** child (`c`), so removing `r` cannot disconnect the tree at the root level — everything still hangs together through `c` (whatever happens deeper in `c`'s subtree, like `c`'s own two children, is a separate question about whether `c` is a cut vertex, evaluated via the non-root rule `Low(child) ≥ c.d`, not the root rule). So based on the *root* condition alone, `r` is not a cut vertex.
</details>

---

**Q11.** Non-root vertex `u` has a child `v` in the DFS tree. What exact inequality involving `Low(v)` and `u.d` identifies `u` as a cut vertex, and give the intuitive (not just formulaic) reason why.

<details><summary>Answer & Explanation</summary>

**`Low(v) ≥ u.d`** identifies `u` as a cut vertex.

Intuition: `Low(v)` is the earliest-discovered vertex reachable from `v`'s subtree using tree edges plus one trailing back edge. If `Low(v) ≥ u.d`, that means no vertex in `v`'s subtree can "escape" upward past `u` — there's no back edge from anywhere in `v`'s subtree that reaches an ancestor of `u`. So `v`'s entire subtree's *only* connection to the rest of the graph is through `u` itself. Remove `u`, and `v`'s subtree is cut off — proving `u` is a cut vertex. If instead `Low(v) < u.d`, some vertex in `v`'s subtree has a back edge reaching above `u`, giving an alternate route around `u`, so `u`'s removal wouldn't disconnect that subtree.
</details>

---

**Q12.** In the `Low`-computation pseudocode, two "control points" update `u.Low`. What are they, and why must the back-edge control point explicitly **exclude** the edge back to `u`'s own immediate parent (`v ≠ u.π`)?

<details><summary>Answer & Explanation</summary>

**Control point ①** (after a child recursion returns): `u.Low = min(u.Low, v.Low)` — propagate the child's reachability upward.
**Control point ②** (on encountering a back edge `(u,v)`, `v` already visited): `u.Low = min(u.Low, v.d)`.

The exclusion `v ≠ u.π` matters because in an **undirected** graph, the tree edge from `u`'s parent to `u` also appears as an edge from `u` back to its parent in `u`'s own adjacency list (undirected edges are stored both ways). Without excluding it, this *same tree edge, viewed from the other direction*, would be misidentified as a "back edge" from `u` straight to its own parent — artificially making `u.Low` collapse to `u.π.d` (or lower) every single time, which would make it look like *every* vertex can escape past its parent trivially, breaking the whole cut-vertex test. Excluding the immediate parent ensures only *genuine* back edges (to a proper ancestor further up, not the direct parent link re-traversed) count.
</details>

---
