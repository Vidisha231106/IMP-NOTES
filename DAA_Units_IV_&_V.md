# Design and Analysis of Algorithms — Units IV & V
## Complete Exam-Ready Notes

# UNIT IV: Dynamic Programming & Greedy Technique

---

## 1. Dynamic Programming — Core Philosophy

Dynamic Programming (DP) solves problems by breaking them into **overlapping subproblems**, solving each subproblem **once**, and storing results in a table (memoization/tabulation).

**When to use DP:**
- Optimal substructure: optimal solution contains optimal solutions to subproblems
- Overlapping subproblems: same subproblems recur multiple times

**Two approaches:**
| Approach | Direction | Storage | Style |
|---|---|---|---|
| Top-Down (Memoization) | Recursive, lazy | Memo table (sparse) | Natural recursion + cache |
| Bottom-Up (Tabulation) | Iterative, eager | Full DP table | Fill table systematically |

---

## 2. Binomial Coefficient — C(n, k)

### Definition
C(n, k) = number of ways to choose k items from n items.

**Mathematical recurrence:**
```
C(n, k) = C(n-1, k-1) + C(n-1, k)    [Pascal's triangle]
Base:  C(n, 0) = 1,  C(n, n) = 1
```

### Why DP?
Naive recursion recomputes C(3,2) many times when computing C(5,3). DP avoids this.

### DP Table Construction (Bottom-Up)

Build a 2D table `C[i][j]` = C(i, j):

```
     k=0  k=1  k=2  k=3  k=4
n=0 [  1    0    0    0    0 ]
n=1 [  1    1    0    0    0 ]
n=2 [  1    2    1    0    0 ]
n=3 [  1    3    3    1    0 ]
n=4 [  1    4    6    4    1 ]
```

### Algorithm (Pseudocode)
```
BINOMIAL(n, k):
  Create table C[0..n][0..k]
  for i from 0 to n:
    for j from 0 to min(i, k):
      if j == 0 or j == i:
        C[i][j] = 1
      else:
        C[i][j] = C[i-1][j-1] + C[i-1][j]
  return C[n][k]
```

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
using namespace std;

long long binomialCoeff(int n, int k) {
    vector<vector<long long>> C(n+1, vector<long long>(k+1, 0));
    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= min(i, k); j++) {
            if (j == 0 || j == i)
                C[i][j] = 1;
            else
                C[i][j] = C[i-1][j-1] + C[i-1][j];
        }
    }
    return C[n][k];
}

int main() {
    cout << binomialCoeff(5, 2) << endl; // 10
    return 0;
}
```

### Space-Optimized Version (1D array)
```cpp
long long binomialCoeff1D(int n, int k) {
    vector<long long> C(k+1, 0);
    C[0] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = min(i, k); j > 0; j--)  // traverse right to left!
            C[j] += C[j-1];
    return C[k];
}
```

### Complexity
| | Time | Space |
|---|---|---|
| Naive Recursion | O(2^n) | O(n) stack |
| DP (2D table) | O(n·k) | O(n·k) |
| DP (1D optimized) | O(n·k) | O(k) |

---

## 3. Warshall's Algorithm (Transitive Closure)

### Problem
Given a directed graph G, find if there is a path from vertex i to vertex j for all pairs (i, j).

### Key Idea
Build matrix R where `R[i][j] = 1` if there is a path from i to j.

**Recurrence:**
```
R^(k)[i][j] = R^(k-1)[i][j]  OR  (R^(k-1)[i][k] AND R^(k-1)[k][j])
```
Meaning: either there's already a path from i to j, OR there's a path from i to k AND from k to j (using vertex k as intermediate).

### Algorithm
```
WARSHALL(A, n):          // A = adjacency matrix
  R = A                  // copy input
  for k from 1 to n:
    for i from 1 to n:
      for j from 1 to n:
        R[i][j] = R[i][j] OR (R[i][k] AND R[k][j])
  return R
```

### Example
Consider 4 vertices with edges: 1→2, 2→4, 3→2, 4→3

Initial A:
```
  1 2 3 4
1[0 1 0 0]
2[0 0 0 1]
3[0 1 0 0]
4[0 0 1 0]
```

After k=1 (use vertex 1 as intermediate): no change (nothing goes through 1)

After k=2:
- (3,4): R[3][2]=1, R[2][4]=1 → R[3][4] = 1

After k=3:
- (2,3): R[2][3] becomes 1 via 4→3
- (4,3) already 1

After k=4:
- (2,3): R[2][4]=1, R[4][3]=1 → R[2][3]=1
- chains propagate

Final R shows reachability for all pairs.

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
using namespace std;

void warshall(vector<vector<int>>& R, int n) {
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                R[i][j] = R[i][j] || (R[i][k] && R[k][j]);
}

int main() {
    int n = 4;
    vector<vector<int>> R = {
        {0,1,0,0},
        {0,0,0,1},
        {0,1,0,0},
        {0,0,1,0}
    };
    warshall(R, n);
    for (auto& row : R) {
        for (int v : row) cout << v << " ";
        cout << endl;
    }
}
```

### Complexity
| | Value |
|---|---|
| Time | O(n³) |
| Space | O(n²) |

**In-place update:** Warshall can safely update R[i][j] in-place because once R[i][k] is true, it stays true for all subsequent j iterations.

---

## 4. Floyd's Algorithm (All-Pairs Shortest Path)

### Problem
Find shortest paths between **all pairs** of vertices in a weighted graph (can have negative weights, but no negative cycles).

### Recurrence
```
D^(k)[i][j] = min(D^(k-1)[i][j],  D^(k-1)[i][k] + D^(k-1)[k][j])
```
D^(k)[i][j] = shortest path from i to j using only vertices {1, 2, ..., k} as intermediates.

**Relationship to Warshall:** Floyd = Warshall but with min/+ instead of OR/AND.

### Algorithm
```
FLOYD(W, n):           // W = weight matrix (∞ if no edge)
  D = W
  for k from 1 to n:
    for i from 1 to n:
      for j from 1 to n:
        D[i][j] = min(D[i][j], D[i][k] + D[k][j])
  return D
```

### Path Reconstruction
```
FLOYD_WITH_PATH(W, n):
  D = W
  P[i][j] = 0 for all i,j    // predecessor matrix
  for k from 1 to n:
    for i from 1 to n:
      for j from 1 to n:
        if D[i][k] + D[k][j] < D[i][j]:
          D[i][j] = D[i][k] + D[k][j]
          P[i][j] = k

PRINT_PATH(P, i, j):
  if P[i][j] != 0:
    PRINT_PATH(P, i, P[i][j])
    print P[i][j]
    PRINT_PATH(P, P[i][j], j)
```

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

const int INF = INT_MAX / 2;

void floyd(vector<vector<int>>& D, int n) {
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                if (D[i][k] != INF && D[k][j] != INF)
                    D[i][j] = min(D[i][j], D[i][k] + D[k][j]);
}

int main() {
    int n = 4;
    vector<vector<int>> D = {
        {0,   5, INF, 10},
        {INF, 0,   3, INF},
        {INF, INF, 0,  1},
        {INF, INF, INF, 0}
    };
    floyd(D, n);
    // D[0][3] = 9 (0→1→2→3: 5+3+1)
}
```

### Complexity
| | Value |
|---|---|
| Time | O(n³) |
| Space | O(n²) |

**Negative cycle detection:** After Floyd, if D[i][i] < 0 for any i, there's a negative cycle.

### Warshall vs Floyd Comparison
| Aspect | Warshall | Floyd |
|---|---|---|
| Purpose | Transitive closure | All-pairs shortest path |
| Edge weights | Binary (0/1) | Weighted (any) |
| Operation | OR / AND | min / + |
| Handles negative? | N/A | Yes (no neg cycle) |
| Time | O(n³) | O(n³) |

---

## 5. 0/1 Knapsack Problem

### Problem Statement
Given n items, each with weight w[i] and value v[i], and a knapsack of capacity W. Select items to maximize total value without exceeding W. Each item either included (1) or excluded (0).

### Recurrence
```
V[i][j] = max value using items 1..i with capacity j

V[i][j] = V[i-1][j]                           if w[i] > j  (can't include item i)
V[i][j] = max(V[i-1][j], v[i] + V[i-1][j-w[i]])  if w[i] <= j
Base: V[0][j] = 0, V[i][0] = 0
```

### Example
Items: {(w=2,v=3), (w=3,v=4), (w=4,v=5), (w=5,v=6)}, W=5

DP Table (rows = items 0-4, cols = capacity 0-5):
```
     0  1  2  3  4  5
i=0[ 0  0  0  0  0  0]
i=1[ 0  0  3  3  3  3]   item1: w=2,v=3
i=2[ 0  0  3  4  4  7]   item2: w=3,v=4
i=3[ 0  0  3  4  5  7]   item3: w=4,v=5
i=4[ 0  0  3  4  5  7]   item4: w=5,v=6
```
Answer: V[4][5] = 7

### Algorithm
```
KNAPSACK_01(v[], w[], n, W):
  Create V[0..n][0..W], initialize to 0
  for i from 1 to n:
    for j from 0 to W:
      V[i][j] = V[i-1][j]               // don't take item i
      if w[i] <= j:
        V[i][j] = max(V[i][j], v[i] + V[i-1][j - w[i]])
  return V[n][W]
```

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int knapsack(vector<int>& v, vector<int>& w, int n, int W) {
    vector<vector<int>> dp(n+1, vector<int>(W+1, 0));
    for (int i = 1; i <= n; i++) {
        for (int j = 0; j <= W; j++) {
            dp[i][j] = dp[i-1][j];
            if (w[i-1] <= j)
                dp[i][j] = max(dp[i][j], v[i-1] + dp[i-1][j - w[i-1]]);
        }
    }
    return dp[n][W];
}

// Item reconstruction: trace back through table
void findItems(vector<vector<int>>& dp, vector<int>& v, vector<int>& w, int n, int W) {
    int j = W;
    for (int i = n; i >= 1; i--) {
        if (dp[i][j] != dp[i-1][j]) {
            cout << "Item " << i << " selected\n";
            j -= w[i-1];
        }
    }
}

int main() {
    vector<int> v = {3, 4, 5, 6};
    vector<int> w = {2, 3, 4, 5};
    int W = 5, n = 4;
    cout << "Max value: " << knapsack(v, w, n, W) << endl; // 7
}
```

### Space-Optimized (1D)
```cpp
int knapsack1D(vector<int>& v, vector<int>& w, int n, int W) {
    vector<int> dp(W+1, 0);
    for (int i = 0; i < n; i++)
        for (int j = W; j >= w[i]; j--)  // MUST go right to left
            dp[j] = max(dp[j], v[i] + dp[j - w[i]]);
    return dp[W];
}
```

### Complexity
| | Time | Space |
|---|---|---|
| Naive recursion | O(2^n) | O(n) |
| DP 2D | O(n·W) | O(n·W) |
| DP 1D | O(n·W) | O(W) |

**Note:** O(n·W) is pseudo-polynomial — polynomial in W's value, but W can be exponential in the number of bits representing it. 0/1 Knapsack is NP-Hard.

---

## 6. Memory Functions (Top-Down DP with Memoization)

### Motivation
Bottom-up DP fills the entire table even if many subproblems aren't needed. Memory functions combine:
- Top-down recursion's laziness (only compute what's needed)
- DP's efficiency (never recompute)

### Structure
```
MEMO_FUNCTION(parameters):
  if memo[parameters] is already computed:
    return memo[parameters]
  compute result recursively
  store in memo[parameters]
  return result
```

### Memory Function for 0/1 Knapsack
```cpp
#include <iostream>
#include <vector>
using namespace std;

const int UNINIT = -1;
vector<vector<int>> memo;

int knapsackMemo(vector<int>& v, vector<int>& w, int i, int j) {
    if (i == 0 || j == 0) return 0;
    if (memo[i][j] != UNINIT) return memo[i][j];  // already solved
    
    int exclude = knapsackMemo(v, w, i-1, j);
    int include = 0;
    if (w[i-1] <= j)
        include = v[i-1] + knapsackMemo(v, w, i-1, j - w[i-1]);
    
    memo[i][j] = max(exclude, include);
    return memo[i][j];
}

int main() {
    vector<int> v = {3, 4, 5, 6};
    vector<int> w = {2, 3, 4, 5};
    int n = 4, W = 5;
    memo.assign(n+1, vector<int>(W+1, UNINIT));
    cout << knapsackMemo(v, w, n, W) << endl;
}
```

### When Memory Functions Win Over Bottom-Up
- Sparse subproblem space (many subproblems never needed)
- Problem structure makes it hard to determine fill order
- Recursive formulation is more natural

---

## 7. Greedy Technique — Core Philosophy

Greedy algorithms make the **locally optimal choice** at each step, hoping to find a global optimum. Unlike DP, greedy does not reconsider choices.

**Greedy works when:**
- Greedy choice property: locally optimal → globally optimal
- Optimal substructure: subproblems have optimal solutions

---

## 8. Prim's Algorithm (Minimum Spanning Tree)

### Problem
Find a Minimum Spanning Tree (MST) of a connected, undirected, weighted graph.

**MST:** A spanning tree with minimum total edge weight.

### Prim's Idea
Start from any vertex. Greedily add the cheapest edge that connects the current tree to a new vertex (not yet in tree).

### Algorithm
```
PRIM(G, start):
  Initialize key[v] = ∞ for all v; key[start] = 0
  parent[v] = NIL for all v
  MST_SET = {}
  Priority Queue PQ = all vertices with key values

  while PQ not empty:
    u = EXTRACT_MIN(PQ)          // vertex with minimum key
    add u to MST_SET
    for each neighbor v of u:
      if v not in MST_SET and w(u,v) < key[v]:
        key[v] = w(u,v)
        parent[v] = u
        UPDATE_PRIORITY(PQ, v, key[v])

  MST edges: (parent[v], v) for all v ≠ start
```

### Example
Graph: A-B(4), A-C(2), B-C(1), B-D(5), C-D(8), C-E(10), D-E(2)

Start from A:
- key = {A:0, B:∞, C:∞, D:∞, E:∞}
- Pick A (key=0): update B→4, C→2
- key = {B:4, C:2, D:∞, E:∞}
- Pick C (key=2): update B→1 (via B-C=1 < 4), D→8, E→10
- key = {B:1, D:8, E:10}
- Pick B (key=1): update D→5
- key = {D:5, E:10}
- Pick D (key=5): update E→2
- key = {E:2}
- Pick E → done

MST edges: C-A(2), B-C(1), D-B(5), E-D(2) → Total = 10

### C++ Implementation (Priority Queue)
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;

typedef pair<int,int> pii; // (weight, vertex)

int prim(vector<vector<pii>>& adj, int n) {
    vector<int> key(n, INT_MAX);
    vector<bool> inMST(n, false);
    priority_queue<pii, vector<pii>, greater<pii>> pq;

    key[0] = 0;
    pq.push({0, 0});
    int totalWeight = 0;

    while (!pq.empty()) {
        auto [w, u] = pq.top(); pq.pop();
        if (inMST[u]) continue;
        inMST[u] = true;
        totalWeight += w;

        for (auto [weight, v] : adj[u]) {
            if (!inMST[v] && weight < key[v]) {
                key[v] = weight;
                pq.push({key[v], v});
            }
        }
    }
    return totalWeight;
}
```

### Complexity
| Implementation | Time |
|---|---|
| Adjacency Matrix | O(V²) |
| Adjacency List + Binary Heap | O((V+E) log V) |
| Adjacency List + Fibonacci Heap | O(E + V log V) |

Space: O(V + E)

---

## 9. Dijkstra's Algorithm (Single Source Shortest Path)

### Problem
Find shortest paths from a single source vertex to all other vertices in a weighted graph with **non-negative edge weights**.

### Key Idea
Same structure as Prim's, but key[v] = shortest distance from source to v (not just weight of connecting edge).

### Algorithm
```
DIJKSTRA(G, source):
  dist[v] = ∞ for all v; dist[source] = 0
  visited[v] = false for all v
  PQ = min-priority queue with (0, source)

  while PQ not empty:
    u = EXTRACT_MIN(PQ)
    if visited[u]: continue
    visited[u] = true
    
    for each neighbor v of u with edge weight w:
      if dist[u] + w < dist[v]:
        dist[v] = dist[u] + w
        PQ.push(dist[v], v)

  return dist[]
```

### Greedy Choice
At each step, we finalize the vertex with the smallest known distance. Since all weights ≥ 0, this distance can never be improved later (greedy is safe).

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <climits>
using namespace std;
typedef pair<int,int> pii;

vector<int> dijkstra(vector<vector<pii>>& adj, int src, int n) {
    vector<int> dist(n, INT_MAX);
    priority_queue<pii, vector<pii>, greater<pii>> pq;
    dist[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;  // stale entry

        for (auto [w, v] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

### Example (Manual)
Graph: 0→1(4), 0→2(1), 2→1(2), 1→3(1), 2→3(5)

- Start: dist={0:0, 1:∞, 2:∞, 3:∞}
- Process 0: dist={1:4, 2:1}
- Process 2 (dist=1): dist={1: min(4,1+2)=3, 3:1+5=6}
- Process 1 (dist=3): dist={3: min(6,3+1)=4}
- Process 3 (dist=4): done

Result: dist={0:0, 1:3, 2:1, 3:4}

### Complexity
| Implementation | Time |
|---|---|
| Adjacency Matrix | O(V²) |
| Binary Heap + Adj List | O((V+E) log V) |
| Fibonacci Heap | O(E + V log V) |

**Why Dijkstra fails with negative edges:** A later-discovered shorter path could update a "finalized" vertex, violating the greedy assumption. Use Bellman-Ford for negative edges.

### Dijkstra vs Prim Comparison
| Aspect | Dijkstra | Prim |
|---|---|---|
| Goal | Shortest path from source | Minimum spanning tree |
| Key value | dist[source] + edge weight | Just edge weight |
| Result | Distance array | Set of MST edges |
| Works on | Directed/undirected | Undirected |

---

## 10. Huffman Trees and Codes

### Problem
Given a set of characters with frequencies, design a prefix-free binary encoding that minimizes total bits used.

**Prefix-free:** No codeword is a prefix of another (ensures unambiguous decoding).

### Greedy Strategy
Always merge the two least frequent nodes first (optimal substructure proven).

### Algorithm
```
HUFFMAN(C, freq[]):
  PQ = min-priority queue of (freq, node) for each char
  while PQ.size() > 1:
    left = EXTRACT_MIN(PQ)
    right = EXTRACT_MIN(PQ)
    new_node.freq = left.freq + right.freq
    new_node.left = left
    new_node.right = right
    INSERT(PQ, new_node)
  return EXTRACT_MIN(PQ)  // root of Huffman tree
```

### Example
Characters: a(5), b(9), c(12), d(13), e(16), f(45)

Step 1: Merge a(5) and b(9) → ab(14)
PQ: {c(12), d(13), ab(14), e(16), f(45)}

Step 2: Merge c(12) and d(13) → cd(25)
PQ: {ab(14), e(16), cd(25), f(45)}

Step 3: Merge ab(14) and e(16) → abe(30)
PQ: {cd(25), abe(30), f(45)}

Step 4: Merge cd(25) and abe(30) → cdabe(55)
PQ: {f(45), cdabe(55)}

Step 5: Merge f(45) and cdabe(55) → root(100)

Tree:
```
         root(100)
        /         \
      f(45)      cdabe(55)
                /        \
             cd(25)      abe(30)
            /    \       /    \
          c(12) d(13) ab(14) e(16)
                      /    \
                    a(5)  b(9)
```

Codes:
```
f: 0        (1 bit)
c: 100      (3 bits)
d: 101      (3 bits)
a: 1100     (4 bits)
b: 1101     (4 bits)
e: 111      (3 bits)
```

Expected bits = 45·1 + 12·3 + 13·3 + 5·4 + 9·4 + 16·3 = 45+36+39+20+36+48 = 224

Fixed-length encoding (3 bits each) = 300 bits → **Huffman saves 25%!**

### C++ Implementation
```cpp
#include <iostream>
#include <queue>
#include <string>
using namespace std;

struct Node {
    char ch; int freq;
    Node *left, *right;
    Node(char c, int f, Node* l=nullptr, Node* r=nullptr)
        : ch(c), freq(f), left(l), right(r) {}
};

struct Compare {
    bool operator()(Node* a, Node* b) { return a->freq > b->freq; }
};

Node* buildHuffman(string chars, vector<int> freq) {
    priority_queue<Node*, vector<Node*>, Compare> pq;
    for (int i = 0; i < chars.size(); i++)
        pq.push(new Node(chars[i], freq[i]));
    
    while (pq.size() > 1) {
        Node* l = pq.top(); pq.pop();
        Node* r = pq.top(); pq.pop();
        pq.push(new Node('\0', l->freq + r->freq, l, r));
    }
    return pq.top();
}

void printCodes(Node* root, string code) {
    if (!root) return;
    if (!root->left && !root->right)
        cout << root->ch << ": " << code << "\n";
    printCodes(root->left, code + "0");
    printCodes(root->right, code + "1");
}
```

### Complexity
| Operation | Time |
|---|---|
| Build tree | O(n log n) |
| Encode/Decode | O(L) where L = message length |
| Space | O(n) |

### Properties
- Huffman is **optimal** (produces minimum expected code length)
- More frequent characters get shorter codes
- Always produces a full binary tree (every node has 0 or 2 children)

---

## 11. Fractional Knapsack Problem

### Problem
Same as 0/1 Knapsack but items can be **broken into fractions**. Maximize value with capacity W.

### Greedy Strategy
Sort items by value-per-unit-weight (v[i]/w[i]) in descending order. Take as much as possible of the highest-ratio item, then next, etc.

### Algorithm
```
FRACTIONAL_KNAPSACK(v[], w[], n, W):
  Compute ratio[i] = v[i] / w[i] for each i
  Sort items by ratio descending
  totalValue = 0, remainingCapacity = W
  
  for each item i (in sorted order):
    if w[i] <= remainingCapacity:
      totalValue += v[i]
      remainingCapacity -= w[i]
    else:
      fraction = remainingCapacity / w[i]
      totalValue += v[i] * fraction
      break     // knapsack full
  
  return totalValue
```

### Example
Items: (v=60,w=10), (v=100,w=20), (v=120,w=30), W=50

Ratios: 6, 5, 4
Sorted: item1(ratio=6), item2(ratio=5), item3(ratio=4)

- Take item1 fully: value+=60, W=40 remaining
- Take item2 fully: value+=100, W=20 remaining
- Take 20/30 of item3: value+=120*(20/30)=80
- Total value = 60+100+80 = **240**

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Item { int value, weight; };

double fractionalKnapsack(vector<Item>& items, int W) {
    sort(items.begin(), items.end(), [](const Item& a, const Item& b){
        return (double)a.value/a.weight > (double)b.value/b.weight;
    });
    
    double totalValue = 0;
    int remaining = W;
    
    for (auto& item : items) {
        if (remaining <= 0) break;
        if (item.weight <= remaining) {
            totalValue += item.value;
            remaining -= item.weight;
        } else {
            totalValue += item.value * ((double)remaining / item.weight);
            remaining = 0;
        }
    }
    return totalValue;
}
```

### Complexity
| | Time | Space |
|---|---|---|
| Fractional Knapsack | O(n log n) | O(1) |

### Why Greedy Fails for 0/1 Knapsack
In 0/1 Knapsack, once an item is included, capacity is committed. Taking the highest ratio item might leave capacity that can't be efficiently filled by remaining items. Greedy gives wrong answers for 0/1 — DP is needed.

### 0/1 vs Fractional Knapsack Summary
| | 0/1 Knapsack | Fractional Knapsack |
|---|---|---|
| Items | Whole only | Can split |
| Algorithm | DP | Greedy |
| Time | O(nW) | O(n log n) |
| Optimal greedy? | No | Yes |
| NP-Hard? | Yes | No (polynomial) |

---

# UNIT V: Backtracking, Branch and Bound, Decision Trees, NP Completeness

---

## 12. Backtracking — Core Philosophy

Backtracking is a systematic trial-and-error approach: build a solution incrementally, and **abandon ("backtrack") a partial solution as soon as it's determined it cannot lead to a valid complete solution**.

**State Space Tree:** A tree where each node represents a partial solution. Backtracking explores this tree using DFS, pruning branches that violate constraints.

**Pruning:** Eliminating branches early. Two types:
- **Feasibility function:** Checks if current partial solution can ever lead to complete solution
- **Bound function:** Checks if partial solution can lead to optimal solution (used in optimization)

**Template:**
```
BACKTRACK(node):
  if is_solution(node):
    process_solution(node)
    return
  for each child of node:
    if is_promising(child):
      BACKTRACK(child)
```

---

## 13. N-Queens Problem

### Problem
Place N queens on an N×N chessboard such that no two queens attack each other (no two queens in same row, column, or diagonal).

### State Space
Each level of the tree represents a row. At level k, we try placing queen in each column 1..N.

### Constraint (Promising Check)
Queen at (row, col) is safe if no previously placed queen shares:
- Same column: `col[i] == col[k]`
- Same main diagonal: `|row_i - row_k| == |col[i] - col_k|`
(Same row is automatically avoided by placing one queen per row)

### Algorithm
```
N_QUEENS(k, col[], n):           // placing queen in row k
  if k > n:
    print col[]     // found a solution
    return
  for c from 1 to n:
    if PROMISING(k, c, col):
      col[k] = c
      N_QUEENS(k+1, col, n)

PROMISING(k, c, col):
  for i from 1 to k-1:
    if col[i] == c or |col[i] - c| == |i - k|:
      return false
  return true
```

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
using namespace std;

int n;
vector<int> col;
int solutionCount = 0;

bool promising(int k) {
    for (int i = 1; i < k; i++) {
        if (col[i] == col[k] || abs(col[i] - col[k]) == abs(i - k))
            return false;
    }
    return true;
}

void nQueens(int k) {
    if (k > n) {
        solutionCount++;
        cout << "Solution " << solutionCount << ": ";
        for (int i = 1; i <= n; i++) cout << col[i] << " ";
        cout << "\n";
        return;
    }
    for (int c = 1; c <= n; c++) {
        col[k] = c;
        if (promising(k))
            nQueens(k + 1);
    }
}

int main() {
    n = 4;
    col.resize(n + 1);
    nQueens(1);
    cout << "Total solutions: " << solutionCount << "\n"; // 2 for n=4
}
```

### 4-Queens Example Solutions
```
Solution 1:  _ Q _ _        Solution 2:  _ _ Q _
             _ _ _ Q                     Q _ _ _
             Q _ _ _                     _ _ _ Q
             _ _ Q _                     _ Q _ _
col = [2,4,1,3]              col = [3,1,4,2]
```

### Complexity
| | Value |
|---|---|
| Worst case nodes | O(n!) |
| Space | O(n) for col array + O(n) stack |
| With pruning | Much less than n! in practice |

Number of solutions for n=1:1, n=4:2, n=5:10, n=8:92

---

## 14. Sum of Subsets Problem

### Problem
Given a set of positive integers W = {w1, w2, ..., wn} and a target sum M, find all subsets that sum to exactly M.

### State Space Tree
Binary tree: at level k, either include (left child) or exclude (right child) element w[k].

### Pruning Conditions (at node with sum s, including w[k]):
- **Prune if too large:** s + w[k] > M (sum already exceeds target)
- **Prune if impossible:** s + (sum of remaining elements) < M (can never reach M)

### Algorithm
```
SUM_OF_SUBSETS(k, s, remaining, w[], M, n, subset[]):
  // k = current element index, s = current sum
  // remaining = sum of elements from k to n
  
  if s == M:
    print subset
    return
  
  if k > n: return
  
  // Include w[k]
  if s + w[k] <= M:
    subset.push(k)
    SUM_OF_SUBSETS(k+1, s+w[k], remaining-w[k], ...)
    subset.pop()
  
  // Exclude w[k] (only if still possible to reach M)
  if s + (remaining - w[k]) >= M:
    SUM_OF_SUBSETS(k+1, s, remaining-w[k], ...)
```

### Example
W = {3, 5, 6, 7}, M = 15
Sort: {3, 5, 6, 7}, Total = 21

Tree exploration:
- Include 3 (s=3): Include 5 (s=8): Include 6 (s=14): Include 7 (s=21 > 15 ✗)
  - Exclude 7 (s=14 < 15 ✗)
- Include 3, 5: Exclude 6 (s=8): Include 7 (s=15=M) ✓ **{3,5,7}**
- Include 3, Exclude 5, Include 6 (s=9): Include 7 (s=16>15 ✗)
  - Exclude 7 (s=9 < 15, remaining=0 ✗)
- Continue...
- Exclude 3, Include 5, Include 6 (s=11): Include 7 (s=18>15 ✗)
- Exclude 3, Include 5, Exclude 6, Include 7 (s=12 < 15 ✗)
- Exclude 3, Exclude 5, Include 6, Include 7 (s=13 < 15 ✗)

Result: {3,5,7}

### C++ Implementation
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

void sumOfSubsets(int k, int s, int remaining, vector<int>& w, int M, int n, vector<int>& subset) {
    if (s == M) {
        cout << "{ ";
        for (int x : subset) cout << x << " ";
        cout << "}\n";
        return;
    }
    if (k >= n) return;

    // Include w[k]
    if (s + w[k] <= M) {
        subset.push_back(w[k]);
        sumOfSubsets(k+1, s + w[k], remaining - w[k], w, M, n, subset);
        subset.pop_back();
    }

    // Exclude w[k]: only if still feasible
    if (s + remaining - w[k] >= M) {
        sumOfSubsets(k+1, s, remaining - w[k], w, M, n, subset);
    }
}

int main() {
    vector<int> w = {3, 5, 6, 7};
    sort(w.begin(), w.end());
    int M = 15, n = w.size();
    int total = 0; for (int x : w) total += x;
    vector<int> subset;
    sumOfSubsets(0, 0, total, w, M, n, subset);
}
```

### Complexity
| | Value |
|---|---|
| Worst case | O(2^n) |
| With pruning | Significantly reduced |
| Space | O(n) |

---

## 15. Branch and Bound

### Philosophy
Branch and Bound is used for **optimization** problems (unlike backtracking which finds all solutions). It explores the state space tree but uses **bounds** to prune branches that cannot yield a better solution than the best found so far.

- **Branch:** Divide the problem into subproblems (children in the state space tree)
- **Bound:** Compute an upper/lower bound on the optimal solution achievable from a node
- **Prune:** Discard a node if its bound is worse than the current best

**Search strategies:**
- **BFS (FIFO):** Explores level by level, systematic
- **DFS (LIFO/Stack):** Memory efficient
- **Best-First (Priority Queue):** Always expand node with best bound — usually fastest

---

## 16. Travelling Salesperson Problem (Branch and Bound)

### Problem
Given n cities and distances between them, find the shortest Hamiltonian cycle (visit each city exactly once and return to start).

### State Space
Tree where level k represents choosing which city to visit next.

### Lower Bound Calculation
A common bound for TSP: For each city, find the two smallest edges. The lower bound for any tour is:
```
LB = (1/2) * Σ(for each city: sum of its 2 smallest incident edges)
```
This is because every city must be entered and exited exactly once in any tour.

### Algorithm (Best-First Branch and Bound)
```
TSP_BNB():
  root = (start at city 1, visited={1}, cost=0)
  root.bound = COMPUTE_BOUND(root)
  PQ = {root}    // priority queue ordered by bound
  bestCost = ∞
  
  while PQ not empty:
    node = EXTRACT_MIN_BOUND(PQ)
    if node.bound >= bestCost: continue    // prune
    
    if node is complete tour:
      bestCost = node.cost
      bestTour = node.path
      continue
    
    for each unvisited city v:
      child = extend node with city v
      child.cost = node.cost + dist[node.city][v]
      child.bound = COMPUTE_BOUND(child)
      if child.bound < bestCost:
        PQ.push(child)
```

### Example (4 cities)
Distance matrix:
```
     1    2    3    4
1  [ ∞    10   8    9  ]
2  [ 10   ∞    5    7  ]
3  [ 8    5    ∞    6  ]
4  [ 9    7    6    ∞  ]
```

Lower bound at root (each city's 2 smallest edges):
- City 1: 8+9=17
- City 2: 5+7=12
- City 3: 5+6=11
- City 4: 6+7=13
- LB = (17+12+11+13)/2 = 53/2 = 26.5 → LB = 27

Explore path 1→2→3→4→1: cost = 10+5+6+9 = 30
Explore path 1→3→2→4→1: cost = 8+5+7+9 = 29

Keep branching until optimal found. Branch and bound prunes paths whose lower bounds exceed current best.

### Key Insight for Exam
The bound update: when edge (u,v) is added to partial tour, remove one candidate edge from each of u and v's 2-smallest list. Recompute bound for remaining cities.

### Complexity
| | Value |
|---|---|
| Worst case | O(n! × n) or O(b^d) where b = branching factor |
| Best case (good bounds) | Much better than brute force |
| Space | O(b^d) for the priority queue |

TSP itself is NP-Hard, so no polynomial worst-case algorithm is known.

---

## 17. Assignment Problem (Branch and Bound)

### Problem
n workers, n jobs. Cost c[i][j] = cost of assigning worker i to job j. Assign each worker to exactly one job (bijection) to minimize total cost.

### Hungarian Method (Reduction for Bound)
Lower bound using row/column reduction:
1. Subtract minimum of each row from that row
2. Subtract minimum of each column from that column
3. Sum of all subtracted values = lower bound

### Algorithm
```
ASSIGNMENT_BNB():
  root = no assignments, bound = row+col reduction sum
  PQ = {root}
  best = ∞
  
  while PQ not empty:
    node = EXTRACT_MIN(PQ)
    if node.bound >= best: continue
    
    if all workers assigned:
      best = node.cost
      continue
    
    k = next unassigned worker
    for each unassigned job j:
      child = assign worker k to job j
      child.cost = node.cost + c[k][j]
      child.bound = child.cost + REDUCTION_BOUND(remaining submatrix)
      if child.bound < best:
        PQ.push(child)
```

### Example
Cost Matrix:
```
     J1  J2  J3
W1 [  9   2   7 ]
W2 [  3   6   4 ]
W3 [  1   8   5 ]
```

Row reduction (subtract row mins 2,3,1):
```
     J1  J2  J3
W1 [  7   0   5 ]
W2 [  0   3   1 ]
W3 [  0   7   4 ]
LB from row reduction = 2+3+1 = 6
```
Column reduction (all cols already have a 0):
```
Column mins: 0, 0, 1
Subtract: 
     J1  J2  J3
W1 [  7   0   4 ]
W2 [  0   3   0 ]
W3 [  0   7   3 ]
LB = 6 + 1 = 7
```

Branch: Assign W1→J2 (0 in reduced matrix):
- Remove W1 row and J2 col
- Repeat reduction on 2x2 submatrix
- Continue until all assigned

Optimal: W1→J2(2), W2→J3(4), W3→J1(1) → Total = 7 ✓

### Complexity
| | Value |
|---|---|
| Worst case | O(n! ) |
| With B&B | Much better in practice |
| Hungarian Algorithm (no B&B) | O(n³) |

---

## 18. Decision Trees for Sorting

### Concept
A decision tree is a binary tree that models the comparisons made by a sorting algorithm. Each **internal node** represents a comparison `a[i] : a[j]`. Each **leaf** represents a final permutation (outcome). 

For sorting n elements, there are **n! possible permutations** (leaves).

### Lower Bound Proof for Comparison-Based Sorting
- A binary tree with L leaves has height ≥ log₂(L)
- For n! leaves: height ≥ log₂(n!)
- By Stirling's approximation: log₂(n!) = Θ(n log n)

**Conclusion:** Any comparison-based sorting algorithm requires **Ω(n log n)** comparisons in the worst case.

### Decision Tree for 3-Element Sort (a, b, c)

```
                    a:b
                  /       \
              a<b           a>b
              /               \
           b:c                b:c
          /   \              /   \
        b<c   b>c          b<c   b>c
        /       \          /       \
  a,b,c         a:c      a:c     b,a,c
                / \      / \
             a<c  a>c  a<c  a>c
             /     \    /     \
           a,c,b b,c,a c,a,b c,b,a
```

Leaves: 3! = 6 permutations
Tree height = 3 (in best case) = ⌈log₂(6)⌉

### Optimal Sorting
- Best sorting algorithms (Merge Sort, Heap Sort) achieve O(n log n) → **optimal**
- Insertion sort is O(n²) → not optimal for general case
- Decision tree proves O(n log n) is the **theoretical minimum**

### Height and Comparisons
| n | n! | ⌈log₂(n!)⌉ (min comparisons) |
|---|---|---|
| 2 | 2 | 1 |
| 3 | 6 | 3 |
| 4 | 24 | 5 |
| 5 | 120 | 7 |
| 10 | 3628800 | 22 |

---

## 19. NP and NP-Complete Problems

### Computational Complexity Classes

#### Deterministic Algorithms
A Turing Machine (TM) at each step has exactly one possible action. A Deterministic TM in polynomial time solves problems in class **P**.

#### Non-Deterministic Algorithms
A Non-Deterministic Turing Machine (NDTM) can "guess" a solution and verify it. Two phases:
1. **Guessing phase:** Non-deterministically guess a candidate solution
2. **Verification phase:** Verify the guess in polynomial time

### Class P
**P** = set of decision problems solvable by a deterministic algorithm in polynomial time O(n^k) for some constant k.

Examples: Sorting, shortest path, MST, matrix multiplication, primality testing.

### Class NP
**NP** (Non-deterministic Polynomial) = set of decision problems where a **given solution can be verified** in polynomial time.

Equivalently: problems solvable by a non-deterministic algorithm in polynomial time.

**Key insight:** P ⊆ NP (if you can solve it in poly time, you can verify it in poly time).

**Open question:** P = NP? (Greatest unsolved problem in CS — Clay Millennium Prize)

Examples: TSP (decision: is there a tour of length ≤ k?), Graph Coloring, 3-SAT, Subset Sum.

### Class NP-Complete
A problem X is **NP-Complete** if:
1. X ∈ NP (verifiable in polynomial time)
2. Every problem Y ∈ NP reduces to X in polynomial time (X is NP-Hard)

NP-Complete = hardest problems in NP. If any NP-Complete problem is solvable in polynomial time, then **P = NP**.

### Class NP-Hard
A problem X is **NP-Hard** if every problem in NP can be reduced to X in polynomial time, but X itself may **not be in NP** (may not even be a decision problem).

Examples: Halting problem, TSP optimization version (find shortest tour — not just yes/no).

### Relationships (Venn Diagram)
```
If P ≠ NP (assumed):                If P = NP (hypothetical):
┌────────────────────────┐          ┌────────────────────────┐
│ NP-Hard                │          │                        │
│  ┌──────────────────┐  │          │   P = NP = NP-Complete │
│  │ NP               │  │          │                        │
│  │  ┌────────────┐  │  │          └────────────────────────┘
│  │  │ NP-Complete│  │  │
│  │  └────────────┘  │  │
│  │  ┌──────┐        │  │
│  │  │  P   │        │  │
│  │  └──────┘        │  │
│  └──────────────────┘  │
└────────────────────────┘
```

### Polynomial Time Reduction
Problem A reduces to problem B (A ≤_p B): any instance of A can be transformed into an instance of B in polynomial time, such that the answer for B gives the answer for A.

If B is easy (poly time solvable), then A is easy too.
If A is hard, then B is also hard.

### Cook's Theorem
**3-SAT is NP-Complete** (Cook, 1971 — first NP-Complete problem proven).

**3-SAT:** Given a boolean formula in CNF where each clause has exactly 3 literals, is there a satisfying assignment?

All subsequent NP-Complete proofs: reduce 3-SAT (or known NP-Complete problem) to the new problem.

### Non-Deterministic Algorithms
A non-deterministic algorithm has two special operations:
- **choice(S):** Arbitrarily selects element from set S
- **success / failure:** Announces success or failure

For NP problems, there exists a non-deterministic algorithm:
```
NON_DET_TSP(G, k):
  tour = CHOICE of all n! permutations    // "guess"
  if cost(tour) <= k:                      // "verify"
    return SUCCESS
  return FAILURE
```

### Key NP-Complete Problems
| Problem | Description |
|---|---|
| 3-SAT | Satisfying assignment for 3-CNF formula |
| Graph Coloring (k≥3) | Color graph with k colors, no adjacent same color |
| Clique | Does graph have clique of size k? |
| Vertex Cover | Does graph have vertex cover of size k? |
| Hamiltonian Cycle | Does graph have a Hamiltonian cycle? |
| TSP | Is there a tour of length ≤ k? |
| Subset Sum | Does a subset sum to target T? |
| 0/1 Knapsack (decision) | Is there a selection with value ≥ V and weight ≤ W? |

### How to Prove X is NP-Complete
**Step 1:** Show X ∈ NP — describe a polynomial-time verifier.
**Step 2:** Show X is NP-Hard — choose a known NP-Complete problem Y, show Y ≤_p X (poly reduction).

**Example: TSP is NP-Complete**
- TSP ∈ NP: Given a tour, verify its length in O(n) time.
- Hamiltonian Cycle ≤_p TSP: Given graph G, create complete graph G' with w(u,v)=1 if (u,v)∈E, else w(u,v)=2. TSP tour of cost ≤ n in G' ↔ Hamiltonian cycle in G.

### Summary Table
| Class | Definition | Relation |
|---|---|---|
| P | Solvable in poly time | P ⊆ NP |
| NP | Verifiable in poly time | P ⊆ NP ⊆ NP-Hard |
| NP-Hard | At least as hard as all NP | Not necessarily in NP |
| NP-Complete | NP ∩ NP-Hard | Hardest in NP |

### Dealing with NP-Hard Problems in Practice
1. **Approximation algorithms:** Find near-optimal solutions with provable bounds (e.g., 2-approx for TSP)
2. **Heuristics:** No guarantee but work well in practice (genetic algorithms, simulated annealing)
3. **Exact exponential algorithms:** For small n (branch and bound, DP)
4. **Parameterized algorithms:** Polynomial when some parameter is small

---

## 20. Quick Reference: Complexity Summary

| Algorithm | Time Complexity | Space | Technique |
|---|---|---|---|
| Binomial Coefficient | O(n·k) | O(n·k) or O(k) | DP |
| Warshall's | O(n³) | O(n²) | DP |
| Floyd's | O(n³) | O(n²) | DP |
| 0/1 Knapsack | O(n·W) | O(n·W) | DP |
| Prim's (Heap) | O((V+E) log V) | O(V+E) | Greedy |
| Dijkstra's (Heap) | O((V+E) log V) | O(V+E) | Greedy |
| Huffman Coding | O(n log n) | O(n) | Greedy |
| Fractional Knapsack | O(n log n) | O(1) | Greedy |
| N-Queens (backtrack) | O(n!) worst | O(n) | Backtracking |
| Sum of Subsets | O(2^n) worst | O(n) | Backtracking |
| TSP (B&B) | O(n! ) worst | O(n²) | Branch & Bound |
| Assignment (B&B) | O(n!) worst | O(n²) | Branch & Bound |

---

## 21. Exam Tips & Common Mistakes

### DP
- **0/1 Knapsack:** Always traverse 1D array RIGHT TO LEFT when space-optimizing (prevents using same item twice)
- **Floyd-Warshall:** Initialize D[i][i]=0, all others = edge weight or ∞. Never forget to use INF/2 to prevent overflow in C++
- **Warshall:** Use bitwise OR, not arithmetic addition

### Greedy
- **Dijkstra:** Cannot handle negative edges — say "non-negative edge weights" in exam
- **Prim vs Kruskal:** Both give MST but Prim adds vertices, Kruskal adds edges; Prim better for dense graphs, Kruskal for sparse
- **Huffman:** Always merge MINIMUM two frequencies; re-insert merged node back into priority queue

### Backtracking
- **N-Queens:** Check columns AND both diagonals (|row_diff| == |col_diff| for diagonal)
- **Sum of Subsets:** Prune early — don't miss the case where remaining sum is insufficient

### Branch and Bound
- **TSP bound:** Lower bound must be ≤ any valid tour cost (otherwise we'd prune optimal solutions)
- **Best-First:** Faster in practice but uses more memory than DFS-based B&B

### NP Completeness
- **P vs NP is unsolved** — you cannot say P ≠ NP as a fact
- **Verification vs Solution:** NP = poly-time verifiable (not necessarily poly-time solvable)
- **Reduction direction:** To show X is NP-Hard, reduce FROM a known NP-Hard problem TO X (not the other way)
- TSP, 0/1 Knapsack, N-Queens (decision), Graph Coloring are all NP-Complete

---

## 22. LeetCode Practice Problems

### Dynamic Programming — Binomial / Combinations

| # | Problem | Relevance |
|---|---|---|
| 62 | Unique Paths | Pascal's triangle / DP on grid |
| 63 | Unique Paths II | DP with obstacles |
| 119 | Pascal's Triangle II | Directly uses binomial recurrence |
| 377 | Combination Sum IV | DP counting combinations |
| 518 | Coin Change II | Classic counting DP |

### All-Pairs Shortest Path (Floyd-Warshall)

| # | Problem | Relevance |
|---|---|---|
| 743 | Network Delay Time | Dijkstra / SSSP |
| 787 | Cheapest Flights Within K Stops | Modified Bellman-Ford/Dijkstra |
| 1334 | Find the City With the Smallest Number of Neighbors | Direct Floyd-Warshall application |
| 399 | Evaluate Division | Graph + shortest path |

### 0/1 Knapsack / Subset DP

| # | Problem | Relevance |
|---|---|---|
| 416 | Partition Equal Subset Sum | Classic 0/1 knapsack variant |
| 494 | Target Sum | Knapsack with +/- assignment |
| 474 | Ones and Zeroes | 2D knapsack |
| 322 | Coin Change | Unbounded knapsack (min coins) |
| 1049 | Last Stone Weight II | Partition into 2 subsets |

### Greedy — MST, Shortest Path

| # | Problem | Relevance |
|---|---|---|
| 1584 | Min Cost to Connect All Points | Prim's / Kruskal's MST |
| 1135 | Connecting Cities With Minimum Cost | MST — Kruskal |
| 743 | Network Delay Time | Dijkstra SSSP |
| 1631 | Path With Minimum Effort | Dijkstra on grid |
| 778 | Swim in Rising Water | Dijkstra / binary search |

### Greedy — Huffman / Coding

| # | Problem | Relevance |
|---|---|---|
| 1167 | Minimum Cost to Connect Sticks | Huffman tree (merge smallest pairs) |
| 630 | Course Schedule III | Greedy scheduling |
| 621 | Task Scheduler | Greedy with frequency |

### Greedy — Fractional / Scheduling

| # | Problem | Relevance |
|---|---|---|
| 435 | Non-overlapping Intervals | Greedy interval scheduling |
| 452 | Minimum Number of Arrows to Burst Balloons | Greedy intervals |
| 1029 | Two City Scheduling | Greedy assignment |

### Backtracking

| # | Problem | Relevance |
|---|---|---|
| 51 | N-Queens | Direct N-Queens |
| 52 | N-Queens II | Count solutions (N-Queens) |
| 39 | Combination Sum | Backtracking with sum target |
| 40 | Combination Sum II | Sum of subsets (with duplicates) |
| 78 | Subsets | Enumerate all subsets |
| 90 | Subsets II | Subsets with duplicates |
| 46 | Permutations | Backtracking permutations |
| 47 | Permutations II | Permutations with duplicates |
| 131 | Palindrome Partitioning | Backtracking + DP |

### NP/Decision Problems (Hard, but good for understanding)

| # | Problem | Relevance |
|---|---|---|
| 698 | Partition to K Equal Sum Subsets | NP-Hard in general (backtracking) |
| 1723 | Find Minimum Time to Finish All Jobs | Partition / backtracking |

---

*Good luck with your exams, Vidisha! These notes cover every algorithm, implementation, and concept from your syllabus. Focus especially on the complexity analysis and worked examples — examiners love those. For each LeetCode problem, try the brute-force first, then optimize using the technique from these notes.*
