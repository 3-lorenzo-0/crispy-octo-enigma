

##  Core DP

**Define Dynamic Programming** Dynamic Programming is an optimization technique that solves complex problems by breaking them into smaller overlapping subproblems, solving each subproblem only once, and storing results to avoid redundant computation.

**What is Optimal Substructure?** A problem has optimal substructure if the optimal solution to the whole problem can be built from the optimal solutions of its subproblems. Example: shortest path A→C via B uses shortest A→B and B→C.

**What are Overlapping Subproblems?** Overlapping subproblems occur when the same subproblems are recomputed multiple times in a recursive solution. DP stores these results so each subproblem is solved only once, reducing time complexity.

**Difference between Greedy vs Dynamic Programming** Greedy makes a locally optimal choice at each step without revisiting decisions — it is fast but may not give a globally optimal result. DP considers all possibilities and guarantees a globally optimal solution using stored subproblem results.

**What is Memoization vs Tabulation?** Memoization is a top-down approach where results of recursive subproblems are cached as they are computed. Tabulation is a bottom-up approach where a table is filled iteratively from the smallest subproblem to the final answer, avoiding recursion altogether.

---

##  Multistage Graph

**Define Multistage Graph** A multistage graph is a directed weighted graph where nodes are divided into stages, and edges only go from one stage to the next. The goal is to find the minimum cost path from the source (stage 1) to the sink (last stage).

**What is a stage in a multistage graph?** A stage is a group of nodes at the same level in the graph. Each node belongs to exactly one stage, and edges exist only between consecutive stages, representing decisions or transitions from one level to the next.

**Why is DP suitable for multistage graph?** DP is suitable because the multistage graph exhibits optimal substructure — the minimum cost path from source to sink passes through optimal subpaths. Each stage decision depends only on the current node, allowing efficient cost computation using forward or backward DP.

---

##  TSP (Traveling Salesman Problem)

**Define Traveling Salesman Problem** TSP requires finding the shortest possible route for a salesman to visit every city exactly once and return to the starting city. The goal is to minimize the total travel distance or cost of the tour.

**Why is TSP difficult? (NP-hard concept basic)** TSP is NP-hard because the number of possible routes grows factorially with the number of cities — for n cities there are (n−1)! possible tours. No known polynomial time algorithm can solve it exactly for all cases.

**What is state representation in TSP DP?** In DP-based TSP, the state is represented as (S, i), where S is the set of cities already visited and i is the current city. This allows storing the minimum cost to reach city i having visited all cities in set S, using bitmask DP.

---

##  Matrix Chain Multiplication

**What is Matrix Chain Multiplication problem?** MCM is the problem of finding the most efficient way to multiply a sequence of matrices. The goal is to determine the optimal order of multiplication that minimizes the total number of scalar multiplications.

**What is meant by parenthesization?** Parenthesization refers to placing parentheses in different ways to group matrices for multiplication. Different groupings result in different numbers of operations, and DP finds the grouping with the minimum cost.

**Define cost in MCM** The cost of multiplying two matrices of dimensions p×q and q×r is p×q×r scalar multiplications. The total cost depends on the order of multiplication, and DP minimizes this total across all possible groupings.

---

##  Shortest Path

**Define Single Source Shortest Path** Single Source Shortest Path (SSSP) finds the shortest path from one source vertex to all other vertices in a weighted graph. Algorithms like Dijkstra and Bellman-Ford solve this problem efficiently.

**Difference between Dijkstra and Bellman-Ford (basic)** Dijkstra uses a greedy approach with a priority queue and works only on graphs with non-negative edge weights. Bellman-Ford uses DP by relaxing all edges repeatedly and handles negative edge weights, but is slower with O(VE) complexity.

**What is edge relaxation?** Edge relaxation is the process of updating the shortest known distance to a vertex. For an edge (u, v) with weight w, if dist[u] + w < dist[v], then dist[v] is updated to dist[u] + w. This is the core operation in both Dijkstra and Bellman-Ford.

---

## Explain Dynamic Programming with properties and example

### 🔹 Definition

**Dynamic Programming (DP)** is an algorithmic paradigm used to solve complex problems by breaking them into simpler, overlapping subproblems. It solves each subproblem only once, stores the result (in an array/table or cache), and reuses it to avoid redundant computations. DP is highly effective for optimization and counting problems.

---

### 🔹 Key Properties

A problem must satisfy **both** properties to be solvable using DP:

1. **Optimal Substructure**  
    An optimal solution to the problem can be constructed from optimal solutions of its subproblems.  
    _Example:_ In the shortest path problem, the shortest path from `A → C` passing through `B` consists of the shortest path `A → B` and `B → C`.
2. **Overlapping Subproblems**  
    The recursive solution repeatedly solves the same subproblems. DP avoids this recomputation by storing results.  
    _Contrast:_ Divide & Conquer (e.g., Merge Sort) has _independent_ subproblems; DP has _repeated_ ones.

---
##  Two Approaches of DP

**Memoization (Top-Down):** Solve the problem recursively, but cache the result of each subproblem in a table the first time it is computed. On future calls with the same input, return the cached result directly instead of recomputing.

**Tabulation (Bottom-Up):** Solve all subproblems iteratively starting from the smallest base case, filling a table from bottom to top until the final answer is reached. No recursion is involved.

---

##  Fibonacci Example

The Fibonacci sequence is defined as:

F(0) = 0, F(1) = 1, F(n) = F(n−1) + F(n−2) for n ≥ 2

---

### Without DP — Plain Recursion

```
fib(n):
    if n <= 1: return n
    return fib(n-1) + fib(n-2)
```

For fib(5), the call tree looks like this:

```
                fib(5)
              /        \
          fib(4)       fib(3)
          /    \       /    \
       fib(3) fib(2) fib(2) fib(1)
       /   \
   fib(2) fib(1)
```

fib(3) is computed 2 times, fib(2) is computed 3 times — massive redundancy as n grows.

---

### With DP — Tabulation (Bottom-Up)

```
fib(n):
    table[0] = 0
    table[1] = 1
    for i from 2 to n:
        table[i] = table[i-1] + table[i-2]
    return table[n]
```

Step-by-step for n = 6:

|i|0|1|2|3|4|5|6|
|---|---|---|---|---|---|---|---|
|F(i)|0|1|1|2|3|5|8|

Each value is computed exactly once using the previously stored values. No repeated calls, no recursion stack overhead.

---

### 🔹 Complexity Comparison

|Approach|Time Complexity|Space Complexity|Reason|
|---|---|---|---|
|Naive Recursion|`O(2ⁿ)`|`O(n)`|Repeated subproblem calls create a binary recursion tree|
|DP (Memoization/Tabulation)|`O(n)`|`O(n)`|Each subproblem computed & stored once|
|**Space-Optimized DP** (Fibonacci only)|`O(n)`|`O(1)`|Keep only last two values instead of full array|

# Multistage Graph — Complete Explanation (Classic Exam Question)

## 1. Definition

A Multistage Graph is a directed, weighted graph where:

- The set of vertices is partitioned into k stages: Stage 1, Stage 2, ..., Stage k
- Edges exist only from a vertex in stage i to a vertex in stage i+1 (no backward edges, no skip edges)
- There is exactly one source vertex s in Stage 1 and one sink vertex t in Stage k
- The goal is to find the minimum cost path from s to t passing through exactly one vertex per stage

It models real-world decision-making problems where at each step you choose the best option to move to the next level.

---

## 2. Properties

- Every path from source to sink passes through exactly k stages
- Each edge has an associated cost c(u, v)
- The problem satisfies both DP properties — optimal substructure and overlapping subproblems
- The Principle of Optimality applies: the optimal path through stage i depends only on the optimal cost from that stage onward, not on how you reached it

---

## 3. Backward Approach — Algorithm

1. Define: cost[v] = min cost from v to sink t
           next[v] = next vertex on optimal path

2. Base: cost[t] = 0

3. Recurrence: For stage j = k-1 downto 1:
                  For each vertex v in stage j:
                     cost[v] = min{c(v,u) + cost[u]}  ∀u in stage j+1
                     next[v] = argmin{u}

4. Reconstruct: s → next[s] → next[next[s]] → ... → t
---
## 4. Numerical Example (5-Stage Graph)

![[Pasted image 20260414081011.png]]
### **Solution (Backward Approach)**

**Given:** Source=S, Sink=T

**Step 1: Initialize**

cost[T] = 0

**Step 2: Stage 3 → T**

cost[D] = 6 + 0 = 6

cost[E] = 3 + 0 = 3

cost[F] = 5 + 0 = 5

**Step 3: Stage 2 → Stage 3**

cost[A] = min(6+6, 3+3) = min(12, 6) = 6   [next: E]

cost[B] = min(5+6, 4+3, 4+5) = min(11, 7, 9) = 7   [next: E]

cost[C] = min(4+3, 3+5) = min(7, 8) = 7   [next: E]

**Step 4: Stage 1 → Stage 2**

cost[S] = min(2+6, 3+7, 4+7) = min(8, 10, 11) = 8   [next: A]

---
## Result

- **Min Cost:** **8**
- **Optimal Path:** S → A → E → T

---
### **Complexity**

|Case|Time|Space|
|---|---|---|
|**Best**|O(n + e)|O(n)|
|**Average**|O(n + e)|O(n)|
|**Worst**|O(n²)|O(n)|

