# All Pairs Shortest Paths (APSP) — Advanced Study Notes
### Compiled from NPTEL W2U3 (Parts 1–5), C. Pandu Rangan

> Audience note: these notes assume you're already comfortable with Dijkstra, Bellman-Ford, and basic DP over graphs. The goal here is depth — every algorithm is derived from first principles (not just stated), every recurrence is proven, and every complexity bound is justified term-by-term.

---

## Table of Contents
1. [Problem Setup & Notation](#1-problem-setup--notation)
2. [Extended Weight Matrix](#2-extended-weight-matrix)
3. [Shortest Paths Weight Matrix](#3-shortest-paths-weight-matrix)
4. [Bellman Equations](#4-bellman-equations)
5. [Algorithm 1 — Basic DP over Edge-Count](#5-algorithm-1--basic-dp-over-edge-count)
6. [Relation to Matrix Multiplication (min-plus semiring)](#6-relation-to-matrix-multiplication-min-plus-semiring)
7. [Squaring Operations & Algorithm 2](#7-squaring-operations--algorithm-2)
8. [Generalized Bellman Equations 1 & 2](#8-generalized-bellman-equations-1--2)
9. [Squaring for Extended BE & Algorithm 3](#9-squaring-for-extended-be--algorithm-3)
10. [k-Paths Formulation](#10-k-paths-formulation)
11. [Algorithm 4 — Floyd–Warshall](#11-algorithm-4--floydwarshall)
12. [Johnson's Algorithm](#12-johnsons-algorithm)
13. [Master Comparison Table](#13-master-comparison-table)
14. [Quiz — Test Yourself](#14-quiz--test-yourself)

---

## 1. Problem Setup & Notation

**All Pairs Shortest Path (APSP):** Given a directed, weighted graph $G = (V, E)$ with **no negative-weight cycle** (zero-weight cycles are also excluded in this treatment), compute the shortest-path weight $\delta(u,v)$ for **every** ordered pair $u, v \in V$.

Definitions used throughout:

- $\delta(u,v)$ = weight of the shortest path from $u$ to $v$ (for $u \neq v$).
- $\delta(u,u) = 0$ (empty path).
- If no path exists from $u$ to $v$, $\delta(u,v) = \infty$.

We want $\delta(u,v)\ \forall\, u,v \in V$ — an $n \times n$ table, where $n = |V|$.

**Why not just run Dijkstra/Bellman-Ford from every vertex?**
You *can* — this is exactly what Johnson's Algorithm does at its core (Section 12). But the goal here is to also study **matrix-based / DP formulations** that don't rely on repeated single-source runs, because:
- They generalize elegantly (min-plus algebra ↔ real matrix multiplication).
- Floyd–Warshall (Algorithm 4) becomes the cleanest $O(n^3)$ algorithm in practice, with no auxiliary source-vertex trick needed.
- They set up exactly the trick (weight transformation) that Johnson's algorithm needs to handle negative edges without negative cycles.

---

## 2. Extended Weight Matrix

Since we want a **matrix** of values, fix $V = \{1, 2, \dots, n\}$.

The **extended weight function** $w$ is defined on **all pairs** $V \times V$ (not just edges):

$$
w(u,u) = 0 \quad \forall u \in V
$$
$$
w(u,v) = \infty \quad \text{if } (u,v) \notin E
$$
$$
w(u,v) = \text{actual edge weight} \quad \text{if } (u,v) \in E
$$

In matrix form:

$$
W = [w_{ij}]_{n \times n}, \qquad
w_{ij} =
\begin{cases}
0 & i = j \\
\infty & (i,j) \notin E \\
w(i,j) & (i,j) \in E
\end{cases}
$$

**Why "extend" the weight function at all?** Because every DP formulation below needs to talk about "the weight of going from $i$ to $j$ in one step" *even when there's no edge* (treated as $\infty$, meaning "not usable") and *even when $i = j$* (treated as $0$, meaning "staying put costs nothing" — this is the identity for path concatenation). Without this convention the recurrences below would need constant special-casing.

**Set of bounded-length paths.** Let $P(l : i, j)$ = the set of all paths from $i$ to $j$ using **at most $l$ edges**, for $l \geq 0$.

- $P(0 : i, j)$: only relevant if $i = j$ (the trivial zero-edge path) — this is where $w_{ii}=0$ comes from.
- Since $G$ is simple (no advantage to repeating a vertex on a shortest path when there are no negative cycles), **any shortest path has at most $n - 1$ edges.**

---

## 3. Shortest Paths Weight Matrix

Define $\delta_{\leq l}(i,j)$ = weight of the shortest path in $P(l : i, j)$ (i.e., best path using **at most** $l$ edges).

Matrix notation:

$$
D^{(l)} = \big[\delta_{\leq l}(i,j)\big] = \big[d^{(l)}_{ij}\big]_{n \times n}
$$

**Base case** ($l = 0$):

$$
d^{(0)}_{ij} =
\begin{cases}
0 & i = j \\
\infty & i \neq j
\end{cases}
$$

(No path with zero edges connects distinct vertices.)

**Goal:** Since any shortest path has $\leq n-1$ edges, we want $D^{(n-1)}$ — this equals the true APSP matrix $\delta(i,j)$.

**Key intuition for the whole section:** Every algorithm from here (Algorithm 1, 2, 3, and even Floyd–Warshall in disguise) is answering the same question — *"how do I get from $D^{(0)}$ to $D^{(n-1)}$ (or an equivalent final matrix) as cheaply as possible?"* — just using different recurrences and different "step sizes."

---

## 4. Bellman Equations

We want to compute $D^{(l)}$ from $D^{(l-1)}$. The **Bellman equation** is the recurrence that makes this possible:

$$
\boxed{d^{(l)}_{ij} = \min_k \left\{ d^{(l-1)}_{ik} + w_{kj} \right\}} \tag{1}
$$

### Statement (what needs proving)
1. $d^{(l)}_{ij} \geq d^{(l-1)}_{ik} + w_{kj}$ for **all** $k$ (the RHS never *underestimates* — i.e., no assignment of $k$ can beat the true value).
2. $d^{(l)}_{ij} = d^{(l-1)}_{ik} + w_{kj}$ for **some** $k$ (equality is *achieved* by the right choice of $k$).

Together, (1) and (2) give exactly $d^{(l)}_{ij} = \min_k \{ d^{(l-1)}_{ik} + w_{kj} \}$.

### Proof

**Part 1 (inequality, $\geq$).**
Fix any $k \in V$. Take any path $P$ from $i$ to $k$ with $\leq l-1$ edges (weight $\geq d^{(l-1)}_{ik}$ by definition of $D^{(l-1)}$ as the *best over such paths*), and append the single edge $(k,j)$ with weight $w_{kj}$ (or, if $k=j$, appending nothing since $w_{jj}=0$). This produces *some* walk from $i$ to $j$ with $\leq l$ edges, of total weight $\geq d^{(l-1)}_{ik} + w_{kj}$.
Since $d^{(l)}_{ij}$ is defined as the minimum over **all** walks/paths with $\leq l$ edges, and this particular walk is one candidate among many, we get:
$$
d^{(l)}_{ij} \leq (\text{weight of this walk})
$$
Wait — we need the *other* direction for the proof of the lower bound. Let's redo this carefully using the actual shortest-path structure (this is the standard argument):

Let $P^*$ be an actual shortest path from $i$ to $j$ using **at most $l$ edges**, so $w(P^*) = d^{(l)}_{ij}$.

- **Case A: $P^*$ has $\leq l - 1$ edges.** Then trivially $d^{(l)}_{ij} = d^{(l-1)}_{ij}$ (adding budget doesn't help if you didn't need it), and taking $k = j$ in the RHS gives $d^{(l-1)}_{ij} + w_{jj} = d^{(l-1)}_{ij} + 0 = d^{(l)}_{ij}$ — equality achieved with $k=j$.
- **Case B: $P^*$ has exactly $l$ edges.** Let $k$ be the second-to-last vertex on $P^*$, i.e., $P^*$ looks like $i \rightsquigarrow k \to j$ where the sub-path $Q = i \rightsquigarrow k$ has exactly $l - 1$ edges and the last edge is $(k,j)$.
  - **Sub-claim: $Q$ is itself a shortest $(l-1)$-edge path from $i$ to $k$**, i.e., $w(Q) = d^{(l-1)}_{ik}$.
    *Proof by contradiction:* if some other path $Q'$ from $i$ to $k$ with $\leq l-1$ edges had $w(Q') < w(Q)$, then replacing $Q$ by $Q'$ in $P^*$ gives a new walk $i \rightsquigarrow k \to j$ with $\leq l$ edges and total weight $w(Q') + w_{kj} < w(Q) + w_{kj} = w(P^*) = d^{(l)}_{ij}$ — contradicting that $P^*$ was the *shortest* such walk. (This is the classic **"optimal substructure"** argument, identical in spirit to the proof used for Dijkstra/Bellman-Ford.) $\blacksquare$
  - So $w(P^*) = w(Q) + w_{kj} = d^{(l-1)}_{ik} + w_{kj}$, which means **equality is achieved at this particular $k$** — proving part (2).

**Part 2 (no $k$ can beat the optimum).**
For an arbitrary $k$, the quantity $d^{(l-1)}_{ik} + w_{kj}$ is (as shown in the "Part 1" attempt above) the weight of *some* valid walk from $i$ to $j$ with $\leq l$ edges (concatenate the best $(l-1)$-edge $i \to k$ path with the edge $k \to j$). Since $d^{(l)}_{ij}$ is the *minimum* weight over all such walks, no candidate can be strictly smaller:
$$
d^{(l)}_{ij} \leq d^{(l-1)}_{ik} + w_{kj} \quad \forall k
$$

Combining: $d^{(l)}_{ij} \leq d^{(l-1)}_{ik}+w_{kj}\ \forall k$, and equality holds for the specific $k$ on the actual optimal path ⟹
$$
d^{(l)}_{ij} = \min_k \left\{ d^{(l-1)}_{ik} + w_{kj} \right\}. \qquad \blacksquare
$$

> **Note on slide correspondence:** the original slides state the inequality direction as "$d^{(l)}_{ij} \geq d^{(l-1)}_{ik}+w_{kj}\ \forall k$" — this is consistent with what's derived above (just written as $\geq$ instead of $\leq$ depending on which side is "fixed"; both together pin down the minimum exactly). The essential content is: *(no k beats the optimum) + (some k achieves it) = the recurrence is a valid minimum.*

### Complexity of one Bellman-equation update
Computing a single $d^{(l)}_{ij}$ requires trying all $n$ choices of $k$, each involving one addition and one comparison → $O(n)$ work per cell.
There are $n^2$ cells in $D^{(l)}$ → $O(n^3)$ to compute the **entire matrix** $D^{(l)}$ from $D^{(l-1)}$.

---

## 5. Algorithm 1 — Basic DP over Edge-Count

```
APSP-1(G = (V, E), W):
    // V = {1, 2, ..., n}; G has no negative/zero cycles
    // W is the extended weight matrix

    D(0) = [d_ij^(0)]  where  d_ij^(0) = 0 if i=j, else ∞

    for l = 1 to n-1:
        compute D(l) from D(l-1) using the Bellman equation (Eq. 1)

    return D(n-1)
```

**Correctness:** immediate from Section 3 — since every shortest path uses $\leq n-1$ edges, $D^{(n-1)} = \delta$.

**Complexity:**
- Each iteration ($D^{(l-1)} \to D^{(l)}$) costs $O(n^3)$ (Section 4).
- There are $n - 1$ iterations.
- **Total: $O(n^4)$.**

This is correct but slow — it's essentially "Bellman-Ford run simultaneously from every source, incrementing edge-budget by exactly 1 each round." The rest of the notes are about **doing better than incrementing the edge-budget by 1 at a time.**

---

## 6. Relation to Matrix Multiplication (min-plus semiring)

This is the conceptual pivot of the whole unit: **the Bellman equation is literally matrix multiplication**, just over a different algebraic structure.

### The reinterpretation trick

Redefine the symbols:
- $a + b := \min\{a, b\}$ (so "$+$" now means "Min")
- $a \cdot b := a + b$ (usual addition) (so "$\cdot$" now means "sum")

Check this is sensible:
- $5 + 3 = \min(5,3) = 3$ (compare: ordinary $5+3=8$).
- $5 \cdot 7 = 5 + 7 = 12$ (i.e., "multiplication" is now what addition used to do).

**Why does this work algebraically?** Both $\min$ and $+$ (ordinary sum) are **associative** and **commutative**, which is exactly what's needed for the "sum of products" pattern in matrix multiplication to make sense under reinterpretation. This structure is formally called the **min-plus semiring** (a.k.a. the **tropical semiring**).

### The zero element (identity for "+")

For ordinary $+$: identity is $0$, since $5 + 0 = 5$.

For "+"= Min: we need $e$ such that $\min(a, e) = a\ \forall a$. This forces $e = \infty$.

$$
\boxed{\text{The "zero element" for Min is } \infty}
$$

This explains *why* $w_{ij} = \infty$ for non-edges in the extended weight matrix (Section 2) — it's not an arbitrary convention, it's **forcing $W$ to be the correct identity-respecting matrix** under min-plus algebra (an "absent edge" must act as "does nothing" under $\min$, exactly like $0$ does nothing under ordinary $+$).

Also, under this reinterpretation:
$$
\text{Min}(a_1, \dots, a_n) = a_1 + a_2 + \cdots + a_n = \sum_{k=1}^n a_k \quad (\text{"+" = Min})
$$

### Rewriting the Bellman equation

Recall Eq. (1): $d^{(l)}_{ij} = \min_k \{ d^{(l-1)}_{ik} + w_{kj} \}$ (ordinary $+$, ordinary min).

Under the new interpretation ($\cdot$ = ordinary sum, "+" = min):

$$
d^{(l)}_{ij} = \min_k \{ d^{(l-1)}_{ik} \cdot w_{kj} \} = \sum_{k=1}^n d^{(l-1)}_{ik} \cdot w_{kj} \tag{3}
$$

Compare this to the **standard matrix product formula**: if $C = AB$ with $A = [a_{ij}]_{n\times n}$, $B = [b_{ij}]_{n \times n}$:

$$
C = [c_{ij}]_{n \times n}, \quad c_{ij} = \sum_{k=1}^n a_{ik} b_{kj} \tag{4}
$$

**(3) and (4) are syntactically identical!** So we may write, in min-plus notation:

$$
D^{(l)} = D^{(l-1)} \otimes W
$$

(using $\otimes$ to denote min-plus matrix "multiplication" to avoid confusion with ordinary matrix product — the slides just write it as ordinary product $D^{(l-1)}W$ once the reinterpretation is fixed).

### Consequence: closed form and associativity

$$
D^{(1)} = D^{(0)} W, \qquad D^{(2)} = D^{(1)}W = D^{(0)}W W = D^{(0)}W^2
$$
(valid because **min-plus matrix multiplication is associative**, inherited directly from associativity of Min and $+$).

Inductively:
$$
D^{(i)} = D^{(0)} W^i \quad \Longrightarrow \quad D^{(n-1)} = D^{(0)} W^{n-1}
$$

This alone re-derives Algorithm 1's $O(n^4)$ bound (computing $W, W^2, \dots, W^{n-1}$ one multiplication at a time — $n-2$ multiplications, each $O(n^3)$). But writing it as **repeated multiplication by the same matrix $W$** immediately suggests a much better idea: **repeated squaring**.

---

## 7. Squaring Operations & Algorithm 2

### The key idea

If we can compute $W^{n-1}$ *directly* and *efficiently* (rather than one factor of $W$ at a time), then $D^{(n-1)} = D^{(0)}W^{n-1}$ gives us the answer with far fewer matrix multiplications.

**Repeated squaring** computes $W^{2^k}$ using only $k$ multiplications (each a *squaring*, i.e., multiplying a matrix by itself):

$$
W \to W^2 \to W^4 \to W^8 \to \cdots \to W^{2^k}
$$

Example: $W^{32}$ needs only **5** squarings: $W \to W^2 \to W^4 \to W^8 \to W^{16} \to W^{32}$ (instead of 31 multiplications the naive way!).

### Why powers of 2 are enough: idempotence beyond $n-1$

Crucial fact: $D^{(n-1)} = D^{(t)}\ \forall\, t \geq n-1$.
**Why:** the longest possible *simple* shortest path has at most $n-1$ edges (no negative cycles ⟹ never beneficial to revisit a vertex on a shortest path), so allowing *more* edge-budget than $n-1$ cannot change the answer — the Bellman recurrence simply stops improving.

So we don't need $W^{n-1}$ exactly — **any** $W^t$ with $t \geq n-1$ works, and we should pick $t$ to be the smallest **power of 2** that is $\geq n - 1$:

$$
t = 2^{\lceil \log_2 n \rceil} \geq n - 1
$$

Example: to get $D^{(13)}$ for some graph, since $2^4 = 16 \geq 13$, compute $D^{(16)} = D^{(13)}$ instead — via $W \to W^2 \to W^4 \to W^8 \to W^{16}$ (4 squarings), then $D^{(16)} = D^{(0)}W^{16}$.

### Algorithm 2 (pseudocode)

```
Algorithm-2(W):
    // Step 1: compute W^t for smallest power-of-2 t >= n-1
    for l = 1 to ceil(log n):
        W = W * W          // squaring (min-plus multiplication)
    // now W = W^t for some t > n-1

    // Step 2: apply to D(0)
    D = D(0) * W           // D = D^(t) = D^(n-1)

    return D
```

### Complexity of Algorithm 2

- Each squaring (min-plus matrix multiply of two $n\times n$ matrices) costs $O(n^3)$.
- Step 1 performs $\lceil \log n \rceil$ squarings; Step 2 performs 1 more multiplication.
- Total matrix multiplications: $\lceil \log n \rceil + 1$.

$$
\boxed{\text{Total complexity} = O\big((\log n + 1)\, n^3\big) = O(n^3 \log n)}
$$

This strictly beats Algorithm 1's $O(n^4)$ for large $n$ (since $\log n \ll n$).

---

## 8. Generalized Bellman Equations 1 & 2

So far the recurrence was based on the **last edge** of the path (or equivalently the vertex just before the destination — vertex $k$ in $i \rightsquigarrow k \to j$). We now generalize to **any intermediate vertex**, not just the second-to-last one. This generalization is the conceptual seed for **both** the $k$-path formulation (→ Floyd–Warshall) **and** the squaring-based Algorithm 3.

### Generalized BE 1 (optimal substructure at *any* interior point)

**Claim:** If $k$ is *any* intermediate vertex on a shortest path $P$ from $i$ to $j$, then:
- the portion of $P$ from $i$ to $k$ is itself a shortest path from $i$ to $k$, **and**
- the portion of $P$ from $k$ to $j$ is itself a shortest path from $k$ to $j$.

**Proof (by contradiction, "cut and paste" argument):**
Write $P = Q \cdot R$ where $Q$ is the $i \rightsquigarrow k$ portion and $R$ is the $k \rightsquigarrow j$ portion, so $w(P) = w(Q) + w(R)$.
Suppose $Q$ is **not** a shortest $i \to k$ path — i.e., some path $Q'$ satisfies $w(Q') < w(Q)$. Then replace $Q$ with $Q'$ in $P$: the new walk $Q' \cdot R$ goes from $i$ to $j$ with weight $w(Q') + w(R) < w(Q) + w(R) = w(P)$. But $P$ was assumed to be a *shortest* $i \to j$ path — contradiction. So $Q$ must be shortest. By the identical argument (symmetric roles), $R$ must be shortest too. $\blacksquare$

This is the same "optimal substructure" property that underlies **every** shortest-path DP (Bellman-Ford, Dijkstra's greedy proof, Floyd-Warshall) — Generalized BE 1 is just the statement made explicit for an *arbitrary* cut point, not only the last edge.

### Generalized BE 2 (a converse / constructive direction — the basis of squaring)

Setup:
- Let $Q_{ik}$ = a shortest path from $i$ to $k$ using **at most $l$ edges**.
- Let $R_{kj}$ = a shortest path from $k$ to $j$ using **at most $l$ edges**.
- Let $W(i,j,k)$ = the walk formed by concatenating $Q_{ik}$ and $R_{kj}$ (so it uses $\leq 2l$ edges total, and passes through $k$).
- Let $P(i,j)$ = the shortest among $\{ W(i,j,k) : k = 1, \dots, n \}$ (best choice of "meeting point" $k$).

**Claim:** Then:
1. $P(i,j)$ is a genuine **simple path** (not merely a walk with repeated vertices).
2. $P(i,j)$ is a true **shortest path** from $i$ to $j$ (over *all* paths, not just walks through some $k$).
3. $P(i,j)$ uses **at most $2l$ edges**.

**Proof sketch:**
- **(3)** is immediate: each $W(i,j,k)$ has $\leq l + l = 2l$ edges by construction, so their best is also $\leq 2l$ edges.
- **(2)**: Let $P^*$ be a *true* shortest $i \to j$ path using $\leq 2l$ edges (exists since $\delta_{\leq 2l}(i,j)$ is well-defined). $P^*$ has at most $2l$ edges, so **some prefix of $P^*$ has $\leq l$ edges and the corresponding suffix also has $\leq l$ edges** (split at the vertex reached after $\leq l$ edges — call it $k_0$). By Generalized BE 1 (applied with the edge-count-bounded version), each half of $P^*$ is itself a shortest bounded-length path — i.e., $P^*$'s prefix has weight $\geq w(Q_{i,k_0})$ and suffix has weight $\geq w(R_{k_0,j})$, so $w(P^*) \geq w(W(i,j,k_0)) \geq w(P(i,j))$ (since $P(i,j)$ is the best over *all* $k$, including $k_0$). Combined with the trivial bound $w(P(i,j)) \geq w(P^*)$ (since $P^*$ is a shortest path and $P(i,j)$ is *some* valid $i\to j$ walk of $\le 2l$ edges), we get $w(P(i,j)) = w(P^*) = \delta_{\leq 2l}(i,j)$.
- **(1)**: Since $P(i,j)$'s weight equals the weight of an actual shortest path $P^*$ and there are no negative cycles, $P(i,j)$ cannot contain a positive-or-zero-weight cycle either (that would only add non-negative weight without changing $i \to j$ reachability, contradicting minimality unless the cycle has weight exactly 0 — and even then a 0-cycle can be deleted without increasing weight, so WLOG the minimizer is simple). Hence $P(i,j)$ can be taken as a genuine simple path. $\blacksquare$

**Why this matters:** this proves that **combining two half-length optimal solutions at every possible midpoint $k$, then taking the best, yields a full-length optimal solution** — this is *exactly* the mathematical justification for **squaring** in min-plus matrix powers (Section 6–7, and formalized next in Section 9).

---

## 9. Squaring for Extended BE & Algorithm 3

Generalized BE 2 directly gives an equation for **doubling** the edge-budget $l$:

$$
D^{(2l)}_{ij} = \min_k \left\{ D^{(l)}_{ik} + D^{(l)}_{kj} \right\}
$$

In min-plus matrix notation (squaring $D^{(l)}$ against **itself**, not against $W$):

$$
D^{(2l)} = D^{(l)} \cdot D^{(l)} = \big(D^{(l)}\big)^2
$$

with base case $D^{(1)}_{ij} = w_{ij}$, i.e. $D^{(1)} = W$.

So repeated squaring gives:
$$
D^{(1)} \to D^{(2)} \to D^{(4)} \to D^{(8)} \to \cdots \to D^{(2^i)} \to \cdots
$$

**This is a different (and more direct) route than Algorithm 2**: instead of squaring $W$ and then multiplying once by $D^{(0)}$, we square $D$ itself at every step (this is possible precisely because of Generalized BE 2: doubling a *shortest-path* matrix by squaring it gives another valid *shortest-path* matrix, not just an upper bound).

### Algorithm 3

Goal: compute $D^{(n-1)}$.

Since $D^{(n-1)} = D^{(l)}$ for all $l \geq n-1$ (idempotence beyond $n-1$, same reasoning as Section 7), and $2^{\lceil \log n \rceil} \geq n > n-1$:

$$
D^{(n-1)} = D^{\left(2^{\lceil \log n \rceil}\right)}
$$

```
Algorithm-3(W):
    D = W                          // D = D^(1)
    for i = 1 to ceil(log n):
        D = D * D                  // min-plus squaring
    return D                       // D = D^(n-1)
```

### Complexity

- $\lceil \log n \rceil$ squarings, each $O(n^3)$.
- **Total: $O(n^3 \log n)$** — same asymptotic bound as Algorithm 2, but conceptually cleaner (single sequence of squarings, no separate "multiply by $D^{(0)}$" step at the end).

> Historical note (from the slides): this algorithm is attributed to **M. Fisher and A. Meyer**.

---

## 10. k-Paths Formulation

This section sets up **Floyd–Warshall** via a completely different decomposition principle: instead of bounding the **number of edges**, we bound the **labels of allowed intermediate vertices**. This is a foundational shift — arguably the most important conceptual idea in this entire unit.

### Definition

A path from $i$ to $j$ is called a **$k$-path** if every *intermediate* vertex on it has a label $\leq k$ (i.e., the intermediate vertices are drawn only from $\{1, 2, \dots, k\}$). Endpoints $i, j$ themselves are **not** restricted by $k$ — only the internal vertices are.

- A **0-path** from $i$ to $j$ has *no* intermediate vertices at all — it's just the direct edge $(i,j)$, if it exists.
- **Monotonicity:** a $k$-path is automatically an $l$-path for every $l > k$ (a valid set of allowed intermediates only grows as $k$ increases).
- $k$ is **independent of $i, j$** — it is a global bound applied uniformly, unlike the $l$ (edge-count) parameter used earlier.
- Since $n$ is the largest vertex label, an $n$-path can use *any* vertex as intermediate — so an $n$-path is simply an unrestricted shortest path:
$$
\delta_n(i,j) = \delta(i,j) \quad \forall i, j \in V
$$

Let $\delta_k(i,j)$ = weight of the shortest **$k$-path** from $i$ to $j$. Note also $\delta_0(i,j) = w(i,j)$ (matches the extended weight matrix exactly).

Define $A^{(k)} = [a^{(k)}_{ij}]_{n \times n}$ where $a^{(k)}_{ij} = \delta_k(i,j)$.

### The recurrence: does the optimal $k$-path use vertex $k$?

A shortest $k$-path from $i$ to $j$ either **uses vertex $k$** as an intermediate, or it **doesn't**. These are the only two cases — analyze each.

**Case A: doesn't use $k$.**
Then all intermediates are $\leq k-1$, so it's actually a $(k-1)$-path. Hence:
$$
\delta_k(i,j) = \delta_{k-1}(i,j) \tag{5}
$$

**Case B: uses $k$.**
Since a shortest path never repeats a vertex (no negative cycles), $k$ occurs **exactly once**. Split the path at $k$: $i \rightsquigarrow k \rightsquigarrow j$. Both halves have all their (other) intermediates $\leq k-1$ (since $k$ itself is used only once, as the splitting point, and by assumption no other intermediate exceeds $k$), so **both halves are $(k-1)$-paths**.

**Sub-claim: both halves are the *shortest* $(k-1)$-paths between their respective endpoints.**
This follows from the exact same cut-and-paste / optimal-substructure argument as Generalized BE 1 (Section 8) — if either half weren't optimal among $(k-1)$-paths, we could substitute a better one and improve the whole path, contradicting optimality.

So:
$$
\delta_k(i,j) = \delta_{k-1}(i,k) + \delta_{k-1}(k,j) \tag{6}
$$

**Combining (5) and (6):** since the true shortest $k$-path is the *better* of "use $k$" vs "don't use $k$":

$$
\boxed{\delta_k(i,j) = \min\big\{\ \delta_{k-1}(i,j),\ \ \delta_{k-1}(i,k) + \delta_{k-1}(k,j)\ \big\}} \tag{7}
$$

This is the **Floyd–Warshall recurrence**.

### Complexity of extending by one $k$

Computing $a^{(k)}_{ij}$ from Eq. (7) needs only **3 lookups** ($\delta_{k-1}(i,j)$, $\delta_{k-1}(i,k)$, $\delta_{k-1}(k,j)$), **one addition**, **one comparison** — this is $O(1)$ work **per cell**, a huge improvement over the Bellman-equation recurrence which needed $O(n)$ work per cell (minimizing over all $k$ simultaneously).

- $n^2$ cells per matrix $A^{(k)}$ → $O(n^2)$ to compute a full $A^{(k)}$ from $A^{(k-1)}$.
- Sequence $A^{(0)} \to A^{(1)} \to \cdots \to A^{(n)}$ needs $n$ such steps.

$$
\boxed{\text{Total: } O(n) \times O(n^2) = O(n^3)}
$$

This is a **cleaner $O(n^3)$** than Algorithms 2/3's $O(n^3\log n)$ — no $\log n$ factor at all! This is because the $k$-path decomposition needs only $O(1)$ work per cell per step (vs $O(n)$ for the edge-count-based Bellman equation), even though it takes $n$ steps instead of $\log n$ — net win: $n \cdot n^2$ beats $\log n \cdot n^3$.

---

## 11. Algorithm 4 — Floyd–Warshall

```
Floyd-Warshall(G, W):
    A(0) = W

    for k = 1 to n:
        for i = 1 to n:
            for j = 1 to n:
                a_ij^(k) = min( a_ij^(k-1),  a_ik^(k-1) + a_kj^(k-1) )

    return A(n)     // a_ij^(n) = delta(i,j)
```

**Correctness:** directly from Eq. (7), with $A^{(n)} = \delta$ since every vertex is now an admissible intermediate.

**Complexity:** $O(n^3)$, independent of the number of edges $m$ — this holds **even for sparse graphs** (the algorithm doesn't "know" or exploit sparsity; it always does $\Theta(n^3)$ work).

**Space:** each $A^{(k)}$ can be computed in-place, overwriting $A^{(k-1)}$ — you don't need to keep all $n+1$ matrices simultaneously (this is a standard implementation optimization; a small subtlety is that overwriting is *safe* here because $a^{(k)}_{ik}$ and $a^{(k)}_{kj}$ don't actually change from $a^{(k-1)}_{ik}, a^{(k-1)}_{kj}$ — using $k$ itself as the "meeting point" for a path from $i$ to $k$ or $k$ to $j$ never improves on the direct $(k-1)$-path value, since it would require a *sub-loop* through $k$).

**Why Floyd–Warshall is the "default" $O(n^3)$ algorithm in practice:** compared to Algorithms 2/3, it has a strictly better constant (no $\log n$ factor, only $O(1)$ inner work), is trivially implementable with a triple loop, needs no matrix-multiplication machinery, and directly generalizes to path *reconstruction* (by also storing predecessor pointers updated whenever the `min` picks the second branch).

---

## 12. Johnson's Algorithm

### Motivation

Floyd–Warshall's $O(n^3)$ is **independent of $m$** (edge count) — for **sparse graphs** (small $m$), this "wastes" the sparsity. Can we do better when $m \ll n^2$?

Two candidate building blocks:
- **All-positive weights:** run Dijkstra $n$ times (once per source). Using a good priority queue:
$$
O\big(n[\log n + m]\big) = O(n^2 \log n + nm)
$$
This is **asymptotically better than $O(n^3)$ when $m$ is small** (sparse graphs) — a clear win.
- **Negative weights allowed (no negative cycles):** Dijkstra's greedy proof breaks with negative edges, so we'd need Bellman-Ford $n$ times instead:
$$
O(n \cdot n \cdot m) = O(n^2 m)
$$
For **dense** graphs ($m = O(n^2)$), this degrades to $O(n^4)$ — *worse* than Floyd–Warshall!

**The problem:** we want Dijkstra's speed, but Dijkstra requires non-negative weights, and we may have negative edges (just no negative *cycles*).

**Johnson's insight:** *transform* the edge weights into an equivalent non-negative set of weights (so Dijkstra becomes legal), run Dijkstra $n$ times on the transformed graph, then *convert the answer back*. The one-time transformation cost is paid via a **single** Bellman-Ford run.

### Weight transformation — the theory

Let $h : V \to \mathbb{Z}$ be *any* function ("potential function"). Define a new weight function:

$$
w'(u,v) = w(u,v) + h(u) - h(v)
$$

**Claim 1: shortest paths are preserved (as paths).** $P$ is a shortest $i\to j$ path under $w$ **iff** it is a shortest $i \to j$ path under $w'$.

**Claim 2: cycle weights are unaffected.** For any cycle $c = v_1 \to v_2 \to \cdots \to v_1$ in $G$: $w(c) = w'(c)$.

**Claim 3 (telescoping): for any path $P$ from $i$ to $j$:**
$$
w(P) = w'(P) + h(j) - h(i)
$$

**Proofs.**

*Claim 3 first (it implies the other two):* Write $P = v_0(=i) \to v_1 \to \cdots \to v_m(=j)$. Then:
$$
w'(P) = \sum_{t=1}^m w'(v_{t-1}, v_t) = \sum_{t=1}^m \big[ w(v_{t-1},v_t) + h(v_{t-1}) - h(v_t) \big] = w(P) + \sum_{t=1}^m \big[h(v_{t-1}) - h(v_t)\big]
$$
The sum $\sum_{t=1}^m [h(v_{t-1}) - h(v_t)]$ **telescopes**: every interior $h(v_t)$ cancels with its neighbor, leaving $h(v_0) - h(v_m) = h(i) - h(j)$. So:
$$
w'(P) = w(P) + h(i) - h(j) \ \Longrightarrow\ w(P) = w'(P) + h(j) - h(i). \qquad \blacksquare
$$

*Claim 2 follows immediately:* for a cycle, $i = j$ (start = end), so $h(j) - h(i) = 0$, giving $w(c) = w'(c)$ directly. $\blacksquare$

*Claim 1 follows from Claim 3:* for **any fixed pair** $(i,j)$, the quantity $h(j) - h(i)$ is a **constant** (doesn't depend on which path $P$ you pick between $i$ and $j$). So $w(P) = w'(P) + \text{const}$ means *minimizing $w(P)$ over all paths $P$* is **equivalent** to *minimizing $w'(P)$* — the same $P$ minimizes both. $\blacksquare$

**Why this matters:** we're free to choose $h$ however we like, and the *shortest paths themselves don't change* — only their reported weight shifts by a constant (which we can undo later via Claim 3). So the strategy is: **choose $h$ specifically to make $w'(e) \geq 0$ for every edge**, run Dijkstra under $w'$, then convert weights back using Claim 3.

### Constructing $h$ so that $w'(e) \geq 0$ everywhere

**Construction:** Introduce a new vertex $s \notin V$. Build $G' = (V', E')$ with $V' = V \cup \{s\}$ and $E' = E \cup \{(s,i) : i \in V\}$, where every new edge has weight $w(s,i) = 0$.

Solve **Single-Source Shortest Path (SSSP)** on $G'$ from source $s$ (using **Bellman-Ford**, since $G$ may have negative edges — this is the *one* place negative weights are handled directly). Define:

$$
h(i) := \delta(s,i) \quad \text{(computed in } G', \text{ which is the same as in } G \text{ since the new edges have weight 0)}
$$

**Well-defined:** since every vertex $i \in V$ has a direct 0-weight edge from $s$, $\delta(s,i)$ is always finite (assuming no negative cycles reachable from $s$, which holds iff $G$ itself has none, since $s$'s only outgoing edges have weight 0 and don't participate in any cycle).

**Claim: $w'(i,j) = w(i,j) + h(i) - h(j) \geq 0$ for every edge $(i,j) \in E$.**

**Proof.** By the defining property of shortest-path distances (triangle inequality for $\delta$):
$$
\delta(s,j) \leq \delta(s,i) + w(i,j) \quad \text{(since } i \to j \text{ is a valid edge, extending any } s\to i \text{ path)}
$$
i.e.
$$
h(j) \leq h(i) + w(i,j) \implies w(i,j) + h(i) - h(j) \geq 0
$$
which is exactly $w'(i,j) \geq 0$. $\blacksquare$

### Detecting negative cycles

If $G$ has a negative-weight cycle, then $G'$ contains the exact same cycle (all original edges + weights are preserved in $G'$, we only *added* edges out of $s$). So running Bellman-Ford on $G'$ will correctly detect it — **report "negative cycle exists" and halt**; do not proceed to the Dijkstra phase (the whole transformation argument assumed no negative cycles).

### Full Algorithm

```
Johnson(G, w):
    1. Build G' by adding vertex s and 0-weight edges (s,i) for all i in V.
    2. Run Bellman-Ford on G' from s.
         If a negative cycle is detected → report "negative cycle" and STOP.
         Else set h(i) = δ(s,i) for all i in V.
    3. Reweight every edge: w'(i,j) = w(i,j) + h(i) - h(j)   for (i,j) in E
       (and w'(i,i) = w(i,i) = 0 trivially).
    4. For each vertex i in V:
           run Dijkstra on G with weights w', source i
           → obtain δ'(i, j) for all j
    5. Recover true distances:
           δ(i,j) = δ'(i,j) + h(j) - h(i)      (Claim 3, rearranged)
    6. Return the n×n matrix D = [δ(i,j)]
```

### Complexity accounting

| Step | Cost |
|---|---|
| Build $G'$ | $O(n + m)$ |
| Bellman-Ford once on $G'$ | $O(nm)$ |
| Reweight all edges | $O(m)$ |
| Dijkstra $n$ times (with binary/Fibonacci heap) | $O(n[\,n\log n + m\,]) = O(n^2 \log n + nm)$ |
| Recover true distances (Step 5, over all $n^2$ pairs) | $O(n^2)$ |

**Total:**
$$
O(mn) + O(n^2 \log n + nm) + O(n^2) = \boxed{O(n^2 \log n + nm)}
$$

### Comparison with Floyd–Warshall

| Regime | Floyd–Warshall | Johnson's |
|---|---|---|
| Sparse graph ($m = O(n)$) | $O(n^3)$ | $O(n^2 \log n)$ — **strictly better** |
| Dense graph ($m = O(n^2)$) | $O(n^3)$ | $O(n^3)$ — **same order** |

So Johnson's algorithm is a **strict asymptotic improvement whenever the graph is sparse**, and never worse (up to constants) even in the dense case.

---

## 13. Master Comparison Table

| # | Algorithm | Core Idea | Recurrence Parameter | Complexity | Handles Negative Edges? |
|---|---|---|---|---|---|
| 1 | Algorithm 1 (basic DP) | Increase edge-budget $l$ by 1 each round | $l$: # edges | $O(n^4)$ | Yes (no neg. cycle) |
| 2 | Algorithm 2 (squaring $W$) | Repeated squaring of $W$, then one multiply by $D^{(0)}$ | $l \to 2l$ (powers of 2) | $O(n^3 \log n)$ | Yes |
| 3 | Algorithm 3 (squaring $D$) | Repeated squaring of $D$ directly (Generalized BE 2) | $l \to 2l$ | $O(n^3 \log n)$ | Yes |
| 4 | Floyd–Warshall | Increase allowed-intermediate bound $k$ by 1 each round | $k$: max intermediate label | $O(n^3)$ | Yes |
| 5 | Johnson's | Reweight to non-negative, then $n \times$ Dijkstra | (uses Bellman-Ford once + Dijkstra $\times n$) | $O(n^2 \log n + nm)$ | Yes (via reweighting) |

**Key structural insight tying it all together:** Algorithms 1–3 all use the *same underlying recurrence* (the Bellman equation), differing only in **how fast they walk from $D^{(0)}$ to $D^{(n-1)}$** (linearly vs. via doubling). Floyd–Warshall uses a **fundamentally different recurrence** (bounding *vertex labels* instead of *edge counts*), which turns out to need $O(1)$ work per cell instead of $O(n)$ — trading a linear number of *cheap* rounds for a logarithmic number of *expensive* rounds, and winning. Johnson's algorithm sidesteps the whole matrix-DP framework and goes back to single-source algorithms, using the weight-transformation trick specifically to unlock Dijkstra's superior heap-based complexity while still correctly handling negative edges.

---

## 14. Quiz — Test Yourself

### Q1. In the extended weight matrix $W$, what is $w_{ij}$ when $(i,j) \notin E$ and $i \neq j$?

**A.** $0$
**B.** $-1$
**C.** $\infty$
**D.** Undefined

**Answer: C.**
**Explanation:** The extended weight function sets $w(u,v) = \infty$ whenever there's no direct edge from $u$ to $v$ (and $u \ne v$), signaling "this direct step is not usable." This convention is also exactly the identity element for the min-plus semiring's "$+$" operation (Section 6) — a non-edge should behave like "no contribution" under $\min$, which forces it to be $+\infty$.

---

### Q2. Under the min-plus (tropical) semiring reinterpretation used to connect Bellman's equation to matrix multiplication, what do "$+$" and "$\cdot$" represent respectively?

**A.** "$+$" = sum, "$\cdot$" = min
**B.** "$+$" = min, "$\cdot$" = sum
**C.** "$+$" = max, "$\cdot$" = sum
**D.** "$+$" = min, "$\cdot$" = min

**Answer: B.**
**Explanation:** The reinterpretation sets $a+b := \min(a,b)$ and $a \cdot b := a + b$ (ordinary sum). This turns the Bellman recurrence $d^{(l)}_{ij} = \min_k\{d^{(l-1)}_{ik} + w_{kj}\}$ into $\sum_k d^{(l-1)}_{ik} \cdot w_{kj}$, which is syntactically identical to the standard matrix-multiplication formula $c_{ij} = \sum_k a_{ik}b_{kj}$.

---

### Q3. What is the "zero element" (additive identity) for the min-plus semiring, and why?

**A.** $0$, because $\min(a,0) = a$ always
**B.** $\infty$, because $\min(a,\infty) = a$ for all $a$
**C.** $-\infty$, because it's the smallest possible value
**D.** There is no identity element

**Answer: B.**
**Explanation:** We need $e$ such that $\min(a,e) = a$ for every $a$. Only $e = \infty$ satisfies this (any finite $e$ could potentially be smaller than some $a$, changing the min). This directly explains why non-edges get weight $\infty$ in $W$.

---

### Q4. Algorithm 2 computes $D^{(n-1)}$ as $D^{(0)}W^{t}$ for some $t$. Which of these best describes the choice of $t$?

**A.** $t = n - 1$ exactly, computed via $n-2$ successive multiplications
**B.** $t$ is the smallest power of 2 that is $\geq n-1$, computed via repeated squaring
**C.** $t = \log n$, computed directly
**D.** $t$ is chosen randomly and refined iteratively

**Answer: B.**
**Explanation:** Since $D^{(n-1)} = D^{(t)}$ for **any** $t \geq n-1$ (idempotence: extra edge-budget beyond $n-1$ never changes the shortest-path weight, as no negative cycles exist), we pick the smallest power of 2 that's $\ge n-1$ — this lets us reach $W^t$ in only $\lceil \log n \rceil$ squarings instead of $t-1$ sequential multiplications.

---

### Q5. What is the total time complexity of Algorithm 2 (squaring $W$)?

**A.** $O(n^3)$
**B.** $O(n^4)$
**C.** $O(n^3 \log n)$
**D.** $O(n^2 \log n)$

**Answer: C.**
**Explanation:** Each squaring of an $n\times n$ matrix under min-plus multiplication costs $O(n^3)$ (same asymptotic cost as ordinary matrix multiplication, computed the naive $O(n^3)$ way). There are $\lceil \log n\rceil + 1$ total multiplications ($\lceil \log n \rceil$ squarings + 1 final multiply by $D^{(0)}$), giving $O(n^3 \log n)$ overall.

---

### Q6. Generalized Bellman Equation 1 states that if $k$ is an intermediate vertex on a shortest path $P$ from $i$ to $j$, then:

**A.** $k$ must be adjacent to both $i$ and $j$
**B.** The $i \to k$ and $k \to j$ portions of $P$ are themselves shortest paths
**C.** $k$ must be the midpoint of $P$ in terms of edge count
**D.** $P$ cannot be a simple path

**Answer: B.**
**Explanation:** This is the classic optimal-substructure property, proven by a cut-and-paste contradiction argument: if either sub-path weren't optimal, you could splice in a cheaper sub-path and produce a strictly shorter $i \to j$ walk — contradicting that $P$ was already shortest.

---

### Q7. In Generalized BE 2, what guarantees that $P(i,j)$ — the best among all $W(i,j,k)$ for $k = 1,\dots,n$ — is a *simple path* (property 1) and not just a walk?

**A.** It's assumed by definition, no proof needed
**B.** Because no negative cycles exist, any weight-minimal candidate can be taken to be simple (cycles can only be removed without increasing weight)
**C.** Because $k$ is chosen to be the smallest-labeled vertex
**D.** Simple paths are not actually guaranteed; the claim is approximate

**Answer: B.**
**Explanation:** Since there are no negative-weight cycles, any cycle embedded in a candidate walk has weight $\geq 0$; removing it never increases total weight and can only help minimality. Hence among weight-minimizing candidates, a simple one always exists / is achieved.

---

### Q8. Why does squaring $D^{(l)}$ directly (Algorithm 3) rely specifically on Generalized BE 2 rather than the basic Bellman equation?

**A.** It doesn't — Algorithm 3 uses the basic Bellman equation unchanged
**B.** Generalized BE 2 justifies that combining two *bounded-length optimal* sub-solutions at an arbitrary meeting vertex $k$ (not just "one edge before the end") yields a new bounded-length optimal solution — this is exactly what "squaring $D$" computes
**C.** Because Generalized BE 2 removes the need for a weight matrix entirely
**D.** Because Generalized BE 2 only applies to non-negative weights

**Answer: B.**
**Explanation:** The basic Bellman equation combines an $(l-1)$-edge sub-solution with a single extra edge. Algorithm 3's squaring step $D^{(2l)}_{ij} = \min_k\{D^{(l)}_{ik}+D^{(l)}_{kj}\}$ combines **two full $l$-edge sub-solutions** at a meeting point $k$ — this doubling step is precisely what Generalized BE 2 proves is valid (the concatenation of two optimal bounded-length paths, minimized over meeting points, gives a true optimum).

---

### Q9. What is a "$k$-path" from $i$ to $j$?

**A.** A path using exactly $k$ edges
**B.** A path whose intermediate vertices all have labels $\leq k$
**C.** A path passing through exactly $k$ intermediate vertices
**D.** The $k$-th shortest path between $i$ and $j$

**Answer: B.**
**Explanation:** The $k$-path definition restricts only the *labels* of intermediate (internal) vertices to $\{1, \dots, k\}$ — it does **not** restrict the number of edges, and the endpoints $i,j$ are unrestricted by this bound (even if their labels exceed $k$).

---

### Q10. What is the Floyd–Warshall recurrence for $\delta_k(i,j)$ in terms of $\delta_{k-1}$?

**A.** $\delta_k(i,j) = \delta_{k-1}(i,j) + \delta_{k-1}(i,k)$
**B.** $\delta_k(i,j) = \min\{\delta_{k-1}(i,j),\ \delta_{k-1}(i,k) + \delta_{k-1}(k,j)\}$
**C.** $\delta_k(i,j) = \max\{\delta_{k-1}(i,j),\ \delta_{k-1}(i,k) + \delta_{k-1}(k,j)\}$
**D.** $\delta_k(i,j) = \delta_{k-1}(i,k) \cdot \delta_{k-1}(k,j)$

**Answer: B.**
**Explanation:** A shortest $k$-path either avoids vertex $k$ entirely (in which case it equals the shortest $(k-1)$-path, i.e. $\delta_{k-1}(i,j)$) or uses $k$ exactly once as a meeting point (in which case it equals $\delta_{k-1}(i,k) + \delta_{k-1}(k,j)$, since both halves must themselves be shortest $(k-1)$-paths by the same cut-and-paste argument as Generalized BE 1). The true $\delta_k(i,j)$ is the minimum of these two cases.

---

### Q11. Why is Floyd–Warshall's per-cell update ($O(1)$) cheaper than the per-cell update in Algorithm 1/2/3 ($O(n)$)?

**A.** Floyd–Warshall uses a faster programming language
**B.** The $k$-path recurrence only ever needs to compare **two** candidates (use $k$ or don't), whereas the edge-count-based Bellman equation must minimize over **all $n$ choices** of intermediate vertex $k$ at every step
**C.** Floyd–Warshall skips vertices with high degree
**D.** They are not actually different — both are $O(n)$ per cell

**Answer: B.**
**Explanation:** In the edge-count recurrence, extending from $l-1$ to $l$ edges requires trying *every* possible last-edge predecessor $k \in \{1,\dots,n\}$ — genuinely $O(n)$ work. In the $k$-path recurrence, extending the *allowed intermediate set* from $\{1,\dots,k-1\}$ to $\{1,\dots,k\}$ only ever needs to ask "does adding $k$ as a newly-available intermediate help, via exactly this one new vertex?" — a fixed $O(1)$ comparison, because only vertex $k$ is new; the possibility of using any smaller-labeled vertex is already baked into $\delta_{k-1}$.

---

### Q12. What is Floyd–Warshall's total time complexity, and how does it compare to the number of edges $m$?

**A.** $O(nm)$, so it improves for sparse graphs
**B.** $O(n^3)$, and it does **not** depend on $m$ at all
**C.** $O(m \log n)$
**D.** $O(n^2 m)$

**Answer: B.**
**Explanation:** Floyd–Warshall always performs $n$ rounds of $O(n^2)$ work each (triple nested loop over $k,i,j$), regardless of how many edges actually exist — even a graph with only $n-1$ edges costs the same $O(n^3)$ as a complete graph. This is precisely the gap that Johnson's algorithm exploits for sparse graphs.

---

### Q13. Why can't we simply run Dijkstra $n$ times directly on a graph with negative edge weights (but no negative cycles) to solve APSP?

**A.** Dijkstra's greedy selection of the minimum-distance unvisited vertex assumes that once a vertex is finalized, its distance cannot be improved later — but a negative edge encountered later can invalidate this, since it may still offer a shorter path
**B.** Dijkstra only works on undirected graphs
**C.** Dijkstra cannot handle more than one source vertex
**D.** Negative weights cause Dijkstra to run in exponential time

**Answer: A.**
**Explanation:** Dijkstra's correctness proof relies on the invariant that the frontier vertex with the smallest tentative distance is already final — this only holds when all edge weights are non-negative (so no future edge relaxation can ever produce a smaller distance than one already finalized). Negative edges break this invariant, potentially producing incorrect ("too large") finalized distances.

---

### Q14. In Johnson's algorithm, what is the purpose of introducing the auxiliary vertex $s$ and edges $(s,i)$ with weight 0?

**A.** To make the graph undirected
**B.** To provide a common reference point from which a potential function $h$ can be computed via a single Bellman-Ford run, without changing any of $G$'s original path weights (since $s$'s edges have weight 0 and $s$ has no incoming edges, so $s$ cannot appear on any cycle or internal path of $G$)
**C.** To detect cycles that don't actually exist in $G$
**D.** To reduce the number of vertices needed for Dijkstra

**Answer: B.**
**Explanation:** We need a function $h: V \to \mathbb{Z}$ satisfying $w(i,j)+h(i)-h(j)\geq 0$ for every edge. Setting $h(i) = \delta(s,i)$ (shortest distance from a universal zero-cost source) makes this true automatically, via the triangle inequality $\delta(s,j)\leq \delta(s,i)+w(i,j)$. Since $s$ only has outgoing edges (no incoming), it cannot be part of any cycle in $G$, and $G'$'s structure otherwise exactly mirrors $G$ — so any negative cycle in $G$ is faithfully detected by Bellman-Ford on $G'$.

---

### Q15. Prove-style question: Given $w'(u,v) = w(u,v) + h(u) - h(v)$, show why $w(P) = w'(P) + h(j) - h(i)$ for any path $P$ from $i$ to $j$. What algebraic property makes this work?

**Answer & Explanation:**
Write $P: v_0 (=i) \to v_1 \to \cdots \to v_m (=j)$. Summing the transformed weights along $P$:
$$
w'(P) = \sum_{t=1}^{m} \big[w(v_{t-1},v_t) + h(v_{t-1}) - h(v_t)\big] = w(P) + \sum_{t=1}^m\big[h(v_{t-1})-h(v_t)\big]
$$
The property that makes this work is **telescoping**: the sum $\sum_{t=1}^m[h(v_{t-1})-h(v_t)]$ collapses because every "middle" value of $h$ (at $v_1, v_2, \dots, v_{m-1}$) appears once with a $+$ sign (as $h(v_{t-1})$ in term $t$) and once with a $-$ sign (as $h(v_t)$ in term $t+1$), cancelling exactly. Only the two endpoints survive: $h(v_0) - h(v_m) = h(i) - h(j)$. Hence $w'(P) = w(P) + h(i) - h(j)$, which rearranges to the stated formula. This telescoping is *why the extra $h(i)-h(j)$ term is a constant that doesn't depend on the internal route taken* — so minimizing $w'(P)$ over all paths is equivalent to minimizing $w(P)$.

---

### Q16. What is the total time complexity of Johnson's algorithm, and under what condition does it beat Floyd–Warshall?

**A.** $O(n^2 \log n + nm)$; beats Floyd–Warshall ($O(n^3)$) whenever the graph is sparse (i.e., $m = o(n^2/\log n)$, and in particular for $m = O(n)$)
**B.** $O(n^3)$ always; never beats Floyd–Warshall
**C.** $O(nm)$; beats Floyd–Warshall only for dense graphs
**D.** $O(n^2)$; always beats Floyd–Warshall

**Answer: A.**
**Explanation:** Johnson's cost is dominated by $n$ runs of Dijkstra, $O(n[n\log n + m]) = O(n^2\log n + nm)$, plus one Bellman-Ford run $O(nm)$ and $O(n^2)$ bookkeeping — all together $O(n^2\log n + nm)$. For dense graphs ($m=\Theta(n^2)$) this reduces to $O(n^3)$, matching Floyd–Warshall. For sparse graphs ($m = O(n)$), it becomes $O(n^2 \log n)$, which is asymptotically **smaller** than $O(n^3)$.

---

### Q17. True or False: If Bellman-Ford, run on $G'$ (the graph with the auxiliary source $s$ added), reports a negative cycle, this necessarily means the *original* graph $G$ has a negative cycle.

**Answer: True.**
**Explanation:** $G'$ is built by adding a new vertex $s$ and *outgoing* 0-weight edges from $s$ — no new edges are added among the original vertices, and $s$ has no incoming edges so it cannot be part of any cycle. Therefore, any cycle detected in $G'$ must lie entirely within the original vertex set $V$ and use only original edges — i.e., it is a genuine cycle of $G$ with the same (negative) weight.

---

### Q18. Consider $D^{(0)}$, the base case of the shortest-paths weight matrix. What are its diagonal and off-diagonal entries?

**A.** Diagonal = $\infty$, off-diagonal = $0$
**B.** Diagonal = $0$, off-diagonal = $\infty$
**C.** All entries = $0$
**D.** All entries = $\infty$

**Answer: B.**
**Explanation:** $D^{(0)}$ represents shortest paths using **at most 0 edges**. The only such "path" from a vertex to itself is the trivial empty path (weight 0). For $i \ne j$, no zero-edge path can connect distinct vertices, so the value is $\infty$ (unreachable within the edge budget) — matching $d^{(0)}_{ij} = 0$ if $i=j$, else $\infty$.

---

### Q19. Why does Algorithm 1 (basic DP) achieve $O(n^4)$ overall, even though each Bellman-equation application only needs $O(n^3)$?

**A.** Because $n-1$ rounds of $D^{(l-1)}\to D^{(l)}$ are each $O(n^3)$, and $(n-1)\cdot O(n^3) = O(n^4)$
**B.** Because the graph has $n^4$ edges in the worst case
**C.** Because Bellman-Ford itself is $O(n^4)$
**D.** It's actually $O(n^3)$, not $O(n^4)$

**Answer: A.**
**Explanation:** Building the full matrix $D^{(l)}$ from $D^{(l-1)}$ costs $O(n^3)$ (each of the $n^2$ cells needs $O(n)$ work to minimize over all choices of intermediate $k$). Algorithm 1 repeats this $n-1$ times (for $l=1,\dots,n-1$), giving $(n-1)\cdot O(n^3) = O(n^4)$ total.

---

### Q20. Rank the four matrix/DP-style APSP algorithms (Algorithm 1, 2, 3, Floyd–Warshall) from slowest to fastest asymptotic complexity, and briefly justify.

**Answer & Explanation:**
$$
\text{Algorithm 1: } O(n^4) \;>\; \text{Algorithm 2 } \approx \text{ Algorithm 3: } O(n^3\log n) \;>\; \text{Floyd–Warshall: } O(n^3)
$$
- **Algorithm 1** is slowest because it advances the edge-budget $l$ by exactly $1$ per round, needing $n-1$ rounds each costing $O(n^3)$.
- **Algorithms 2 and 3** tie, because both use repeated *squaring* (doubling the edge-budget each round instead of incrementing by 1), cutting the number of rounds from $O(n)$ to $O(\log n)$ — but each round still costs a full $O(n^3)$ matrix multiplication.
- **Floyd–Warshall** is fastest among these because its recurrence advances a *different* parameter (allowed intermediate-vertex-label bound $k$) that only requires $O(1)$ work per matrix cell per round (vs. $O(n)$ for the edge-count recurrence), even though it still takes $O(n)$ rounds — net: $O(n)\cdot O(n^2) = O(n^3)$, beating $O(\log n)\cdot O(n^3) = O(n^3\log n)$ since $\log n$'s inner-work saving is even larger than its round-count saving in this comparison.

---

*End of notes. These cover: the extended weight matrix, the shortest-paths weight matrix, both Bellman equations (basic and generalized) with full proofs, the min-plus semiring connection to matrix multiplication (including the zero element), squaring operations and Algorithms 2 & 3 with pseudocode and complexity, the k-path formulation with full derivation of the Floyd–Warshall recurrence, Algorithm 4 (Floyd–Warshall) itself, and Johnson's Algorithm end-to-end including the weight-transformation proofs, construction of the potential function h, and negative-cycle detection.*
