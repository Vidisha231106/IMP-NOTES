# Graph Theory — Unit V
### Discrete Mathematical Structures and Combinatorics | 4th Semester

---

## 1. Introduction to Graphs

### Definition
A **graph** G is a pair **G = {V, E}** where:
- **V = V(G)** — the **vertex set** (finite or countable)
- **E = E(G) ⊂ V × V** — the **edge set**

| Term | Meaning |
|------|---------|
| **Order (n)** | Number of vertices in G |
| **Size (m)** | Number of edges in G |

> We use **n** for order and **m** for size throughout.

**Example — Fibonacci Graph H:**  
S = {2, 3, 5, 8, 13, 21}. Pairs whose sum or difference (absolute value) also belongs to S form the edges.  
→ E(H) = {2,3}, {2,5}, {3,5}, {3,8}, {5,8}, {5,13}, {8,13}, {8,21}, {13,21}  
→ **Order = 6, Size = 9**

---

## 2. Terminology

| Term | Definition |
|------|-----------|
| **End vertices** | u and v are end vertices of edge (u, v) |
| **Parallel edges** | Edges that share the same two end vertices |
| **Loop** | An edge of the form (v, v) |
| **Simple graph** | No parallel edges and no loops |
| **Empty graph** | Graph with no edges (E is empty) |
| **Null graph** | Graph with no vertices at all (V and E are both empty) |
| **Trivial graph** | Graph with exactly one vertex |
| **Adjacent edges** | Edges that share a common end vertex |
| **Adjacent vertices** | Vertices u, v such that (u, v) is an edge |
| **Degree d(v)** | Number of edges with v as an end vertex |
| **Pendant vertex** | Vertex whose degree is 1 |
| **Pendant edge** | An edge that has a pendant vertex as an end vertex |
| **Isolated vertex** | Vertex whose degree is 0 |

> **Convention:** A loop counts **twice** toward degree. Parallel edges contribute **separately**.

---

## 3. Degree Theory

- **Minimum degree**: **δ(G)** — smallest degree in G
- **Maximum degree**: **Δ(G)** — largest degree in G
- For any vertex v in graph G of order n: **0 ≤ δ(G) ≤ deg(v) ≤ Δ(G) ≤ n − 1**

---

### Theorem 1 — First Theorem of Graph Theory (Handshaking Lemma)
> **∑_{v ∈ V(G)} deg(v) = 2m**

**Proof:** Every edge contributes exactly 2 to the total degree sum — once for each of its two endpoints. □

**Worked Example:**  
G has order 14, size 27. Degrees are only 3, 4, or 5; exactly 6 vertices have degree 4.  
Let x = # vertices of degree 3, y = # vertices of degree 5.  
- x + y = 14 − 6 = 8  
- 3x + 4(6) + 5y = 2(27) = 54  

Solving: **x = 5** (five degree-3 vertices), **y = 3** (three degree-5 vertices).

---

### Theorem 2 — Even Number of Odd-Degree Vertices
> **Every graph has an even number of vertices with odd degree.**

**Proof:** Partition V(G) into V₁ (odd-degree vertices) and V₂ (even-degree vertices).  
By the First Theorem: ∑_{V₁} deg(v) + ∑_{V₂} deg(v) = 2m.  
∑_{V₂} deg(v) is even (sum of even integers), so ∑_{V₁} deg(v) = 2m − (even) = even.  
Since each term in ∑_{V₁} is odd, the count |V₁| must be even. □

---

## 4. Degree Sequences

A **degree sequence** of G is the list of all vertex degrees (can be arranged in any order — non-increasing, non-decreasing, etc.).

A finite sequence of non-negative integers is **graphical** if it is the degree sequence of some graph.

**Example — Which sequences are graphical?**
| Sequence | Graphical? | Reason |
|----------|-----------|--------|
| 3, 3, 2, 2, 1, 1 | **Yes** | Sum = 12 = 2(6), valid |
| 6, 5, 5, 4, 3, 3, 3, 2, 2 | **No** | Fails Erdős–Gallai / degree sum issues |
| 7, 6, 4, 4, 3, 3, 3 | **No** | Only 7 vertices but max degree = 7 (impossible in simple graph) |
| 3, 3, 3, 1 | **No** | Sum = 10, which is even but the sequence fails graphicality conditions |

> **Quick check:** Sum must be **even** (for any graph to exist), and max degree must be < n.

---

## 5. Regular Graphs

- G is **regular** if δ(G) = Δ(G) (every vertex has the same degree)
- G is **r-regular** if every vertex has degree exactly r (where 0 ≤ r ≤ n−1)
- A **3-regular** graph is called a **cubic graph**

### The Petersen Graph
The most famous cubic graph. Defined as:
- **Vertices:** The ten 2-element subsets of {1, 2, 3, 4, 5}
- **Edges:** Pairs of disjoint 2-element subsets
- It is 3-regular with 10 vertices.

### Theorem — Existence of r-Regular Graphs
> **An r-regular graph of order n exists ⟺ at least one of r and n is even.**

**Reason:** If both r and n are odd, then ∑ deg(v) = rn = odd × odd = odd, which violates the First Theorem (must equal 2m, hence must be even).

---

## 6. Subgraphs

| Type | Definition |
|------|-----------|
| **Subgraph H ⊂ G** | V(H) ⊂ V(G) and E(H) ⊂ E(G) |
| **Proper subgraph** | H ⊂ G where V(H) ⊊ V(G) **or** E(H) ⊊ E(G) |
| **Spanning subgraph** | H ⊂ G where **V(H) = V(G)** (same vertices, subset of edges) |
| **Induced subgraph ⟨S⟩** | For S ⊂ V(G): subgraph with vertex set S containing **all** edges of G between vertices in S |
| **Edge-induced subgraph ⟨X⟩** | For X ⊂ E(G): subgraph with edge set X and all vertices incident to X |

- **G − e**: Spanning subgraph of G with edge e removed
- **G − X**: Spanning subgraph with all edges in set X removed; E(G − X) = E(G) − X

Also written as **G[S]** for ⟨S⟩ and **G[X]** for ⟨X⟩.

---

## 7. Walks, Trails, and Paths

### Walk
A **u−v walk W** is a sequence of vertices:  
**W : u = v₀, v₁, v₂, ..., vₖ = v**  
where consecutive vertices vᵢ and vᵢ₊₁ are adjacent for all i.

- **Length of W** = k (number of edges traversed, counting repetitions)
- **Closed walk**: u = v (same start and end)
- **Open walk**: u ≠ v
- **Trivial walk**: length 0 (single vertex W : v)

### Trail
A **u−v trail** is a u−v walk in which **no edge is repeated**.

### Path
A **u−v path** is a walk in which **no vertex is repeated** (and hence no edge repeated either).

> **Hierarchy:** Path ⊂ Trail ⊂ Walk (every path is a trail; every trail is a walk)

**Example** (from PDF graph with vertices u, v, w, x, y):
- Walk: x, y, w, y, v, w — length 5 (edge y-w repeated)
- Trail: u, w, y, x, w, v (no repeated edge)
- Path: u, w, y, v (no repeated vertex)

### Theorem
> If G contains a u−v walk of length l, then G contains a u−v **path** of length **at most l**.

---

## 8. Circuits and Cycles

| Term | Definition |
|------|-----------|
| **Circuit** | Closed trail of length **≥ 3** (no repeated edges; first = last vertex; vertices may repeat) |
| **Cycle** | Circuit that repeats **no vertex** except the first/last |
| **k-cycle** | Cycle of length k |
| **Triangle** | A 3-cycle |
| **Odd cycle** | Cycle of odd length |
| **Even cycle** | Cycle of even length |

> Deleting any vertex of a cycle yields a path. (This is **not** true for general circuits.)

---

## 9. Connectivity

- Vertices u and v are **connected** if there exists a u−v path in G
- G is **connected** if every pair of distinct vertices is connected  
  ↔ equivalently, G contains a u−v **walk** for every pair u, v
- G is **disconnected** if some pair of vertices has no connecting path
- A **component** of G is a maximal connected subgraph of G
- G is connected ⟺ G has **exactly one component**

For components G₁, G₂, ..., Gₖ: write **G = G₁ ∪ G₂ ∪ ... ∪ Gₖ**

### Theorem — Maximum Edges with k Components
> A simple graph with **n vertices and k components** has at most **⌊(n−k)(n−k+1)/2⌋** edges.

**Proof sketch:** Edges are maximized when each component is complete (Kₙᵢ for component i). Using the inequality:  
∑ nᵢ² ≤ (∑ nᵢ)² − (k−1)(2∑ nᵢ − k),  
the maximum total edges ∑ nᵢ(nᵢ−1)/2 ≤ (n−k)(n−k+1)/2. □

---

## 10. Distance in Graphs

For connected graph G, the **distance d(u, v)** = length of the **shortest u−v path**.

- A shortest u−v path is called a **u−v geodesic**
- If d(u, v) = k, then there is a path of length k but none shorter
- **Diameter diam(G)** = max distance between any two vertices in G
- **Radius rad(G)** = min eccentricity among all vertices (eccentricity of v = max distance from v to any other vertex)

| Graph | diam | rad |
|-------|------|-----|
| Kₙ (n ≥ 2) | 1 | 1 |
| Pₙ | n−1 | ⌈(n−1)/2⌉ |
| Cₙ | ⌊n/2⌋ | ⌊n/2⌋ |
| Kₚ,q (p or q ≥ 2) | 2 | 2 |

---

## 11. Common Classes of Graphs

### Path Pₙ
Vertices v₁, v₂, ..., vₙ with edges v₁v₂, v₂v₃, ..., vₙ₋₁vₙ.

### Cycle Cₙ (n ≥ 3)
Vertices v₁, ..., vₙ with edges v₁v₂, v₂v₃, ..., vₙ₋₁vₙ, **and** v₁vₙ.

### Complete Graph Kₙ
Every two distinct vertices are adjacent.  
- **Size** of Kₙ = C(n,2) = n(n−1)/2

### Bipartite Graph
V(G) can be partitioned into two disjoint sets **U** and **W** (called **partite sets**) such that every edge joins a vertex of U to a vertex of W.  
→ A graph is bipartite ⟺ **each of its components is bipartite**

### Complete Bipartite Graph Kₚ,q
Every vertex of U (|U| = p) is adjacent to every vertex of W (|W| = q).
- Vertices: n = p + q
- Edges: m = pq

> C₅ is **not** bipartite (it is an odd cycle — see theorem below).

### Theorem — Bipartite ↔ No Odd Cycles (Theorem 9)
> **A nontrivial connected graph G is bipartite ⟺ G contains no odd cycles.**

**Proof (⇒, by contradiction):** Suppose G is bipartite with parts Vₒ, Vₑ and contains an odd cycle v₁, v₂, ..., v₂ₖ₊₁, v₁. WLOG v₁ ∈ Vₒ. Since v₁v₂ is an edge, v₂ ∈ Vₑ. Alternating: v₃ ∈ Vₒ, ..., v₂ₖ₊₁ ∈ Vₒ. But v₂ₖ₊₁v₁ is an edge, forcing v₁ ∈ Vₑ — **contradiction**. □

---

## 12. Isomorphic Graphs

Graphs G and H are **isomorphic** (G ≅ H) if there is a bijection **f : V(G) → V(H)** such that:
$$uv \in E(G) \iff f(u)f(v) \in E(H)$$

The function f is called an **isomorphism**. If no such f exists, G and H are **non-isomorphic** (G ≇ H).

### Necessary Conditions for G ≅ H
1. **Same order** (same number of vertices)
2. **Same size** (same number of edges)
3. **Same number of vertices of each degree** (identical degree multisets)
4. **Same degree sequence AND same cycle vector** (c₁, c₂, ..., cₙ) where cᵢ = number of i-cycles

> **Important:** G ≅ H ⟺ G̅ ≅ H̅ (complements are also isomorphic iff originals are)

### Count of Non-Isomorphic Simple Graphs
| Order | Count |
|-------|-------|
| 1 | 1 |
| 2 | 2 |
| 3 | 4 |
| 4 | 11 |

---

## 13. Operations on Graphs

### Complement G̅
The **complement** of G has vertex set V(G), with edge uv ∈ E(G̅) **iff** uv ∉ E(G).

- If G has order n and size m, then G̅ has order n and size **C(n,2) − m**
- **K̄ₙ** (complement of Kₙ) = empty graph of order n (n vertices, no edges)
- G is **self-complementary** if G ≅ G̅
  - **Examples:** P₄ and C₅ are self-complementary graphs

### Cartesian Product G × H
- **Vertices:** Every ordered pair (u, v) where u ∈ V(G), v ∈ V(H)
- **(u, v) adjacent to (x, y)** iff:
  - u = x **and** vy ∈ E(H) — **(same G-part, H-neighbors)**, **or**
  - v = y **and** ux ∈ E(G) — **(same H-part, G-neighbors)**

> G × H ≅ H × G (order of multiplication is structurally irrelevant).

**Informal construction:** Replace each vertex x of G with a copy Hₓ of H. For adjacent u, v in G, join corresponding vertices of Hᵤ and Hᵥ by an edge.

### n-Cubes / Hypercubes Qₙ
Defined recursively:
- **Q₁** = K₂
- **Q₂** = K₂ × K₂ = C₄ (a 4-cycle)
- **Q₃** = C₄ × K₂ = **3-cube**
- **Qₙ** = Qₙ₋₁ × K₂

**Alternative definition:** Vertices are all n-bit binary strings; two vertices adjacent iff their strings differ in **exactly one bit position**.

---

## 14. Matrix Representations

### Adjacency Matrix A (n × n)
$$a_{ij} = \begin{cases} 1 & \text{if } v_iv_j \in E(G) \\ 0 & \text{otherwise} \end{cases}$$

- A is **symmetric** for simple graphs
- Diagonal is all 0s (no self-loops)
- Row sum of row i = **deg(vᵢ)**

### Incidence Matrix B (n × m)
$$b_{ij} = \begin{cases} 1 & \text{if vertex } v_i \text{ is incident with edge } e_j \\ 0 & \text{otherwise} \end{cases}$$

- Each **column** has exactly **two 1s** (one per endpoint of each edge)
- Row sum of row i = **deg(vᵢ)**

### Example (graph G with V = {v₁,v₂,v₃,v₄,v₅}, E = {e₁,...,e₆}):

$$A = \begin{bmatrix} 0&1&1&1&0 \\ 1&0&0&1&0 \\ 1&0&0&1&0 \\ 1&1&1&0&1 \\ 0&0&0&1&0 \end{bmatrix} \qquad B = \begin{bmatrix} 1&1&1&0&0&0 \\ 1&0&0&1&0&0 \\ 0&1&0&0&1&0 \\ 0&0&1&1&1&1 \\ 0&0&0&0&0&1 \end{bmatrix}$$

---

## 15. Eulerian Graphs

### Definitions
- **Eulerian circuit**: A circuit in G that contains **every edge exactly once**
- **Eulerian graph**: A connected graph that contains an Eulerian circuit
- **Eulerian trail**: An **open** trail containing every edge of G (start ≠ end)

### Theorem — Eulerian Condition (Theorem 12)
> **If G is Eulerian, then every vertex has even degree.**

**Proof:** In an Eulerian circuit, each visit to a vertex uses one incoming and one outgoing edge, contributing 2 to that vertex's degree. Since every edge is traversed exactly once and the circuit is closed, each vertex's degree is even. □

### Summary of Cases
| Graph property | Euler circuit | Euler trail |
|---------------|:---:|:---:|
| All vertices even degree | ✅ | ✅ (same as circuit) |
| Exactly 2 odd-degree vertices | ❌ | ✅ (start and end at odd-degree vertices) |
| >2 odd-degree vertices | ❌ | ❌ |

---

## 16. Hamiltonian Graphs

### Definitions
- **Hamiltonian cycle**: A cycle in G containing **every vertex** of G (= spanning cycle)
- **Hamiltonian graph**: A graph that contains a Hamiltonian cycle
- **Hamiltonian path**: A path in G containing every vertex of G

> **Key facts:**
> - Cₙ (n ≥ 3) and Kₙ (n ≥ 3) are Hamiltonian
> - Removing any edge from a Hamiltonian cycle produces a Hamiltonian path
> - A graph with a Hamiltonian path **need not** have a Hamiltonian cycle
> - No efficient characterization (unlike Eulerian) — this is an NP-complete problem

### Sufficient Conditions (not necessary)

**Ore's Theorem (Theorem 13):**
> If G has order n ≥ 3 and **deg(u) + deg(v) ≥ n** for every pair of non-adjacent vertices u, v → G is Hamiltonian.

**Dirac's Theorem (Theorem 14):**
> If G has order n ≥ 3 and **deg(v) ≥ n/2** for every vertex v → G is Hamiltonian.

> Both converses are **false** — these are sufficient but not necessary.

---

## 17. Planar Graphs

### Definition
G is **planar** if it can be drawn in the plane such that no two edges cross (except at common vertices). A non-planar graph cannot be so drawn.

A planar drawing divides the plane into **regions (faces)**, including one **unbounded (outer) region**.

---

### Euler's Identity / Formula (Theorem 15)
> For a **connected planar graph** with n vertices, m edges, and r regions:
> $$n - m + r = 2$$

**Proof (by induction on r):**  
*Base (r = 1):* No cycles → G is a tree → m = n − 1 → n − (n−1) + 1 = 2 ✓  
*Inductive step (r ≥ 2):* G has a cycle; let e be an edge on it. Removing e merges two adjacent regions into one: G − e has m−1 edges and r−1 regions. By induction hypothesis: n − (m−1) + (r−1) = 2, so n − m + r = 2. □

---

### Planarity Inequalities (Theorem 16)
For a planar graph with **n ≥ 3** vertices and **m** edges:
1. **m ≤ 3n − 6** (general case)
2. **m ≤ 2n − 4** (if G has no 3-cycles / triangles)

### Contrapositive Remarks (used to prove non-planarity)
1. If n ≥ 3 and **m > 3n − 6** → G is **non-planar**
2. If G has no triangles and **m > 2n − 4** → G is **non-planar**

---

### Theorem — Minimum Degree in Planar Graphs (Theorem 17)
> **Every planar graph contains at least one vertex of degree ≤ 5.**

**Proof:** Suppose all degrees ≥ 6. Then 2m = ∑ deg(v) ≥ 6n → m ≥ 3n > 3n − 6, violating planarity. □

---

### K₅ is Non-Planar (Theorem 18)
K₅ has n = 5, m = 10.  
Check: m = 10 > 9 = 3(5) − 6 → **m > 3n − 6** → K₅ is **non-planar** ✓

### K₃,₃ is Non-Planar (Theorem 19)
K₃,₃ has n = 6, m = 9, and no triangles (bipartite!).  
Check: m = 9 > 8 = 2(6) − 4 → **m > 2n − 4** → K₃,₃ is **non-planar** ✓

---

### Kuratowski's Theorem (Theorem 20)
> **G is planar ⟺ G has no subgraph homeomorphic to K₅ or K₃,₃.**

Two graphs are **homeomorphic** if one can be obtained from the other by subdividing edges (inserting/deleting degree-2 vertices on edges). This is the fundamental characterization of planarity.

---

### Detection of Planarity — Elementary Reductions

**Step 1:** A disconnected graph is planar ⟺ each component is planar. A separable graph is planar ⟺ each **block** (maximal non-separable subgraph) is planar.  
→ Decompose G into blocks G₁, G₂, ..., Gₖ and test each separately.

**Step 2:** Remove all **self-loops** (don't affect planarity).

**Step 3:** Remove **parallel edges** — keep only one edge between each vertex pair (parallel edges don't affect planarity).

**Step 4:** **Eliminate degree-2 vertices** by merging the two edges meeting at them into one (series reduction). This doesn't affect planarity.

> Repeatedly applying Steps 3 and 4 drastically simplifies the graph before testing.

---

## 18. Vertex Coloring

*Historical motivation: In 1852, Francis Guthrie observed that the counties of England can be colored with 4 colors so no two adjacent counties share a color. This led to the famous Four Color Problem.*

### Definitions
- **k-coloring**: Assignment f : V(G) → S with |S| = k (labels = "colors")
- **Color class**: Set of all vertices receiving the same color
- **Proper k-coloring**: Adjacent vertices always get **different** colors
- **k-colorable**: G has a proper k-coloring
- **Chromatic number χ(G)**: The **minimum** k for which G is k-colorable

> For coloring, we focus on **simple connected graphs** (parallel edges don't affect adjacency, disconnected components are handled separately).

### Key Properties of χ(G)

| Property | Statement |
|----------|-----------|
| 1 | χ(G) = 1 ⟺ G is totally disconnected (no edges) |
| 2 | G has ≥ 1 edge → χ(G) ≥ 2 |
| 3 | χ(G) ≤ n (trivial: give each vertex its own color) |
| 4 | H ⊂ G → χ(H) ≤ χ(G) |
| 5 | **χ(Kₙ) = n** and **χ(K̄ₙ) = 1** |
| 6 | **χ(C₂ₙ) = 2** (even cycles are bipartite) |
| 7 | **χ(C₂ₙ₊₁) = 3** (odd cycles need 3 colors) |
| 8 | **χ(Kₘ,ₙ) = 2** (bipartite → 2-colorable) |
| 9 | Disconnected G = G₁ ∪ ... ∪ Gᵣ → **χ(G) = max{χ(Gᵢ)}** |

### Fundamental Theorems

**Koenig's Theorem (Theorem 21):**
> A graph is **2-colorable (bicolorable) ⟺ it has no odd cycles** (i.e., it is bipartite).

**Upper Bound (Theorem 22):**
> **χ(G) ≤ 1 + Δ(G)** for every graph G.

**Brooks' Theorem (Theorem 23):**
> For a connected simple graph G that is **neither Kₙ nor an odd cycle**:
> **χ(G) ≤ Δ(G)**

### Coloring Theorems for Planar Graphs

| Theorem | Bound |
|---------|-------|
| Six Color Theorem (Thm 24) | χ(G) ≤ 6 |
| Five Color Theorem (Thm 25) | χ(G) ≤ 5 |
| **Four Color Theorem (Thm 26)** | **χ(G) ≤ 4** |

---

## 19. Chromatic Polynomial

### Definition
**P(G, λ)** = number of proper colorings using colors from {1, 2, ..., λ}.

**Properties:**
1. P(G, λ) = 0 for λ < χ(G)
2. P(G, λ) > 0 for λ ≥ χ(G)
3. χ(G) = smallest λ for which P(G, λ) > 0

### Standard Chromatic Polynomials

| Graph | P(G, λ) |
|-------|---------|
| **Kₙ** | λ(λ−1)(λ−2)···(λ−n+1) |
| **K̄ₙ** (null graph) | λⁿ |
| **K₁,₄** (star) | λ(λ−1)⁴ |
| **Any tree on n vertices** | λ(λ−1)ⁿ⁻¹ |

> Every two **isomorphic** graphs have the same chromatic polynomial.  
> But nonisomorphic graphs can share the same chromatic polynomial (e.g., all n-vertex trees do).

### Theorem — Tree Characterization (Theorem 27)
> G with n vertices is a tree ⟺ **P(G, λ) = λ(λ−1)ⁿ⁻¹**

### Deletion-Contraction Formula (Theorem 28)
For **non-adjacent** vertices u, v in G, let:
- **G + e**: G with edge uv added
- **G · e**: G with u and v **contracted** (fused together), parallel edges replaced by single edges

Then:
$$\boxed{P(G, \lambda) = P(G + e, \lambda) + P(G \cdot e, \lambda)}$$

**How to use it:** Repeatedly apply to non-adjacent pairs until all subproblems are complete graphs.

---

### Example 3 — Chromatic Polynomial of a 4-vertex "diamond" graph
Apply deletion-contraction on the one missing edge (between u, v):

$$P(G, \lambda) = P(K_4, \lambda) + P(K_3, \lambda)$$
$$= \lambda(\lambda-1)(\lambda-2)(\lambda-3) + \lambda(\lambda-1)(\lambda-2)$$
$$= \lambda(\lambda-1)(\lambda-2)\bigl[(\lambda-3) + 1\bigr]$$
$$= \lambda(\lambda-1)(\lambda-2)^2$$

### Example 4 — More complex graph (applying deletion-contraction twice)
$$P(G, \lambda) = P(K_5, \lambda) + 2P(K_4, \lambda) + P(K_3, \lambda)$$
$$= \lambda(\lambda-1)(\lambda-2)(\lambda-3)(\lambda-4) + 2\lambda(\lambda-1)(\lambda-2)(\lambda-3) + \lambda(\lambda-1)(\lambda-2)$$
$$= \lambda(\lambda-1)(\lambda-2)\bigl[(\lambda-3)(\lambda-4) + 2(\lambda-3) + 1\bigr]$$
$$= \lambda(\lambda-1)(\lambda-2)(\lambda^2 - 5\lambda + 7)$$

---

## 20. Edge Coloring

### Definitions
- **k-edge-coloring**: Assignment f : E(G) → S with |S| = k
- **Proper k-edge-coloring**: **Incident** edges receive different colors (each color class is a **matching**)
- **k-edge-colorable**: G has a proper k-edge-coloring
- **Edge chromatic number (Chromatic index) χ'(G)**: minimum k for a proper k-edge-coloring

### Lower Bound
$$\chi'(G) \geq \Delta(G)$$
(All edges at a vertex are incident and need different colors → at least Δ(G) colors.)

### Edge Chromatic Numbers of Basic Graphs

| Graph | χ'(G) |
|-------|--------|
| Kₙ (n odd) | n |
| Kₙ (n even) | n − 1 |
| Cₙ (n even) | 2 |
| Cₙ (n odd) | 3 |
| Pₙ (path) | 2 |

### Theorem — Bipartite Graphs (Theorem 29)
> For a bipartite graph G: **χ'(G) = Δ(G)**

**Corollary (Theorem 30):** For complete bipartite Kₘ,ₙ:
> **χ'(Kₘ,ₙ) = max(m, n)**

### Vizing's Theorem (Theorem 31)
> For any **simple** graph G:
> $$\Delta(G) \leq \chi'(G) \leq \Delta(G) + 1$$

*Proved independently by Vizing (1964) and Gupta (1966).* This means every simple graph is:
- **Class 1**: χ'(G) = Δ(G), or  
- **Class 2**: χ'(G) = Δ(G) + 1

---

## 21. Scheduling Applications

Graph coloring models resource conflict scheduling.

### Vertex Coloring → Course/Time Scheduling
Build graph: vertices = courses (or tasks), edges = conflicts (shared resource).  
**Minimum periods/slots = χ(G)**

**Example 5.1:** 6 courses (GT, S, LA, AC, G, MA) for 10 students. Construct conflict graph H (vertices = courses, edges = shared students). **χ(H) = 4** → **4 time periods** needed.

### Vertex Coloring → Traffic Lights
Build graph: vertices = lanes, edges = conflict (two lanes cannot be active simultaneously).  
**Minimum phases = χ(G)**

**Example 5.2:** 9 lanes L₁–L₉. Notice ⟨{L₂, L₄, L₆, L₈}⟩ ≅ K₄, so need ≥ 4 colors. Since 4-coloring exists: **χ(G) = 4 phases**.

### Edge Coloring → Tournament/Match Scheduling
Build graph: vertices = players/teams, edges = matches to be played.  
**Minimum days = χ'(H)** (no player can play two matches per day)

**Example 5.3:** Alvin + 3 couples (7 people). **χ'(H) = 6** → matches over **6 days**:
| Day | Matches |
|-----|---------|
| 1 | Bob–Gena, Carrie–Edith, David–Frank |
| 2 | Alvin–Frank, Bob–David, Edith–Gena |
| 3 | Alvin–Edith, Bob–Frank, Carrie–Gena |
| 4 | Alvin–Gena, Bob–Edith, Carrie–David |
| 5 | David–Gena, Edith–Frank |
| 6 | Alvin–David, Carrie–Frank |

---

## 22. Trees

### Definitions
- **Acyclic graph (Forest)**: A graph containing no cycles
- **Tree**: An **acyclic connected** graph
- **Forest**: An acyclic graph (not necessarily connected); each component of a forest is a tree

> Trees are connected; forests need not be.

---

### Theorem — Size of a Tree (Theorem 32)
> **Every tree of order n has exactly n − 1 edges.**

**Proof (by induction on n):**  
*Base:* K₁ has 0 edges = 1 − 1. ✓  
*Step:* Assume every tree of order k has k − 1 edges. Let T be a tree of order k + 1. Every non-trivial tree has ≥ 2 leaves (end-vertices). Remove a leaf v → T' = T − v is a tree of order k with k − 1 edges. T has exactly one more edge than T' → T has k edges = (k+1) − 1. □

---

### Theorem — Unique Path Characterization (Theorem 33)
> **G is a tree ⟺ every two vertices of G are connected by exactly one path.**

**Proof:**  
(→) G is a tree → G is connected (paths exist). Two distinct paths between the same pair would create a cycle — contradiction.  
(←) Unique paths → G is connected. If G had a cycle C, any two vertices on C would have two distinct paths — contradiction. So G is acyclic → G is a tree. □

---

### Theorem 34
> **Every edge in a tree is a bridge** (removing it disconnects the tree).

### Theorem — Forest Size (Theorem 35)
> **A forest of order n with k components has exactly n − k edges.**

---

### Special Types of Trees

| Type | Definition |
|------|-----------|
| **Star K₁,ₙ₋₁** | One central vertex connected to all n−1 others |
| **Path Pₙ** | Tree with exactly 2 end-vertices (leaves) |
| **Double star** | Tree with exactly 2 internal vertices (adjacent) — hence exactly 2 non-leaf vertices |
| **Caterpillar** | Tree of order ≥ 3 where removing all leaves gives a path (the **spine**) |

> Every path, star (of order ≥ 3), and double star is a caterpillar.

### Worked Problem
*A tree T of order 13 has vertices of degrees 1, 2, and 5 only. Exactly 3 vertices have degree 2. How many leaves (degree-1 vertices) does T have?*

Let x = # degree-1 vertices, then (13 − 3 − x) = (10 − x) = # degree-5 vertices.  
By the First Theorem: x(1) + 3(2) + (10 − x)(5) = 2(13 − 1)  
→ x + 6 + 50 − 5x = 24 → −4x = −32 → **x = 8** leaves.

---

### Rooted Trees
- Designate a vertex as the **root r** of tree T → T becomes a **rooted tree**
- Draw T with r at the top; other vertices arranged in **levels** by their distance from r

---

### Eccentricity and Center of a Tree
- **Eccentricity e(v)**: distance from v to the farthest vertex in G
- **Center of G**: vertex/vertices with minimum eccentricity
- Leaves always have the **largest** eccentricities → centers are **among internal vertices**

**Algorithm to find the center:**  
Repeatedly peel off all leaves. After each removal, re-identify new leaves. Continue until 1 or 2 vertices remain — these are the center(s).

**Theorem 36:**
> **Every tree has one or two centers. If two, they are adjacent.**

---

## 23. Spanning Trees and Kruskal's Algorithm

### Spanning Tree
A **spanning tree T** of connected graph G is a spanning subgraph (V(T) = V(G)) that is a tree.
- Every connected graph has at least one spanning tree
- A spanning tree of G with n vertices has exactly **n − 1 edges**

### Weighted Graphs
- A **weighted graph** assigns a real number **weight w(e)** to each edge (also called cost)
- **Weight of subgraph H**: $w(H) = \sum_{e \in E(H)} w(e)$

### Minimum Spanning Tree (MST)
A spanning tree T* with **minimum total weight** among all spanning trees of G.  
Finding one is the **Minimum Spanning Tree Problem**.

---

### Kruskal's Algorithm

**Input:** Connected weighted graph G with n vertices  
**Output:** A minimum spanning tree T

**Steps:**
1. Sort all edges in **non-decreasing order** of weight
2. Initialize T = ∅ (empty edge set)
3. For each edge e (in sorted order):
   - If adding e to T **does not create a cycle** → add e to T
   - Otherwise → skip e
4. Stop when T has **n − 1 edges** (spanning tree complete)

**Correctness:** Greedy choice of minimum-weight edges that maintain acyclicity always yields an MST (provable via the cut property of MSTs).

---

### Example — Railway Network Problem (Problem 37)

**Cities:** A, B, C, D, E, F, G, H  

| Edge | Cost | Edge | Cost |
|------|------|------|------|
| C–E | 105 | A–D | 155 |
| D–F | 110 | B–C | 155 |
| A–G | 130 | A–B | 160 |
| F–G | 150 | C–D | 160 |
| | | E–F | 160 |
| | | F–H | 160 |
| | | G–H | 170 |

**Kruskal's execution (sorted order):**

| Step | Edge | Cost | Action |
|------|------|------|--------|
| 1 | C–E | 105 | Add ✓ |
| 2 | D–F | 110 | Add ✓ |
| 3 | A–G | 130 | Add ✓ |
| 4 | F–G | 150 | Add ✓ (links {A,G} with {D,F}) |
| 5 | A–D | 155 | Skip — A and D already connected (via A-G-F-D) |
| 6 | B–C | 155 | Add ✓ |
| 7 | A–B | 160 | Add ✓ (links {A,D,F,G} with {B,C,E}) |
| 8 | F–H | 160 | Add ✓ (connects H; all 8 cities now connected) |

**Selected edges:** CE, DF, AG, GF, BC, AB, FH  
**Minimum cost = 105 + 110 + 130 + 150 + 155 + 160 + 160 = 970 crores**

*(The PDF expresses this equivalently as AB, AG, BC, CE, GF, HF, FD = 160+130+155+105+150+160+110 = 970)*

---

## Quick Reference: Key Formulas and Results

| Result | Formula / Statement |
|--------|---------------------|
| Handshaking Lemma | ∑ deg(v) = 2m |
| Odd-degree vertices | Always an even number |
| Size of Kₙ | n(n−1)/2 |
| Size of Kₚ,q | pq |
| Euler's formula (planar) | n − m + r = 2 |
| Planarity bound | m ≤ 3n − 6 |
| Planarity bound (no triangles) | m ≤ 2n − 4 |
| Max edges (n vertices, k components) | (n−k)(n−k+1)/2 |
| Tree: size | n − 1 edges |
| Forest: size | n − k edges (k components) |
| Complement size | C(n,2) − m |
| χ upper bound | χ(G) ≤ 1 + Δ(G) |
| Brooks' bound | χ(G) ≤ Δ(G) (not Kₙ or odd Cₙ) |
| Four Color Theorem | χ(planar G) ≤ 4 |
| Vizing's theorem | Δ(G) ≤ χ'(G) ≤ Δ(G) + 1 |
| χ'(bipartite G) | = Δ(G) |
| P(Kₙ, λ) | λ(λ−1)(λ−2)···(λ−n+1) |
| P(K̄ₙ, λ) | λⁿ |
| P(tree on n vertices, λ) | λ(λ−1)ⁿ⁻¹ |
| Deletion-contraction | P(G,λ) = P(G+e,λ) + P(G·e,λ) |

---

*Source: Unit V — Graph Theory | Department of Mathematics, RV College of Engineering | 4th Semester CS241AT — Discrete Mathematical Structures and Combinatorics*
