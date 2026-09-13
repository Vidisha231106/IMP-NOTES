# Minimum Spanning Trees — Complete Advanced Study Notes
### (Prim's Algorithm • Kruskal's Algorithm • Union-Find Data Structures)
*Based on C. Pandu Rangan's NPTEL lectures (W3U1, W3U2)*

---

## TABLE OF CONTENTS
1. Graph Theory Refresher (Connectivity, Trees, Forests)
2. Minimum Spanning Trees — Definitions & Properties
3. The Cut Property — The "Theorem" Behind Every MST Algorithm
4. The Generic Template for MST Algorithms
5. Prim's Algorithm — Full Design & Development
6. Kruskal's Algorithm — Design, Example, Two-Part Proof
7. Implementing Kruskal's — Union-Find / Partition ADT
8. Name-Array Representation
9. Inverted Forest (Parent-Pointer Tree) Representation
10. Weighted-Union Optimization & Complexity Proof
11. Complexity Analysis of Kruskal's Algorithm (both implementations)
12. Prim's vs Kruskal's — When to Use What
13. Quick-Reference Cheat Sheet
14. QUIZ — 15 Questions with Full Explanations

---

## 1. GRAPH THEORY REFRESHER

### 1.1 Connectivity
- Two vertices **u, v** are **connected** if there exists a path from u to v in G.
- **Connected graph**: every pair of vertices is connected.
- **Disconnected graph**: not connected — it splits into maximal connected subgraphs called **connected components**.
- A connected graph is just a special (degenerate) case: it is "made of one connected component," which is the whole graph.

### 1.2 The Equivalence-Relation View of Connectivity (important — used to justify Union-Find)
Define a relation **R** on vertex set V:
> u R v ⟺ there is a path from u to v. Also define u R u ∀u ∈ V.

For an **undirected** graph, R is:
- **Reflexive** (uRu),
- **Symmetric** (uRv ⟹ vRu, since edges are undirected),
- **Transitive** (uRv, vRw ⟹ uRw, by concatenating paths).

⟹ R is an **equivalence relation** ⟹ (fundamental theorem of equivalence relations) R **partitions** V into disjoint equivalence classes.

**Each equivalence class = one connected component of G.**
Each component is *maximal* w.r.t. connectivity: no vertex outside it is reachable from inside it, and every vertex inside it is reachable from every other vertex inside it.

> **Why this matters:** This is the deep reason Kruskal's "does this edge form a cycle?" question can be answered using a **Partition ADT** — connected components ARE a partition, and adding an edge merely **merges two parts of the partition** (a Union operation).

### 1.3 Acyclic Graphs, Trees, Forests
- **Acyclic graph**: no cycles.
- **Tree**: connected + acyclic.
- A general acyclic graph may be disconnected; each of its connected components is itself connected + acyclic ⟹ each component is a tree.
- Hence a generic acyclic graph = a collection of trees = a **Forest**.

---

## 2. MINIMUM SPANNING TREES — DEFINITIONS & PROPERTIES

Let G = (V, E, w) be a **connected, undirected, edge-weighted** graph with n = |V| vertices and m = |E| edges. Assume (for simplicity of proofs) all edge weights are **distinct** — this guarantees a **unique** MST and lets exchange-argument proofs use strict inequalities.

### 2.1 Spanning Tree
A **spanning tree** T = (V, A) of G is a subgraph that:
- contains **all** vertices of G,
- is a **tree** (connected + acyclic),
- hence contains exactly **(n − 1)** edges (A ⊆ E).

### 2.2 Cost of a Spanning Tree
w(T) = Σ w(e) over all e ∈ A (sum of the weights of its n−1 edges).

### 2.3 Key Structural Properties of (Spanning) Trees
1. A tree on n vertices has **exactly n − 1 edges**.
2. Between **any two vertices** there is a **unique** simple path.
3. Adding **any** non-tree edge to a spanning tree creates **exactly one cycle**.
4. Removing **any** edge from a spanning tree splits it into **exactly two** components (a "cut").
5. A spanning tree is a **minimal connected spanning subgraph** — remove any edge and it disconnects; it is also a **maximal acyclic spanning subgraph** — add any edge and a cycle forms.

### 2.4 Minimum Spanning Tree (MST)
Among **all** possible spanning trees of G, the MST is the one with **minimum total cost** w(T).
- If all edge weights are distinct, the MST is **unique**.
- MST problems only make sense on **connected** graphs (else use a **Minimum Spanning Forest**, one MST per component).

---

## 3. THE CUT PROPERTY — THE CENTRAL THEOREM

This is the theoretical engine that both Prim's and Kruskal's algorithms exploit (even though the slides derive Kruskal's correctness independently via an exchange argument, the intuition is identical).

### 3.1 Definitions
- A **cut** of G is a partition of V into two non-empty, disjoint sets **(S, V − S)**.
- An edge (u, v) is a **crossing edge** (w.r.t. the cut) if exactly one endpoint is in S and the other in V − S.
- An edge is **internal** to the cut if both endpoints lie in the same side.
- A cut (S, V−S) **respects** a set of edges A if **no edge of A is a crossing edge** (i.e., all edges of A are internal to one side or the other).
- A crossing edge of **minimum weight** is called a **safe edge** for that cut.

### 3.2 Cut Property (Theorem)
> **Theorem:** Let A be a subset of some MST's edge set (A is "safe so far"). Let (S, V−S) be *any* cut that respects A. Let (u,v) be a **minimum-weight crossing edge** of that cut. Then A ∪ {(u,v)} is also safe (i.e., is a subset of the edges of *some* MST).

**Intuition of proof (exchange argument):** Suppose the true MST T does not contain (u,v). Adding (u,v) to T creates a cycle; that cycle must cross the cut at least twice (once via (u,v), once via some other edge (x,y)). Since (u,v) is a minimum-weight crossing edge, w(u,v) ≤ w(x,y). Swap (x,y) out and (u,v) in: you get another spanning tree with cost ≤ w(T). With distinct weights, this forces w(u,v) < w(x,y), producing a **strictly cheaper** tree unless (u,v) was already in T — contradiction. This exact exchange logic reappears, spelled out in full rigor, in the **Kruskal correctness proof** (Section 6.4).

### 3.3 Why This Matters
Both Prim's and Kruskal's are instances of a **single greedy meta-strategy**: repeatedly find a cut respecting the current edge-set A, and add its minimum-weight crossing edge. They differ only in **which cut** they use at each step.

---

## 4. GENERIC TEMPLATE FOR MST ALGORITHMS

```
GENERIC-MST(G):
    A = ∅
    while A does not form a spanning tree:
        find a cut (S, V-S) that respects A
        find a safe edge e (minimum-weight crossing edge of that cut)
        A = A ∪ {e}
    return A
```

- **Prim's Algorithm** — always uses **one specific, growing cut**: S = {vertices already in the tree}. It grows S by exactly one vertex per iteration.
- **Kruskal's Algorithm** — does **not** track one single cut explicitly. Instead, at each step, it processes the **globally next-cheapest edge** in the whole graph and checks (via connectivity) whether adding it is safe (i.e., whether it crosses **some** respecting cut — equivalently, whether its endpoints are currently in different components of the forest built so far).

---

## 5. PRIM'S ALGORITHM — FULL DESIGN & DEVELOPMENT

### 5.1 Starting the Recursion (Base Case)
When A = ∅, the **simplest possible cut** is:
> S = {r} for an arbitrary root vertex r, and V − S = the rest.

The minimum-weight crossing edge is (r, v) such that w(r,v) ≤ w(r,x) for all x ∈ Adj(r) ∩ (V−S).

We update: A = A ∪ {(r,v)} = {(r,v)}.

**Question:** How do we update the cut to keep it "respecting A"?
**Naive idea:** just move v from V−S into S.

### 5.2 The Subtlety — Naive Vertex-Moving Can Break the Cut!
Suppose current minimum crossing edge is (x, y), and A already contains some edge (y, z) (with y ∈ V−S, z ∈ V−S too, at that moment).
- If we move y into S, the edge (y, z) — which is in A — now becomes a **crossing edge** of the *new* cut!
- This violates the requirement that the cut must **respect A** (no edge of A may cross the cut).
- **So blindly moving the endpoint of the minimum crossing edge does NOT always work.**

### 5.3 The Fix — Invariant: All Edges of A Stay Internal to S
**Key design decision:** We deliberately maintain the invariant that **every edge in A is internal to S**, and **no edge of A is ever in V−S** (trivially true, since V−S is currently just "loose" vertices not yet touched by any A-edge on both ends... more precisely: we build S vertex-by-vertex and only ever add crossing edges that connect the *new* vertex directly to S, so every edge added to A has one endpoint already in S — meaning as soon as its other endpoint joins S, the edge becomes internal, and it can never "revert" to crossing).

With this invariant:
- Moving **any** vertex v from V−S to S is now always safe: it can only convert **crossing edges into internal edges** or **turn non-A crossing edges into new crossing edges belonging to no set A** — it never turns an A-edge into a crossing edge, because A-edges were never in V−S to begin with.
- Diagrammatically: solid edges = edges in A (internal to S); dotted edges = potential future crossing edges (not in A). Moving y from V−S into S makes (x,y) internal and creates new dotted (non-A) crossing edges to y's other neighbors (z₁, z₂, z₃) — **but these new crossing edges are NOT in A**, so the cut still respects A.

### 5.4 Naive Complexity
- Finding the min-weight crossing edge naively = scan **all** crossing edges = O(m) per iteration.
- We perform (n−1) iterations (to add n−1 edges).
- **Naive complexity = O(nm).**

### 5.5 The Optimization — Track a "Partner" and "Cost" per Vertex
For **every vertex v ∈ V−S**, maintain:
- **p(v)** = the "partner" of v in S: the endpoint in S of the minimum-weight edge currently connecting v to S.
- **c(v)** = w(v, p(v)) — the weight of that minimum crossing edge for v.

**Claim:** If v ∈ V−S has c(v) ≤ c(u) for all u ∈ V−S, then (v, p(v)) IS the overall minimum-weight crossing edge.
⟹ We only need to scan **|V−S| ≤ (n−1)** values of c(·), NOT all m edges!

### 5.6 Update Rule After Moving v into S
After adding v to S (and (v, p(v)) to A), we must update p(u), c(u) for remaining u ∈ V−S:
> **p(u) needs updating ⟺ (v,u) ∈ E AND w(v,u) < w(u, p(u)).**
> If so: set p(u) = v and c(u) = w(v,u).

(This is exactly one "relaxation" step per neighbor of v — same idea as Dijkstra's relaxation.)

### 5.7 High-Level Pseudocode
```
A = ∅, S = {r}, V-S = V - {r}
For all v ∈ V: p(v) = NULL, c(v) = ∞
For each u ∈ V-S: if (r,u) ∈ E then p(u)=r, c(u)=w(r,u)

While S ≠ V:
    find v ∈ V-S with minimum c(v)
    move v to S,  add (v, p(v)) to A
    update c(u), p(u) for all remaining u ∈ V-S
```

### 5.8 Detailed Pseudocode
```
Prim(G = (V,E,w)):
    // r ∈ V arbitrary; T=(V,A), A represented implicitly by p()
    // A = { (v, p(v)) | v ∈ V - {r} }
    S = {r}
    p(v) = NULL  ∀v ∈ V
    c(v) = ∞     ∀v ∈ V
    For all u ∈ V - S:
        if (u,r) ∈ E:  p(u) = r;  c(u) = w(u,r)

    while (V - S) is non-empty:
        1) Find v ∈ V-S such that c(v) ≤ c(u) ∀u ∈ V-S
        2) Move v to S
        3) For each u ∈ V-S:
              if (u,v) ∈ E and w(u,v) < c(u):
                  c(u) = w(u,v);  p(u) = v
```

### 5.9 Complexity (Standard Extension Beyond Slides — Good to Know)
| Implementation of "find min c(v)" | Total Complexity |
|---|---|
| Linear scan (as above) | O(n²) — good for **dense** graphs |
| Binary heap + decrease-key | O((n+m) log n) |
| Fibonacci heap | O(m + n log n) — theoretically optimal |

---

## 6. KRUSKAL'S ALGORITHM

### 6.1 Motivating Idea
Instead of growing ONE connected cluster (Prim's approach), Kruskal's builds a **forest** greedily by always trying to add the **globally cheapest remaining edge**, from anywhere in the graph.

**Design principle:** "Construct a spanning tree with as many cheap edges as possible."
1. Build **L** = list of all edges sorted by increasing weight — O(m log m).
2. Process edges of L **in order**; for each edge, **decide**: include it in T, or reject it.
3. T = growing set of edges A; hope: at the end A is the MST.

### 6.2 The Rejection Rule (Cycle Test)
> If edge e ∈ L would form a **cycle** with the edges already accepted into A, **reject** e (T must stay acyclic — it's meant to be a tree/forest). Otherwise, **accept**: A = A ∪ {e}.

### 6.3 High-Level Pseudocode
```
// G=(V,E) connected, weighted, undirected. w(e) = weight of e.
// L = list of edges of E sorted by increasing weight.
// T = (V,A) is the output; A = set of accepted edges.

1) A = ∅;  e = first edge of L
2) while (NOT end of L):
     2.1) if (e does not form a cycle with any subset of edges in A):
              A = A ∪ {e}
     2.2) e = Next(e, L)
```
**Important caveat directly noted in lecture:** At this stage it is *not yet obvious* that A forms a tree at all, let alone a *minimum* spanning tree. Both facts require proof (Section 6.4–6.5).

### 6.4 Worked Example
Graph G with vertices {a,b,c,d,e,f}, edges & weights:
(a,b)=5, (a,c)=6, (b,c)=7, (d,f)=3, (b,d)=12, (b,e)=9, (c,e)=10, (c,d)=15, (e,f)=20 (plus a couple duplicated labels d,c in slide are effectively the same b,d/c,d edges).

**Sorted list L:** (d,f)=3, (a,b)=5, (a,c)=6, (b,c)=7, (b,e)=9, (c,e)=10, (b,d)=12, (c,d)=15, (e,f)=20

Walking through:
| Step | Edge considered | Decision | Reason |
|---|---|---|---|
| 1 | (d,f) | **Accept** | no cycle |
| 2 | (a,b) | **Accept** | no cycle |
| 3 | (a,c) | **Accept** | no cycle |
| 4 | (b,c) | **Reject** | forms cycle with (a,b),(a,c) |
| 5 | (b,e) | **Accept** | no cycle |
| 6 | (c,e) | **Reject** | forms cycle with (c,a),(a,b),(b,e) |
| 7 | (b,d) | **Accept** | no cycle |
| 8+ | remaining | **Reject** | all remaining would form cycles (tree already spans & has n−1 edges) |

**Final A** = {(d,f), (a,b), (a,c), (b,e), (b,d)} — this is a valid spanning tree (5 edges for 6 vertices ✓).
**Cost = 3 + 5 + 6 + 9 + 12 = 35** *(note: slide arithmetic shows 3+5+7+9+12=36 using a slightly different edge set from a different weight labeling — the takeaway is the mechanical process, not the specific numeric example, since two documents use slightly different weight assignments for the same figure).*

### 6.5 PROOF PART 1 — A Forms a Spanning Tree
**Claim:** The graph T = (V, A) induced by Kruskal's output is acyclic AND connected (hence a tree).

- **Acyclic:** Trivial — the rejection rule explicitly forbids adding any edge that would create a cycle.
- **Connected (proof by contradiction):**
  Suppose A induces a **disconnected** graph. Then there exist two connected components C₁, C₂ of A. Since the *original graph G* is connected, there must exist some path **P** in G connecting a vertex in C₁ to a vertex in C₂.
  - Let **e** be the **first edge along P that is NOT in A**.
  - This edge e connects a vertex already "reachable-in-A" (via the portion of P already inside A) to a vertex outside that component.
  - Therefore, e **cannot form a cycle** with any subset of edges already in A (since e straddles two different components — no cycle is possible).
  - But then, when the algorithm examined e (during its single pass through sorted list L), the algorithm's own rejection rule would have **accepted** e into A!
  - This **contradicts** the assumption that e ∉ A.
  - ⟹ No such disconnection can exist ⟹ **A induces a connected graph**.

∴ A is connected + acyclic ⟹ **A is a tree**, and it spans all of V ⟹ A contains exactly **(n−1)** edges.

### 6.6 PROOF PART 2 — A Is Indeed an MST (Exchange Argument)
Let x₁, x₂, …, x_{n−1} be the edges of A **in increasing order of weight** (this is exactly the order the algorithm accepted them, since it scans L in sorted order and skips rejects — the *accepted* subsequence is still sorted).

**Proof by contradiction.** Suppose T = (V,A) is **not** the MST. Let T′ be an actual MST with w(T′) < w(T). Let y₁, y₂, …, y_{n−1} be the edges of T′ in increasing order of weight.

**Step 1 — Find the first point of divergence.**
Let j be the smallest index such that x_j ≠ y_j (but x_i = y_i for all i < j). Such a j must exist since w(T′) < w(T) means the two sorted sequences cannot be identical throughout.

**Step 2 — Kruskal's greediness gives us an inequality.**
Since x_j was chosen greedily by the algorithm (it's the cheapest edge at that point that didn't create a cycle) and y_j is some alternative edge sitting at the same rank in T′'s sorted list:
> **w(x_j) < w(y_j)**  ... (this holds because if w(y_j) ≤ w(x_j), the algorithm — being greedy over the globally sorted edge list — would have encountered and *not rejected* an edge at least as cheap as y_j at or before this point, unless doing so created a cycle; the careful cycle-tracing argument below pins this down rigorously.)

**Step 3 — Constructing a contradiction via a cycle exchange.**
Add edge x_j to T′. Since T′ is a spanning tree, this creates **exactly one cycle** C. This cycle consists of x_j plus the unique path in T′ between x_j's endpoints — call this path's edges ⟨y_{i₁}, y_{i₂}, …, y_{i_k}⟩.

- **Case A:** If this entire path's edges ⊆ {y₁, …, y_{j−1}} = {x₁, …, x_{j−1}} (using Step 1's equality for indices < j), then x_j together with these x-edges would form a cycle **within A itself** — but A is a tree (acyclic, from Proof Part 1)! **Contradiction.** So this case is impossible.
- **Case B (the real case):** The path must therefore contain **at least one edge y_t with t ≥ j** (i.e., an edge from T′ not yet "matched" to an x-edge). Since T′'s edges are sorted and t ≥ j:
  > w(y_t) ≥ w(y_j) > w(x_j)  ...(1)
  (using Step 2's inequality w(x_j) < w(y_j)).

**Step 4 — Build a cheaper spanning tree T″.**
Define: **T″ = T′ + x_j − y_t** (add x_j, remove y_t).
- T″ is still a **spanning tree**: adding x_j created cycle C, and removing y_t (which lies on C) breaks that cycle, restoring an acyclic connected structure spanning all vertices.
- Cost comparison:
  > w(T″) = w(T′) + w(x_j) − w(y_t) < w(T′)   [using inequality (1)]

**Step 5 — The Contradiction.**
This means T″ is a spanning tree **strictly cheaper than T′** — but T′ was assumed to be a **minimum** spanning tree! This is a direct contradiction.

∴ No spanning tree cheaper than Kruskal's output T can exist ⟹ **T is a Minimum Spanning Tree.** ∎

> **Big-picture takeaway:** This proof is the *rigorous version* of the Cut Property intuition from Section 3.2 — Kruskal's greedy choice at each step can always be "defended" by an exchange that never increases (and here, strictly decreases) the cost of any competing tree, so greed cannot lose.

---

## 7. IMPLEMENTING KRUSKAL'S — UNION-FIND / PARTITION ADT

### 7.1 The Core Implementation Challenge
The pseudocode says: *"if e does NOT form a cycle with any subset of edges in A."*
This sounds expensive to check directly (naive: search for a path in the current partial graph — could be exponential-time thinking if not careful, or at best O(V+E) BFS/DFS per edge, giving O(m(V+E)) overall — very slow).

### 7.2 Key Reduction: Cycle Check ⟹ Connectivity Check
**Observation:** An edge e = (x,y) forms a cycle with (some subset of) A **if and only if** x and y are **already connected** by a path P consisting entirely of edges from A.

⟹ Reformulated question: *"Are x and y in the same connected component of T=(V,A)?"*

This connects directly back to **Section 1.2**: connected components of T = (V,A) form a **partition** of V (via the equivalence relation R). So we need an efficient ADT for:
- **Find(x)** → returns the *name/identifier* of the component (partition-block) containing x.
- **Union(X, Y)** → merges two named components into one.

### 7.3 Kruskal's Algorithm Rewritten Using Find/Union
```
Let e = (x, y)
X = Find(x);  Y = Find(y)
If (X ≠ Y):        // different components ⟹ adding e is safe, no cycle
    Add e to A
    Union(X, Y)     // merge the two components
Else:
    'ignore e'      // same component ⟹ e would create a cycle
```

**Observation about component count:** Each successful Union merges two components into one ⟹ the **number of connected components decreases by exactly 1** every time an edge is accepted. (Starts at n singleton components, ends at 1 component after n−1 accepted edges — consistent with a spanning tree!)

### 7.4 Full Pseudocode (Kruskal + Union-Find)
```
// A = edges collected. T = (V, A).
// Find(x) → name of component of T containing x.
// Union(X,Y) → merges components X, Y into one; X, Y are removed, new name returned.
// L = edges of E sorted by increasing weight.

Initialize:
    A = ∅
    e = first edge of L
    Initialize names of connected components of T = (V, A)   // n singletons

Process:
    While (NOT end of L):
        Let e = (x, y)
        X = Find(x);  Y = Find(y)
        If (X ≠ Y):
            Add e to A
            Union(X, Y)
        e = Next(e, L)

Output: T = (V, A)   // T is an MST of G
```

### 7.5 Partition Abstract Data Type (ADT) — Formal Definition
Let V = {1, 2, 3, …, n}.
- A **k-partition** of V is a collection of subsets S₁, S₂, …, S_k of V such that:
  - S_i ∩ S_j = ∅ for i ≠ j (disjoint), and
  - ⋃ᵢ Sᵢ = V (covering).
- The **n-partition** = n singletons: {1}, {2}, …, {n} (finest partition).
- The **1-partition** = the whole set V (coarsest partition — one giant block).
- **Identifier/Name of a set:** Any element of the set can serve as its name, since every element of V belongs to **exactly one** block. Convention used here: **the minimum element of a set is used as its name.**

---

## 8. NAME-ARRAY REPRESENTATION (First Implementation of Partition ADT)

### 8.1 Idea
Maintain an array `Name[1..n]` where `Name[i]` = the identifier (name) of the block currently containing element i.

### 8.2 Worked Example
Partition: {1,2,4,7}, {3,8,10}, {5,6,9,11}, {12,14}, {13}
Names (min element of each block): **1, 3, 5, 12, 13**

| i | Name[i] | i | Name[i] |
|---|---|---|---|
|1|1|8|3|
|2|1|9|5|
|3|3|10|3|
|4|1|11|5|
|5|5|12|12|
|6|3|13|13|
|7|1|14|12|

### 8.3 Operations
```
Find(x):        // x ∈ V
    return Name[x]

Union(A, B):    // A, B are names of two sets in current partition
                // assume WLOG A < B
    for i = 1 to n:
        if Name[i] == B:
            Name[i] = A     // every element of B moves into set A
```

### 8.4 Complexity
- **Find(x): O(1)** — direct array lookup.
- **Union(A,B): O(n)** — must scan the ENTIRE array to relabel all elements of B.

⟹ Fast lookups, but **expensive merges**. This is the crux of why an alternative (tree-based) representation is desirable when many unions are expected.

---

## 9. INVERTED FOREST (PARENT-POINTER TREE) REPRESENTATION

### 9.1 Motivation
Name-array gives O(1) Find but O(n) Union. We want to flip the cost balance (or at least make Union cheap), accepting a possibly higher Find cost that we can later bound tightly.

### 9.2 Definition — In-Tree
An **in-tree** is a **directed rooted tree** where **every edge points "towards" the root** (child → parent direction, opposite of the usual "rooted tree drawn top-down with edges pointing to children"). Crucially:
> **The root has a self-loop**: p(root) = root.

A **k-partition** is represented by an **in-forest** with **k in-trees**, one tree per block of the partition. The **shape of each tree is arbitrary** (not necessarily balanced!). Each node stores exactly one element of its block; the **root's value serves as the name** of that block.

### 9.3 Worked Example
4-partition of {1,…,14}: {1,2,4,7}, {3,6,8}, {5,9,13}, {10,11,14,12}

Parent-array `p()` representation (self-loop at root ⟹ p(root)=root):
```
p(1)=2   p(5)=9    p(9)=13   p(13)=13
p(2)=2   p(6)=3    p(10)=11  p(14)=10
p(3)=3   p(7)=2    p(11)=11
p(4)=7   p(8)=3    p(12)=10
```
(Roots: 2, 3, 13, 11 — each with self-loop, i.e. p(2)=2, p(3)=3, p(13)=13, p(11)=11.)

**Union by "Tree Hooking":** `Union(A, B)` — where A, B are the root values (names) of two different trees — simply makes one root point to the other: e.g. `Union(13, 11)` sets p(13) = 11 (the tree rooted at 13 is "hooked" underneath the tree rooted at 11). **This is an O(1) operation** — just one pointer update!

### 9.4 Find via "Ancestor Chasing"
```
Find(x):
    // returns the name of the set containing x
    // i.e., returns the value at the root of the tree containing x
    y = p(x)
    while (y ≠ p(y)):     // p(root) == root is the stopping condition (self-loop)
        y = p(y)
    return y
```
- Note: `p(x) = x` **if and only if** x is the root (because of the mandatory self-loop convention).
- This is literally walking up parent-pointers until you hit a self-loop — hence "ancestor chasing."

### 9.5 Complexity — Naive In-Forest
- **Union: O(1)** — just re-point one root.
- **Find: O(depth of x)** — proportional to how far x is from its tree's root.
- **Worst case: O(n)** — if repeated naive unions always hook the "new" tree under a growing chain, you get a **degenerate, path-like (skewed) tree** of depth n−1, making Find as slow as O(n) in the worst case! (This is exactly analogous to why naive linked-list-style unions are bad — you must be smart about *which* root points to which.)

---

## 10. WEIGHTED-UNION OPTIMIZATION (Union by Size)

### 10.1 The Fix — Always Hook the Smaller Tree Under the Larger Tree
Maintain an auxiliary array `size(x)` for every current **root** x = number of nodes in that tree.

**Initialization** (for the initial n-partition {1},{2},…,{n}):
```
p(i) = i        ∀ i, 1 ≤ i ≤ n     (n singleton self-loop trees)
size(i) = 1     ∀ i, 1 ≤ i ≤ n
```

### 10.2 Weighted-Union Algorithm
```
Union(A, B):                          // A, B are roots (names)
    if size(A) ≤ size(B):
        p(A) = B
        size(B) = size(B) + size(A)   // hook smaller (A) under larger (B)
    else:
        p(B) = A
        size(A) = size(A) + size(B)   // hook smaller (B) under larger (A)
```
(Still **O(1)** per Union — same as before, just now with a size comparison and update.)

### 10.3 Complexity Proof for Find — The "Doubling" Argument (Crucial!)
**Claim:** With weighted union, Find(x) = **O(log n)** for any x, in the worst case.

**Proof sketch:**
1. Initially, `depth(x) = 0` for every x (each x is its own root).
2. **Key Lemma:** Every time the depth of a fixed element x *increases by exactly 1* (i.e., x's tree gets hooked underneath another tree during some Union), the **size of the set containing x at LEAST DOUBLES.**
   - *Why?* x's depth only increases when x's *current* tree (say of size s, rooted at A) is the **smaller** one being hooked under another tree (say of size s′ ≥ s, rooted at B). By the weighted-union rule, this only happens when size(A) ≤ size(B), i.e., **s ≤ s′**. After the union, the resulting tree has size s + s′ ≥ s + s = 2s. So the size of x's component at least doubles.
3. Since the total number of vertices is n, the size of any set can double **at most log₂(n) times** before it reaches n (the maximum possible size).
4. Therefore, x's depth can increase **at most log n times** ⟹ **max depth of any element ≤ O(log n)**.
5. Since x was arbitrary, **Find(x) = O(log n)** for ALL x — this is a **worst-case guarantee**, not just amortized!

### 10.4 Summary Complexity Table (Union-Find variants)
| Representation | Union | Find |
|---|---|---|
| Name Array | O(n) | O(1) |
| Naive In-Forest | O(1) | O(n) worst case |
| **Weighted-Union In-Forest** | **O(1)** | **O(log n)** worst case |

> **Note (advanced, beyond slide scope but worth knowing):** Adding **Path Compression** (flattening the tree during every Find by re-pointing visited nodes directly to the root) combined with weighted union gives the famous **O(α(n))** amortized bound, where α is the inverse-Ackermann function — effectively constant for any n conceivable in practice. The slides only cover weighted-union (no path compression), giving the clean O(log n) worst-case bound proven above.

---

## 11. COMPLEXITY ANALYSIS OF KRUSKAL'S ALGORITHM (Full Picture)

### 11.1 Fixed Costs
- Sorting the edge list L: **O(m log m) = O(m log n)** (since m ≤ n², log m = O(log n)).
- This sorting cost is typically treated as a **pre-processing cost independent of the Union-Find data structure choice**, and is often excluded when *comparing* data structures (though it's part of total runtime).

### 11.2 Union-Find Operation Counts During Kruskal's Main Loop
- **(n − 1) Union operations** (exactly one per accepted edge — since the final tree has n−1 edges).
- **2m Find operations** (two Find calls — one per endpoint — for *every* edge examined, accepted or rejected; there are m edges total).

### 11.3 Total Complexity — Name-Array Implementation
> Union cost: O(n) each × (n−1) unions = O(n(n−1)) = **O(n²)**
> Find cost: O(1) each × 2m finds = **O(m)**
> **Total: O(n² + m)**

### 11.4 Total Complexity — Weighted-Union Parent-Array (Inverted Forest) Implementation
> Union cost: O(1) each × (n−1) unions = **O(n)**
> Find cost: O(log n) each × 2m finds = **O(m log n)**
> **Total: O(n + m log n)**

### 11.5 Which Is Better — Dense vs Sparse Graphs
| Implementation | Complexity | Best for |
|---|---|---|
| Name-Array | O(n² + m) | **Dense graphs** (m close to n²) — the n² term stops mattering relatively, and O(1) Find pays off given many Find calls (2m of them) |
| Parent-Array (weighted union) | O(n + m log n) | **Sparse graphs** (m close to n) — avoids paying the n² penalty when m is small |

**Why:** The n² term dominates for name-arrays regardless of m, while the m log n term scales gracefully with m. For sparse graphs (e.g., m = O(n)), parent-array gives O(n log n), far better than O(n²). For dense graphs (m = O(n²)), name array gives O(n²+m)=O(n²) while parent array gives O(n² log n) — plain array wins.

---

## 12. PRIM'S vs KRUSKAL'S — QUICK COMPARISON

| Aspect | Prim's | Kruskal's |
|---|---|---|
| Core strategy | Grows ONE tree from a starting vertex, always adding cheapest edge crossing the current tree's boundary | Globally sorts ALL edges, greedily adds cheapest edge that doesn't form a cycle, possibly growing multiple tree fragments simultaneously that later merge |
| Underlying cut used | A single evolving cut (S, V−S) | Implicit — connectivity via Union-Find substitutes for explicit cut tracking |
| Data structure needed | Priority queue / array for c(v), p(v) | Sorted edge list + Union-Find (Partition ADT) |
| Naive complexity | O(nm) | O(m log n) [sort] + O(n² or m log n) [union-find] |
| Best for | Dense graphs (with array-based c(v)) | Sparse graphs (with weighted-union forest) |
| Produces intermediate structures | Always ONE connected tree at every step | A **forest** (multiple disconnected tree fragments) until the final step |

---

## 13. QUICK-REFERENCE CHEAT SHEET

- **Spanning tree**: connected, acyclic, spans all n vertices, has n−1 edges.
- **Cut**: partition (S, V−S); **crossing edge**: one endpoint in each side; **respects A**: no A-edge crosses.
- **Cut Property**: min-weight crossing edge of any A-respecting cut is always safe to add.
- **Prim's invariant**: keep ALL of A's edges strictly internal to S — never crossing — so that moving any vertex into S is always safe.
- **Prim's per-vertex trackers**: p(v) = best neighbor in S; c(v) = weight to that neighbor.
- **Kruskal's core check**: "does e connect two different components?" (Find(x) ≠ Find(y)?)
- **Kruskal's correctness proof, two parts**: (1) A is connected+acyclic ⟹ tree [contradiction via "first edge of P not in A"]; (2) A is minimum [contradiction via exchange x_j ↔ y_t, T″ = T′+x_j−y_t].
- **Partition ADT**: k-partition, disjoint + covering; identifier = any representative element (convention: minimum element).
- **Name array**: Find O(1), Union O(n).
- **In-forest (parent pointer)**: self-loop at root, Find = ancestor chasing = O(depth).
- **Weighted union**: hook smaller under bigger by size(); guarantees O(log n) Find via the "doubling" argument (size at least doubles every time depth +1).
- **Kruskal total**: name-array → O(n²+m) [dense-graph friendly]; weighted-union forest → O(n + m log n) [sparse-graph friendly].

---

## 14. QUIZ — 15 QUESTIONS WITH FULL EXPLANATIONS

---

**Q1.** A spanning tree of a connected graph with n vertices always has exactly how many edges?
**A)** n
**B)** n − 1
**C)** n + 1
**D)** Depends on the graph

> **Answer: B) n − 1.**
> **Explanation:** A tree is defined as connected + acyclic. A fundamental graph-theory fact is that any tree on n vertices has exactly n−1 edges — one fewer edge than vertices, since adding any (n)th edge to a tree of n-1 edges would necessarily create a cycle (as trees are maximal acyclic subgraphs), while removing any edge disconnects it (trees are minimal connected subgraphs).

---

**Q2.** In the "cut" framework, what does it mean for a cut (S, V−S) to "respect" an edge set A?
**A)** Every edge of A is a crossing edge of the cut
**B)** No edge of A is a crossing edge of the cut (all A-edges are internal)
**C)** A contains exactly one crossing edge
**D)** The cut has equal numbers of vertices on both sides

> **Answer: B.**
> **Explanation:** A cut "respects" A precisely when none of A's edges cross the cut — i.e., every edge already chosen for the tree stays entirely on one side or the other. This is the exact condition Prim's algorithm is careful to maintain throughout its execution (Section 5.3), because it's what allows any vertex to be safely moved from V−S into S without accidentally turning an already-accepted edge into a new crossing edge.

---

**Q3.** In Prim's algorithm development, why can't we simply move vertex `v` (the far endpoint of the current minimum crossing edge) into `S` in general?
**A)** Because v might not exist in the graph
**B)** Because doing so might convert some OTHER edge already in A into a new crossing edge, breaking the "respects A" property
**C)** Because v is always the root
**D)** Because the minimum crossing edge is never unique

> **Answer: B.**
> **Explanation:** As shown with the example of edges (x,y) as the current minimum crossing edge, and (y,z) already in A: if we move y into S, the previously-internal-to-(V−S) edge (y,z) suddenly becomes a crossing edge of the NEW cut, since z remains in V−S. This violates "respects A." The fix is the invariant that all A-edges must be forced to be internal to S at all times, which Prim's algorithm's construction (only ever adding an edge that connects the growing S to a brand-new vertex) automatically guarantees.

---

**Q4.** What do p(v) and c(v) represent in the optimized Prim's algorithm pseudocode?
**A)** p(v) is the position of v in a priority queue; c(v) is its color
**B)** p(v) is v's "partner" (nearest neighbor already in S); c(v) is the weight of that connecting edge
**C)** p(v) is the parent in the DFS tree; c(v) is the number of children
**D)** p(v) is a random pointer; c(v) is a counter

> **Answer: B.**
> **Explanation:** For every vertex v currently in V−S, p(v) stores the identity of the vertex in S to which v has its cheapest connecting edge, and c(v) stores the weight of that edge. Maintaining these two arrays lets the algorithm find the overall minimum-weight crossing edge in O(n) time per iteration (scan all c(v) values) rather than O(m) (scan all crossing edges), which is the key optimization over the naive O(nm) approach.

---

**Q5.** In Kruskal's algorithm, an edge is REJECTED specifically when:
**A)** Its weight is higher than the average
**B)** It connects two vertices already in the same connected component of the currently-accepted edge set A
**C)** It is the last edge in the sorted list
**D)** Both its endpoints have degree > 2

> **Answer: B.**
> **Explanation:** Kruskal's rejection rule is precisely the cycle-formation check, which — as proven in Section 7.2 — is equivalent to checking whether the edge's two endpoints already belong to the same connected component of the partial forest T=(V,A). If Find(x) == Find(y), adding (x,y) would create a cycle, so it must be rejected. If Find(x) ≠ Find(y), the edge safely merges two separate tree fragments (Union(X,Y)), and is accepted.

---

**Q6.** In the correctness proof that Kruskal's output A forms a connected graph, what does the proof do if it (hypothetically) assumes A is disconnected?
**A)** It shows the algorithm would crash
**B)** It finds the FIRST edge along a connecting path P (which exists in G since G is connected) that is NOT in A, and shows this edge would NOT create a cycle and thus MUST have been accepted by the algorithm — a contradiction
**C)** It recomputes the MST from scratch
**D)** It shows all edges must have equal weight

> **Answer: B.**
> **Explanation:** This is exactly the proof-by-contradiction structure in Section 6.5. If A were disconnected into components C₁ and C₂, since G itself is connected there's a path P between a vertex in C₁ and one in C₂. The first edge of P not already in A cannot form a cycle with A's current edges (since it bridges two different, not-yet-linked components) — so Kruskal's rejection rule would never have rejected it, meaning it MUST be in A. This contradicts our assumption that it's not in A, so A cannot actually be disconnected.

---

**Q7.** In the second part of Kruskal's correctness proof (that A is a MINIMUM spanning tree), what is the key final construction used to derive a contradiction?
**A)** T″ = T′ + x_j − y_t, and it's shown that w(T″) < w(T′), contradicting that T′ was minimum
**B)** A new random spanning tree is generated repeatedly
**C)** All edges are removed and re-added in reverse order
**D)** The graph is made directed

> **Answer: A.**
> **Explanation:** Assuming a strictly cheaper MST T′ exists, the proof finds the first index j where the sorted edge sequences of A (x-edges) and T′ (y-edges) diverge, uses Kruskal's greediness to establish w(x_j) < w(y_j), traces the cycle formed by adding x_j to T′, finds some edge y_t on that cycle with weight ≥ w(y_j) > w(x_j), and swaps: T″ = T′ + x_j − y_t. This T″ is a valid spanning tree with w(T″) < w(T′) — contradicting the assumed minimality of T′, and thus proving no cheaper tree than Kruskal's output can exist.

---

**Q8.** What is the defining structural feature that distinguishes an "in-tree" (used in the inverted forest data structure) from a regular rooted tree diagram?
**A)** In-trees must be perfectly balanced
**B)** Every edge is directed TOWARD the root (child → parent), and the root has a self-loop
**C)** In-trees can only have 2 children per node
**D)** In-trees have no leaves

> **Answer: B.**
> **Explanation:** As defined in Section 9.2, an in-tree is a directed rooted tree where all edges point "towards" the root — the opposite convention of a typical tree diagram where edges point away from the root toward children. Additionally, by convention, the root carries a self-loop: p(root) = root. This self-loop is exactly what allows the Find operation's "ancestor chasing" loop to have a clean, simple stopping condition: `while y ≠ p(y)`.

---

**Q9.** Using the plain Name-Array representation of the Partition ADT, what are the time complexities of Find(x) and Union(A,B) respectively?
**A)** O(1) and O(1)
**B)** O(n) and O(1)
**C)** O(1) and O(n)
**D)** O(log n) and O(log n)

> **Answer: C.**
> **Explanation:** Find(x) is a direct array lookup: `return Name[x]`, so it's O(1). Union(A,B), however, must scan through the ENTIRE array (all n positions) to find every element currently labeled B and relabel it to A (or vice versa), making it O(n). This asymmetry (cheap Find, expensive Union) is precisely why an alternative tree-based ("inverted forest") data structure is explored — because Kruskal's algorithm calls Union (n−1) times and Find 2m times, and for graphs with many edges relative to vertices, a data structure with fast Union pays off.

---

**Q10.** Why must the root of an in-tree have a self-loop rather than, say, a NULL parent pointer?
**A)** It's purely stylistic and has no algorithmic purpose
**B)** It provides a clean termination condition for the "ancestor chasing" Find loop: `p(x) = x` if and only if x is the root
**C)** Self-loops make the graph disconnected
**D)** It reduces memory usage

> **Answer: B.**
> **Explanation:** The Find(x) algorithm walks up parent pointers (`y = p(y)`) repeatedly until it reaches a fixed point where `y == p(y)`. Defining the root's parent pointer to point to itself creates exactly this fixed point, giving a simple, uniform loop condition (`while y ≠ p(y)`) without needing a special-case check for a NULL sentinel value.

---

**Q11.** In the weighted-union (union-by-size) optimization, what is the fundamental "doubling" lemma used to bound the worst-case Find complexity?
**A)** Every Union operation doubles the total number of vertices in the graph
**B)** Every time a specific element x's depth increases by 1, the size of the set containing x at least doubles
**C)** Every Find operation halves the depth of the tree
**D)** The number of trees doubles after every operation

> **Answer: B.**
> **Explanation:** This is the crux of the O(log n) Find proof (Section 10.3). Because weighted-union always hooks the SMALLER tree underneath the LARGER (or equal-sized) one, whenever a specific element x's depth increases (meaning x's current tree got hooked under another), it must be true that x's tree was the (weakly) smaller one, so the resulting merged tree has size ≥ 2 × (x's old tree size). Since a set's size can double at most log₂(n) times before exceeding n, any element's depth is bounded by O(log n), which directly bounds Find's runtime.

---

**Q12.** For Kruskal's algorithm, roughly how many Union and Find operations are performed in total (in terms of n = |V| and m = |E|)?
**A)** n Unions and m Finds
**B)** (n−1) Unions and 2m Finds
**C)** m Unions and n Finds
**D)** n² Unions and m² Finds

> **Answer: B.**
> **Explanation:** Exactly (n−1) edges end up being accepted into the final spanning tree, and each acceptance corresponds to exactly one Union call — hence (n−1) Unions total. However, EVERY edge in the sorted list L (all m of them) must be examined via two Find calls (one per endpoint) to test whether it should be accepted or rejected — hence 2m Find calls total, regardless of how many edges are ultimately accepted or rejected.

---

**Q13.** Given the total complexities O(n² + m) for name-array Kruskal's and O(n + m log n) for weighted-union-forest Kruskal's, which implementation is generally preferable for SPARSE graphs (where m = O(n))?
**A)** Name-array, because O(n²) is always smaller
**B)** Weighted-union forest, because O(n + m log n) becomes roughly O(n log n), much better than O(n²)
**C)** Both are identical for sparse graphs
**D)** Neither can handle sparse graphs

> **Answer: B.**
> **Explanation:** For a sparse graph where m = O(n), the name-array complexity O(n² + m) simplifies to O(n²) — dominated entirely by the expensive n² term from repeated O(n) Unions. In contrast, the weighted-union forest complexity O(n + m log n) simplifies to roughly O(n log n) — dramatically better, since it avoids ever paying an O(n) cost per Union (each Union there is O(1); only the O(log n) worst-case Find cost matters, applied across only 2m ≈ O(n) find calls).

---

**Q14.** Which of the following is NOT one of the standard properties/facts established about spanning trees in these notes?
**A)** Removing any edge from a spanning tree disconnects it into two components
**B)** Adding any non-tree edge to a spanning tree creates exactly one cycle
**C)** A spanning tree can contain cycles as long as it still spans all vertices
**D)** Between any two vertices in a tree, there is a unique simple path

> **Answer: C.**
> **Explanation:** This statement is FALSE and thus is the correct answer to "which is NOT a true property." By definition, a tree (and hence a spanning tree) must be ACYCLIC — it can never contain any cycle, no matter how many vertices it spans. The other three options (A, B, D) are all true, standard structural properties of trees covered in Section 2.3.

---

**Q15.** What is the essential conceptual link between the equivalence-relation view of graph connectivity (Section 1.2) and the Partition ADT used in Kruskal's implementation?
**A)** There is no real connection; they are unrelated topics covered separately
**B)** Connected components of a graph correspond exactly to the equivalence classes induced by a "path-connectivity" relation, and these classes ARE precisely the blocks of a partition — which is exactly what the Union-Find/Partition ADT is designed to represent and manipulate efficiently
**C)** Equivalence relations only apply to directed graphs
**D)** Partition ADTs cannot represent connected components, only vertex degrees

> **Answer: B.**
> **Explanation:** This is the deep unifying insight tying together Sections 1.2 and 7. The "is-connected-to" relation R on an undirected graph's vertices is reflexive, symmetric, and transitive — i.e., an equivalence relation — and equivalence relations always partition their underlying set into disjoint equivalence classes. Here, those classes are exactly the connected components of the graph. Since Kruskal's algorithm's core need is "track and merge connected components as edges are added," and connected components naturally form a partition, the Partition ADT (with Find and Union operations) is the perfectly natural and efficient tool for the job — this is not a coincidence but a direct consequence of the underlying mathematical structure.

---

## END OF NOTES

*Compiled from: W3U1 (Minimum Spanning Trees Part 2 — Prim's Algorithm development), W3U2 (Kruskal's Algorithm — design, proof, example), and W3U2 Part 2 (Kruskal's implementation — Union-Find, Name-Array, Inverted Forest, Complexity Analysis) — C. Pandu Rangan, NPTEL.*
