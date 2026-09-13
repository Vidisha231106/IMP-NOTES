# Strongly Connected Components — Complete Study Notes
*(Compiled from NPTEL Week 8 lecture notes: SCC Parts 1–3, BFS)*

---

## Table of Contents
1. [Directed Graphs, Cycles & DAGs — Foundations](#1-foundations)
2. [Mutual Reachability & Strongly Connected Graphs](#2-mutual-reachability)
3. [Theorem: Testing if a Graph is Strongly Connected](#3-theorem)
4. [Naive Algorithm — O(n⁴)](#4-naive)
5. [Improved Algorithm — 2 DFS Calls, O(n+m)](#5-improved)
6. [DFS Deep Dive: Discovery/Finish Time, Tree Edges, DFS Forest](#6-dfs-deep-dive)
7. [Strongly Connected Components — Formal Definition](#7-scc-definition)
8. [The Condensed Graph is Always a DAG (Proof)](#8-condensed-dag)
9. [Kosaraju's Algorithm — Full Method](#9-kosaraju)
10. [Worked Example — 12-Vertex Graph, Fully Traced](#10-worked-example)
11. [Correctness Intuition Behind Kosaraju's Algorithm](#11-correctness)
12. [BFS Essentials & BFS vs DFS](#12-bfs)
13. [Complexity Summary Table](#13-complexity)
14. [Quiz — Test Yourself](#14-quiz)

---

<a name="1-foundations"></a>
## 1. Foundations: Directed Graphs, Cycles & DAGs

A **directed graph** $G = (V, E)$ consists of a vertex set $V$ and a set of **ordered** edge pairs $E \subseteq V \times V$. Unlike undirected graphs, an edge $(u,v)$ does **not** imply $(v,u)$ exists.

**Key structural facts you must internalize:**

- A **cycle** in a directed graph is a sequence of vertices $v_1 \to v_2 \to \dots \to v_k \to v_1$ where consecutive vertices are connected by directed edges.
- **Critical Lemma (used everywhere in SCC theory):** *All vertices lying on a common directed cycle belong to the same Strongly Connected Component (SCC).*
  - Why? If $u$ and $v$ are both on a cycle, you can walk from $u$ to $v$ along the cycle, and also from $v$ back to $u$ along the rest of the cycle. Hence they are mutually reachable → same SCC.
- A **DAG (Directed Acyclic Graph)** is a directed graph with **no cycles at all**. DAGs are the natural output structure when you "collapse" cycles — this is exactly what happens when you shrink every SCC to a single node (see §8, the Condensed Graph).

**Intuition to carry forward:** SCC analysis is fundamentally about *finding all the cycles/cyclic clusters in a graph and treating each cluster as one unit*. Once you do that, what remains is always acyclic (a DAG).

---

<a name="2-mutual-reachability"></a>
## 2. Mutual Reachability & Strongly Connected Graphs

**Definition (Mutually Reachable):** Two vertices $u, v \in V$ are said to be **mutually reachable** if:
- there exists a path from $u$ to $v$, **and**
- there exists a path from $v$ to $u$, in $G$.

**Definition (Strongly Connected Graph):** $G$ is strongly connected **iff** every pair of vertices in $G$ is mutually reachable.

### Mutual reachability is an equivalence relation
Define $u \, R \, v$ iff $u$ and $v$ are mutually reachable. This relation is:
- **Reflexive:** $u$ reaches itself trivially.
- **Symmetric:** by definition (path both ways).
- **Transitive:** *(this is the non-trivial part, proved below)* — this is what allows the relation to **partition** $V$ into disjoint groups.

### Proof of Transitivity
Claim: If $u \to s$ (path) and $s \to v$ (path) exist, then a path $u \to v$ exists.

**Proof:** Let $P$ be a path from $u$ to $s$, and $Q$ be a path from $s$ to $v$.
- **Case 1 (P and Q are vertex-disjoint except at $s$):** Then $P$ followed by $Q$ (i.e., $P(u,s) + Q(s,v)$) is itself a valid path from $u$ to $v$.
- **Case 2 (P and Q share other vertices too):** Then $P + Q$ forms a **walk** from $u$ to $v$ (it may revisit vertices). However, *every walk from $u$ to $v$ contains a path from $u$ to $v$* (simply drop the repeated-vertex loops). Hence a path from $u$ to $v$ still exists. $\blacksquare$

By a symmetric argument, a path from $v$ to $u$ also exists (via $v \to s \to u$). So $u$ and $v$ are mutually reachable.

**Consequence:** Since mutual reachability is an equivalence relation, it **partitions** $V$ into disjoint equivalence classes. **Each equivalence class is, by definition, a Strongly Connected Component.**

---

<a name="3-theorem"></a>
## 3. Theorem: Testing if $G$ is Strongly Connected

> **Theorem:** Let $G=(V,E)$ be a directed graph and fix any vertex $s \in V$. Then $G$ is strongly connected **iff** for every $v \in V - \{s\}$:
> - there is a path from $s$ to $v$, **and**
> - there is a path from $v$ to $s$.

This is a huge simplification: instead of checking **all** $\binom{n}{2}$ pairs, you only need to check reachability **to and from one fixed vertex $s$**.

### Proof Sketch
**($\Leftarrow$) direction:** Suppose for all $u, v \ne s$: path $s \to u$ and path $u \to s$ both exist (similarly for $v$). We want to show a path exists between arbitrary $u$ and $v$.
- Since path $u \to s$ exists and path $s \to v$ exists, by **transitivity** (proved in §2) a path $u \to v$ exists.
- Similarly a path $v \to u$ exists (via $v \to s \to u$).
- Hence $u, v$ are mutually reachable for **all** pairs $\Rightarrow$ $G$ is strongly connected.

**($\Rightarrow$) direction:** Trivial — if $G$ is strongly connected, then in particular every vertex is mutually reachable with $s$.

### Turning the Theorem into an Algorithm
1. **Fix a vertex $s$.** Perform **one DFS** starting from $s$ **in $G$**.
   - If DFS colors all vertices **black** (fully visited) ⇒ all vertices are reachable **from** $s$. ✅ (first condition satisfied)
   - If any vertex remains **white** ⇒ some vertex is unreachable from $s$ ⇒ $G$ is **NOT** strongly connected. **Stop.**
2. **Construct $G^R$** (the reverse/transpose graph — see §6.3 for details).
3. Perform **one DFS** starting from $s$ **in $G^R$**.
   - A path from $s$ to $u$ in $G^R$ ⟺ a path from $u$ to $s$ in $G$ (reversing every edge of the path reverses its direction).
   - If DFS in $G^R$ reaches all vertices ⇒ every vertex has a path **to** $s$ in $G$. ✅ (second condition satisfied)
   - If any vertex is white ⇒ **NOT** strongly connected.
4. If **both** DFS calls turn every vertex black ⇒ return **"G is Strongly Connected."** Else return **"G is NOT strongly connected."**

**Complexity: $O(n + m)$** — just 2 DFS traversals (each DFS is $O(n+m)$), plus $O(n+m)$ to build $G^R$.

---

<a name="4-naive"></a>
## 4. Naive Algorithm — Why It's Slow

Before arriving at the elegant 2-DFS test above, the "obvious" brute-force method is:

- For **every ordered pair** $(u, v)$, $u \ne v$: run a DFS from $u$ and check if $v$ is reached, **and** run a DFS from $v$ and check if $u$ is reached.
- Number of DFS calls needed: $(n-1)$ pairs $(s, v)$ each needing 2 calls $\to (n-1) + (n-1) = 2(n-1) = O(n)$ ... but if done for **every vertex as the source** (not fixing one $s$), it becomes:
$$ 2\binom{n}{2} = n(n-1) \text{ DFS calls} $$
- Each DFS costs $O(n+m)$.
- **Total: $O(n(n-1)(n+m)) = O(n^2(n+m))$.**
- Since $m = O(n^2)$ in the worst case (dense graph), this becomes $O(n^2 \cdot n^2) = \boxed{O(n^4)}$.

**Key takeaway:** The naive method wastes effort because it doesn't realize that checking connectivity to/from **one fixed vertex $s$** is sufficient (Theorem in §3) — you don't need to repeat the check for every vertex as a "hub."

---

<a name="5-improved"></a>
## 5. Improved Algorithm — From $O(n^4)$ to $O(n+m)$

This is the single biggest optimization idea in this topic:

> **"The improvement from $n(n-1)$ DFS calls to 2 DFS calls is really interesting."**

Instead of testing all pairs, fix **any single vertex $s$**, and:
1. Run **1 DFS on $G$** from $s$ → confirms reachability **from** $s$ to everyone.
2. Run **1 DFS on $G^R$** from $s$ → confirms reachability **to** $s$ from everyone (equivalently: everyone can reach $s$ in $G$).

If both succeed (no white vertex left over in either run) ⇒ $G$ is strongly connected.

**Why this works (formally):** If a path exists from $s \to u$ AND from $u \to s$ for **every** $u \ne s$, then (by the transitivity proof in §2) a path exists between **any** two vertices $u, v \in V \times V$ ⇒ $G$ is strongly connected. So checking against **one fixed hub $s$** is mathematically equivalent to checking all $\binom{n}{2}$ pairs.

**Complexity:** $O(n+m)$ — dramatically better than $O(n^4)$.

---

<a name="6-dfs-deep-dive"></a>
## 6. DFS Deep Dive (Prerequisite Machinery for SCC)

### 6.1 Discovery Time & Finish Time
For every vertex $u$ visited during a DFS traversal, we record two timestamps:
- **$u.d$ (discovery time):** the "time" (an incrementing counter) at which $u$ is first discovered (turns from white → gray).
- **$u.f$ (finish time):** the time at which DFS **finishes** processing $u$ — i.e., all of $u$'s descendants have been fully explored (turns from gray → black).

Always: $u.d < u.f$, and time values across the whole graph run from $1$ to $2n$ (each vertex contributes one discovery "tick" and one finish "tick").

### 6.2 Tree Edges vs Non-Tree Edges (in DFS)
As DFS explores, the edges it traverses split into two categories:
- **Tree edges:** an edge $(u,v)$ traversed when $v$ is **white** (unvisited) at the moment of traversal — this edge becomes part of the **DFS tree/forest**.
- **Non-tree edges:** an edge $(u,v)$ where $v$ is **already visited** (gray or black) — these are back/forward/cross edges, and they do **not** appear in the DFS tree.

### 6.3 DFS Forest
In general, a single DFS call from one starting vertex may **not** reach all vertices of $G$ (if $G$ is disconnected/not strongly connected). So the full algorithm restarts DFS from any remaining unvisited (white) vertex, again and again, until all vertices are visited.

- Each restart produces **one tree**, rooted at the vertex where that DFS call began.
- The **tree edges** $(\pi(v), v)$ across *all* these restarted calls together form a **forest** — the **DFS forest**.
- **Crucially: the tree vertices of the DFS forest form a partition of $V$** (every vertex belongs to exactly one tree).

### 6.4 Constructing $G^R$ (the Reverse / Transpose Graph)
$G^R$ (also written $G^T$) is obtained from $G$ by **reversing the direction of every single edge**:
$$ (u,v) \in E \iff (v,u) \in E^R $$

Example: if $G$ has edges $a \to b$ and $c \to b$ (both pointing into $b$), then in $G^R$: $b \to a$ and $b \to c$ (both point *out of* $b$).

**Practical construction:** build a fresh adjacency list for $G^R$ by scanning every edge $(u,v)$ of $G$ once, and inserting $v \to u$ into $G^R$'s adjacency list. This takes $O(n+m)$ time — same order as building $G$ itself.

**Important fact used heavily below:** *$G$ and $G^R$ have exactly the same Strongly Connected Components.* (Reversing every edge doesn't change which vertices are mutually reachable — if $u,v$ are mutually reachable in $G$, the same two paths, reversed, prove mutual reachability in $G^R$.)

---

<a name="7-scc-definition"></a>
## 7. Strongly Connected Components — Formal Definition

> For a directed graph $G=(V,E)$: $G$ is strongly connected iff for every pair $(u,v)$, there is a path from $u$ to $v$.
>
> Define $u \, R \, v$ iff there is a path $u \to v$ **and** a path $v \to u$.
>
> This relation **induces a partition on the vertex set $V$**. **Each block/class of this partition is called a Strongly Connected Component (SCC).**

**Visualizing:** if a graph's SCCs are drawn as blobs (e.g., a square-shaped SCC, a triangle-shaped SCC, a single isolated vertex SCC, etc.), the graph is said to have "4 components," and so on — every vertex belongs to exactly one blob.

**Component Numbering Convention:**
- $V = \{1, 2, 3, \dots, n\}$
- Components are numbered $1, 2, 3, \dots$
- Input: $G = (V,E)$. Output: array $SCC[\,]$ where $SCC[i] = j$ means **vertex $i$ belongs to component $j$**.
- Example from lecture: $SCC(2) = 1$, $SCC(7) = 3$.

---

<a name="8-condensed-dag"></a>
## 8. The Condensed Graph — Always a DAG (Proof)

**Definition (Condensed Graph):** Shrink every SCC of $G$ into a single "super-vertex." Draw an edge from super-vertex $C_i \to C_j$ if there is at least one edge in $G$ from some vertex in $C_i$ to some vertex in $C_j$ (for $i \ne j$). This is the **condensed graph** (sometimes called the **component graph**), often denoted $G^{SCC}$.

> **Theorem: The condensed graph of any directed graph is always a DAG (acyclic).**

**Proof (by contradiction):**
Suppose the condensed graph had a cycle: $C_1 \to C_2 \to \dots \to C_k \to C_1$.
- Pick a vertex $x \in C_1$ and $y \in C_2$. Since $C_1 \to C_2$ has an edge, and by walking around the cycle $C_2 \to C_3 \to \dots \to C_k \to C_1$, there is a path from $y$ back to $x$ as well.
- So $x$ and $y$ are mutually reachable — **but $x \in C_1$ and $y \in C_2$ are supposed to be in *different* SCCs!**
- This is a contradiction, because if $x, y$ are mutually reachable, they must (by definition, §7) belong to the **same** SCC.
- Hence, the condensed graph **cannot** contain a cycle. $\blacksquare$

**Concrete illustration from the lecture (9-vertex example):**
A graph with SCCs $C_1 = \{2,1,3,4\}$, $C_2=\{5,6,7\}$, $C_3=\{8,9\}$, connected as $C_1 \to C_2 \to C_3$ but **explicitly no path from vertex 5 back to vertex 1** — confirming the condensed graph $C_1 \to C_2 \to C_3$ has no back-edge (i.e., stays acyclic).

**This is the theoretical backbone of Kosaraju's algorithm**: since the condensed graph is a DAG, it has a valid **topological order** on its SCCs, and this is exploited via finishing times (§9, §11).

---

<a name="9-kosaraju"></a>
## 9. Kosaraju's Algorithm — Full Method (finding ALL SCCs)

**Goal:** Given $G$ is **not** strongly connected, find **all** its SCCs efficiently.

### The Four Steps

**Step 1 — DFS on $G$, record finish times.**
Run DFS on $G$ (restarting from unvisited vertices as needed to cover the whole graph). For every $v \in V$, record $v.d$ (discovery) and $v.f$ (finish) times.

**Step 2 — Construct $G^R$.**
Build the reverse graph by flipping every edge (§6.4). $O(n+m)$.

**Step 3 — DFS on $G^R$, in decreasing order of finish time.**
This is the crucial twist:
- Process vertices in the order of **decreasing $v.f$** (the vertex with the globally **highest** finish time from Step 1 goes first).
- Start a fresh DFS call **only from unvisited vertices**, always picking the **unvisited vertex with the highest remaining finish time**.
- Each DFS call explores until it exhausts all vertices reachable from that root **within $G^R$** — this produces one **tree**.

**Step 4 — Output.**
Each tree produced in Step 3 is exactly **one Strongly Connected Component** of $G$. Output the vertex sets of each tree as the components.

### Pseudocode Summary
```
KOSARAJU-SCC(G):
    1. Run DFS(G) → compute u.f for every u ∈ V
    2. Compute G^R (reverse every edge)
    3. Run DFS(G^R), but in the main loop consider vertices
       in order of DECREASING u.f (from step 1)
    4. Each tree in the resulting DFS forest = one SCC of G
```

**Complexity:** $O(n+m)$ for Step 1, $O(n+m)$ for Step 2, $O(n+m)$ for Step 3 → **overall $O(n+m)$**, i.e., linear time, same order as a single DFS!

---

<a name="10-worked-example"></a>
## 10. Worked Example — Full Trace on a 12-Vertex Graph

This is the exact worked example from the lecture. Study this until you can redo it from scratch.

**The graph $G$** has vertices $1$–$12$ with edges forming roughly a "chain-like" shape:
`1↔2↔4↔3↔5`, then `5→7`, `4→6`, `7↔8↔9`, `9↔10`, `10↔11↔12`, etc. (see diagram in slide — the exact adjacency isn't critical to memorize; what matters is the *method*.)

### Step 1: DFS on $G$ — Discovery / Finish times

| $v$ | $v.d$ | $v.f$ |
|---|---|---|
| 1  | 1  | 24 |
| 2  | 2  | 23 |
| 3  | 19 | 20 |
| 4  | 3  | 22 |
| 5  | 18 | 21 |
| 6  | 4  | 17 |
| 7  | 5  | 16 |
| 8  | 6  | 15 |
| 9  | 7  | 14 |
| 10 | 8  | 13 |
| 11 | 9  | 12 |
| 12 | 10 | 11 |

**Resulting DFS forest on $G$ (tree edges only):**
- Tree 1 (rooted at 1): $1 \to 4 \to \{2, 5\}$ ... i.e. vertices $\{1, 3, 4, 2, 5\}$
- Tree 2 (rooted at 6): $\{6\}$ (isolated in the forest structure)
- Tree 3 (rooted at 7): $7 \to 9 \to 8$, i.e. $\{7, 9, 8\}$
- Tree 4 (rooted at 10): $10 \to 12 \to 11$, i.e. $\{10, 12, 11\}$

**These trees are a preview of the SCCs** — but note: **the DFS forest on $G$ itself is not guaranteed to give correct SCCs directly.** It only becomes exact once we do the second DFS on $G^R$ ordered by finish time (that's *the whole trick* of Kosaraju's algorithm). In this particular worked example, they happen to align — but you must always run Step 3 on $G^R$ to be rigorous.

### Step 2: Construct $G^R$
Reverse every edge of $G$; build a fresh adjacency list.

### Step 3: DFS on $G^R$ in decreasing order of $v.f$
- **Max finish time is 24, for vertex 1.** → Start DFS from vertex $1$ on $G^R$.
  - This reaches $\{1, 3, 4, 5, 2\}$ → **this set is one SCC of $G$.**
- Among remaining (unvisited) vertices, **vertex 6 has the max finish time (17).** → Continue DFS on $G^R$ from vertex $6$.
  - Reaches only $\{6\}$ → **$\{6\}$ is a SCC of $G$.**
- Next highest remaining finish time is vertex 7 (16). → DFS from $7$ on $G^R$.
  - Reaches $\{7, 9, 8\}$ → **another SCC.**
- Next highest remaining is vertex 10 (13). → DFS from $10$ on $G^R$.
  - Reaches $\{10, 12, 11\}$ → **final SCC.**

### Step 4: Final SCC List
$$ \{1,2,3,4,5\}, \quad \{6\}, \quad \{7,8,9\}, \quad \{10,11,12\} $$
Labeled $C_1, C_2, C_3, C_4$ respectively — **4 strongly connected components total.**

### The Condensed Graph for this example
Collapsing each SCC to a super-node gives a DAG:
$$ C_1 \to C_2, \quad C_1 \to C_3, \quad C_2 \to C_3, \quad C_3 \to C_4 $$
- $SCC(2) = 1$ (vertex 2 is in component 1)
- $SCC(7) = 3$ (vertex 7 is in component 3)
- Notice the ordering $C_1, C_2, C_3, C_4$ is exactly a **topological order** of the condensed DAG — this is *not* a coincidence; it falls directly out of running DFS on $G^R$ in decreasing finish-time order (see §11).

---

<a name="11-correctness"></a>
## 11. Correctness Intuition — *Why* Kosaraju's Algorithm Works

This is the "advanced" part most students skip — but understanding it deeply pays off.

### Key Lemma (Finish-Time Ordering Across Components)
> If there is an edge from component $C_i$ to component $C_j$ in the condensed graph ($C_i \ne C_j$), then:
> $$ \max_{v \in C_i} v.f \; > \; \max_{v \in C_j} v.f $$
> (The component whose DFS *finishes* first is always "later"/"deeper" in the DAG order; the component discovered/rooted first in the overall DFS forest finishes **last** among all components reachable from it.)

**Why this matters:** This means if you sort vertices by **decreasing finish time**, the very first vertex you encounter belongs to a component that has **no incoming edges** in the condensed DAG (a "source" component of the DAG) — informally, a "root" component that nothing else points into it before it, based on discovery/finishing order.

### Why reversing the graph "traps" DFS inside one SCC at a time
- In the **condensed DAG**, edges point from *earlier-finishing-max* components → *later-finishing-max* components (per the lemma).
- When we **reverse all edges** to get $G^R$, all condensed-DAG edges effectively flip direction too.
- So when we start DFS on $G^R$ from the vertex with the **globally highest finish time** (which sits in a "source" component of the original condensed DAG), the *reversed* edges leaving that component in $G^R$ **cannot lead outward** to any other not-yet-visited component (because in $G^R$, edges out of this component would correspond to edges *into* it in the original $G$ — but as a source component, nothing points into it, so nothing reverses to point out of it in $G^R$... except it *can* reach vertices strictly *within its own SCC*, since inside an SCC everything is mutually reachable both ways).
- **Net effect:** the DFS call from the highest-finish-time vertex, run on $G^R$, visits **exactly** the vertices of that one SCC — no more, no less.
- Removing that SCC and repeating with the next-highest remaining finish time peels off SCCs **one whole component at a time**, each time trapped precisely inside its own component. This is exactly the trace you saw in §10.

### Sanity Checks Used in the Lecture
- **"If there is a path from $s$ to $u$ in $G^R$, then there is a path from $u$ to $s$ in the original graph $G$."** (Reversing a path reverses its direction — the backbone fact behind using $G^R$ everywhere in this topic.)
- **DFS forest partition property:** "In general, DFS will result in several trees, each rooted at a vertex and consisting of all vertices reachable from the root — the tree vertices form a partition of $V$." When this DFS is run specifically on $G^R$ in decreasing finish-time order (from a prior DFS on $G$), **that partition IS exactly the SCC partition** of $G$.

---

<a name="12-bfs"></a>
## 12. BFS Essentials & BFS vs DFS

While BFS isn't directly used to compute SCCs (SCC computation is fundamentally a DFS-based technique because it relies on **finish times**, which BFS doesn't naturally produce), understanding BFS sharply by contrast deepens your DFS intuition — and BFS regularly appears in the same exam context.

### BFS vs DFS — Shape of the Search Tree
- **DFS** produces a **long, stringy tree** — it dives as deep as possible before backtracking (think: a single long branch, occasionally forking).
- **BFS** builds its search tree **level by level**, starting from the root (level 0), then all neighbors (level 1), then their neighbors (level 2), and so on — a **wide, shallow, layered tree**.

### BFS Tree Edges vs Non-Tree Edges
- **Tree edge:** an edge from a **visited vertex to an unvisited vertex** — i.e., $(visited \to unvisited)$. This is how the BFS tree grows.
- **Non-tree edge:** an edge between **two already-visited vertices**, i.e., $(visited \to visited)$. These do not extend the tree; they are "cross" connections within or across levels.
  - *(Contrast with DFS, where non-tree edges are classified further into back/forward/cross edges — BFS on an undirected/simple traversal typically only produces tree edges and cross edges within the same or adjacent level, never "forward" edges skipping levels, because BFS discovers everything at distance $k$ before anything at distance $k+1$.)*

### Vertex Attributes in BFS
- **$u.color$:** White (undiscovered) → Gray (discovered, being processed/in the frontier) → Black (fully processed, all neighbors examined).
- **$u.d$:** the **distance** from the root — literally the number of edges on the shortest path from the source $s$ to $u$ **in the BFS tree**. This is also called the **level number**. The root always has $u.d = 0$.
- The act of processing a vertex's neighbor list during BFS is called **"expanding the vertex."**

### Why This Matters for SCC Topic
- SCC algorithms need **finish times**, a concept that only cleanly arises from **DFS's recursive backtracking structure** (a vertex "finishes" only after ALL its descendants finish) — BFS has no equivalent natural notion of "finishing" a subtree, since it processes level by level rather than depth by depth. This is precisely why **Kosaraju's algorithm is DFS-based, not BFS-based**.

---

<a name="13-complexity"></a>
## 13. Complexity Summary Table

| Task | Method | Time Complexity |
|---|---|---|
| Check if $G$ strongly connected | Naive: $n(n-1)$ DFS calls over all pairs | $O(n^2(n+m)) = O(n^4)$ (since $m=O(n^2)$) |
| Check if $G$ strongly connected | Fix one $s$; 1 DFS on $G$ + 1 DFS on $G^R$ | $O(n+m)$ |
| Find ALL SCCs of $G$ | Kosaraju's Algorithm (DFS + reverse + DFS by finish time) | $O(n+m)$ |
| Build $G^R$ from $G$ | Scan all edges once, insert reversed | $O(n+m)$ |
| Single DFS or BFS traversal | Standard | $O(n+m)$ |

**Big-picture lesson:** almost every "cheap trick" in this unit boils down to *"don't redo work — exploit reachability structure (transitivity) and finish-time ordering to replace $O(n)$ redundant traversals with a small constant number of traversals."*

---

<a name="14-quiz"></a>
## 14. Quiz — Test Yourself

Try to answer before reading the explanation.

---

**Q1. Two vertices $u$ and $v$ lie on a common directed cycle in $G$. What can you conclude?**

**A.** They must be in the same SCC.
**B.** They must be adjacent (directly connected by an edge).
**C.** Nothing can be concluded without more information.
**D.** They must be in different SCCs.

> **Answer: A.**
> **Explanation:** Any two vertices on the same cycle can reach each other by walking around the cycle in both directions, so they're mutually reachable — which, by definition, places them in the same SCC (§1, §7). Being on the same cycle does *not* require them to be directly adjacent (B is wrong), and "different SCCs" directly contradicts the mutual-reachability definition (D is wrong).

---

**Q2. Why is checking strong connectivity by fixing a single vertex $s$ (instead of testing all $\binom{n}{2}$ pairs) mathematically valid?**

**A.** Because DFS from any vertex always visits every vertex in a directed graph.
**B.** Because mutual reachability is a transitive relation, so if $u \to s \to v$ and $v \to s \to u$ both hold, then $u,v$ are automatically mutually reachable.
**C.** Because $G^R$ always equals $G$.
**D.** It isn't valid in general; it only works for undirected graphs.

> **Answer: B.**
> **Explanation:** The whole justification (§2, §3) rests on proving mutual reachability is **transitive**: a path $u\to s$ concatenated with $s \to v$ yields (after removing revisited-vertex loops) a genuine path $u \to v$. This lets a single "hub" vertex $s$ stand in for all $\binom{n}{2}$ pairwise checks. (A is false — DFS only visits reachable vertices, not necessarily all vertices, which is exactly the test being used.)

---

**Q3. What is the time complexity of the naive strongly-connected-graph-checking algorithm, and why?**

**A.** $O(n+m)$, because it uses DFS.
**B.** $O(n^2(n+m))$ in general, which becomes $O(n^4)$ when $m = O(n^2)$.
**C.** $O(n \log n)$, similar to sorting.
**D.** $O(2^n)$, exponential.

> **Answer: B.**
> **Explanation:** The naive method performs $n(n-1)$ DFS calls (checking every ordered pair), each costing $O(n+m)$, giving $O(n(n-1)(n+m)) = O(n^2(n+m))$. Since a directed graph can have up to $m = O(n^2)$ edges, this simplifies to $O(n^2 \cdot n^2) = O(n^4)$ in the worst case (§4).

---

**Q4. What is $G^R$ (the reverse/transpose graph), and what key property does it preserve relative to $G$?**

**A.** $G^R$ has the same vertices but every edge direction is flipped; it has exactly the same SCCs as $G$.
**B.** $G^R$ removes all cycles from $G$.
**C.** $G^R$ is the condensed graph of $G$.
**D.** $G^R$ only exists if $G$ is already a DAG.

> **Answer: A.**
> **Explanation:** $G^R$ is built by reversing every edge $(u,v) \to (v,u)$ (§6.4). Because reversing both directions of every mutual-reachability path just swaps which direction is called "forward," the SCC partition is unchanged between $G$ and $G^R$ — this is exactly why Kosaraju's algorithm can safely use $G^R$'s DFS forest to identify $G$'s SCCs.

---

**Q5. In Kosaraju's Algorithm, why must Step 3 (DFS on $G^R$) process vertices in *decreasing* order of finish time obtained from Step 1 (DFS on $G$)?**

**A.** It's an arbitrary implementation choice; any order works equally well.
**B.** Decreasing finish-time order guarantees you always start from a vertex in a "source" component of the condensed DAG, ensuring each DFS call on $G^R$ stays trapped within exactly one SCC.
**C.** It ensures the algorithm runs in $O(n^2)$ instead of $O(n)$.
**D.** Finish time has no bearing on Step 3; only discovery time matters.

> **Answer: B.**
> **Explanation:** By the finish-time lemma (§11), the max finish time within a component is largest for "earliest/source" components in the condensed DAG. Starting DFS on $G^R$ from the currently-highest unvisited finish time ensures you begin at a source component (relative to remaining components); because condensed-DAG edges point away from source components, in $G^R$ those edges are reversed and cannot escape outward, so the DFS call is confined exactly to one SCC. **This ordering is not arbitrary — it is the entire correctness argument of the algorithm.**

---

**Q6. True or False: The DFS forest computed directly on $G$ (Step 1 of Kosaraju's algorithm) always immediately gives you the correct SCCs, without needing Step 2/3 (constructing and using $G^R$).**

> **Answer: False.**
> **Explanation:** The DFS forest on $G$ alone does *not* reliably reveal SCCs — you might get trees whose vertex sets don't correspond to true SCCs, because a single DFS tree on $G$ can span across multiple actual SCCs if edges happen to connect them in a way DFS traverses through. You need the finish-time ordering **combined with reversing the graph** to correctly isolate SCCs (§9, §11). In the lecture's worked example the two happened to visually align, but this alignment is not guaranteed in general and the algorithm's correctness proof relies fundamentally on the $G^R$ step.

---

**Q7. What is the overall time complexity of Kosaraju's Algorithm to find ALL SCCs of a graph, and why is this impressive?**

**A.** $O(n^4)$ — same as the naive pairwise method.
**B.** $O(n+m)$ — linear time, i.e., the same order of complexity as a single DFS traversal, because it consists of exactly a constant number (three) of $O(n+m)$ passes.
**C.** $O(n^2 \log n)$.
**D.** It depends on the number of SCCs, so no fixed bound exists.

> **Answer: B.**
> **Explanation:** Kosaraju's algorithm is 3 linear-time passes: (1) DFS on $G$, (2) build $G^R$, (3) DFS on $G^R$ in finish-time order. Each pass is $O(n+m)$, so the total remains $O(n+m)$ — impressively, finding **ALL** SCCs costs the same asymptotic order as running a **single** DFS (§9, §13).

---

**Q8. What does it mean for the "condensed graph" of $G$ to always be a DAG, and why is this true?**

**A.** It means every edge in $G$ disappears after condensing.
**B.** Collapsing each SCC to a single super-vertex and connecting super-vertices per inter-component edges always yields an acyclic graph — because a cycle among super-vertices would force those SCCs to merge into one bigger SCC, a contradiction.
**C.** It's only a DAG if $G$ itself was already acyclic.
**D.** The condensed graph's acyclicity depends on the choice of DFS root.

> **Answer: B.**
> **Explanation:** This is a proof by contradiction (§8): if condensed super-vertices $C_1 \to C_2 \to \dots \to C_k \to C_1$ formed a cycle, then any vertex in $C_1$ could reach any vertex in $C_2$ (and vice versa via the rest of the cycle), making them mutually reachable — but they were assumed to be in *different* SCCs, a contradiction. So no such cycle can exist among components, regardless of whether $G$ itself has cycles (indeed $G$ typically does have cycles — that's exactly what created the SCCs in the first place).

---

**Q9. In BFS, what does $u.d$ represent, and how does this differ conceptually from DFS's use of $d$ and $f$ timestamps?**

**A.** In BFS, $u.d$ is the discovery *time* just like in DFS; there is no difference.
**B.** In BFS, $u.d$ represents the *distance* (number of edges, i.e., "level") from the root to $u$ in the BFS tree — a structural/level concept — whereas DFS's $u.d$ and $u.f$ are *time-stamps* recording the order of a recursive dive-and-backtrack traversal.
**C.** BFS does not define $u.d$ at all.
**D.** $u.d$ in BFS is always equal to $u.f$ in DFS for the same vertex.

> **Answer: B.**
> **Explanation:** §12: BFS explicitly assigns $u.d$ as the **level number / distance from the root**, since BFS builds its tree strictly level-by-level. DFS's timestamps ($u.d$, $u.f$) instead record **temporal order** of a depth-first recursive exploration, which is why only DFS naturally supports the "finish time" concept crucial for SCC algorithms — BFS has no equivalent notion.

---

**Q10. Classify this BFS edge type: an edge $(u,v)$ is examined where both $u$ and $v$ are already visited (black or gray).**

**A.** Tree edge.
**B.** Non-tree edge.
**C.** This can never happen in BFS.
**D.** It is automatically a back edge (as in DFS terminology).

> **Answer: B.**
> **Explanation:** §12: BFS tree edges are specifically **(visited → unvisited)** transitions — that's literally how the BFS tree grows outward. An edge connecting two vertices that are **both already visited** is, by definition, a **non-tree edge** — it doesn't extend the search tree, just represents an extra connection.

---

**Q11. Suppose in the condensed DAG of a graph, there's an edge from component $C_i$ to component $C_j$. According to the finish-time lemma used to justify Kosaraju's algorithm, which of the following holds?**

**A.** $\max_{v\in C_i} v.f < \max_{v \in C_j} v.f$
**B.** $\max_{v\in C_i} v.f > \max_{v \in C_j} v.f$
**C.** Finish times give no information about component ordering.
**D.** $\min_{v \in C_i} v.d = \min_{v \in C_j} v.d$

> **Answer: B.**
> **Explanation:** §11's key lemma states that if the condensed DAG has an edge $C_i \to C_j$, the maximum finishing time among vertices of $C_i$ exceeds the maximum finishing time among vertices of $C_j$. This is exactly what lets us process components in decreasing order of finish time and always start from a "source" (topologically earliest) unvisited component when running DFS on $G^R$.

---

**Q12. In the 12-vertex worked example, vertex 1 has the maximum finish time (24) across the whole graph. What is the very first action taken in Step 3 of Kosaraju's algorithm, and what does it yield?**

**A.** Start DFS on $G$ (not $G^R$) from vertex 1; yields the full vertex set as one SCC.
**B.** Start DFS on $G^R$ from vertex 1 (the globally highest finish-time vertex); this DFS call reaches exactly $\{1,2,3,4,5\}$, which is declared one full SCC of $G$.
**C.** Start DFS on $G^R$ from vertex 12 (lowest finish time) first.
**D.** Skip vertex 1 since it was already visited during Step 1.

> **Answer: B.**
> **Explanation:** Per the algorithm (§9) and the worked trace (§10), Step 3 always begins from the **unvisited vertex with the globally highest finish time**, and it runs the DFS on $G^R$ (not $G$). In this example, vertex 1 (finish time 24) is chosen first, and the DFS confined within $G^R$ reaches only $\{1,2,3,4,5\}$ — declared as the first discovered SCC, matching component $C_1$.

---

### Quick Self-Check Recap (Answer in one line each, no options given)
1. Why must transitivity of mutual reachability hold for SCC to be a well-defined partition? *(Because an equivalence relation must be transitive to partition a set into disjoint classes.)*
2. Why is a single DFS insufficient to find SCCs, requiring a second DFS on $G^R$? *(Because forward reachability alone doesn't guarantee mutual reachability — you also need the reverse-direction check, and ordering by finish time is what correctly isolates components.)*
3. What guarantees the condensed graph has no cycles? *(A cycle among components would force those components to merge into a single SCC — contradiction.)*
4. What's the complexity gain from naive to optimized SCC-existence check? *(From $O(n^4)$ down to $O(n+m)$.)*
5. What's the complexity of finding ALL SCCs (not just testing existence of one big SCC)? *(Still $O(n+m)$, via Kosaraju's algorithm.)*

---

## Final One-Page Mental Model (Memorize This)

```
                     ┌─────────────────────────────┐
                     │   Mutual reachability (u,v)  │
                     │  = path u→v  AND  path v→u   │
                     └───────────────┬──────────────┘
                                     │ is an equivalence relation
                                     ▼
                     ┌─────────────────────────────┐
                     │   Partitions V into SCCs     │
                     └───────────────┬──────────────┘
                                     │ collapse each SCC to a point
                                     ▼
                     ┌─────────────────────────────┐
                     │  Condensed Graph = DAG       │  (proof: cycle among
                     │   (always acyclic!)          │   components ⇒ contradiction)
                     └───────────────┬──────────────┘
                                     │
              ┌──────────────────────┴───────────────────────┐
              ▼                                               ▼
   Just testing "IS G strongly connected?"        Finding ALL SCCs of G
   → fix vertex s; 1 DFS(G) + 1 DFS(G^R)          → Kosaraju's Algorithm:
   → O(n+m)                                          DFS(G) for f-times →
                                                       build G^R →
                                                       DFS(G^R) in decreasing
                                                       f-time order →
                                                       each tree = one SCC
                                                    → O(n+m)
```
