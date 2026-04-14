

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


## **Matrix Chain Multiplication using DP**

### **1. Definition**

Matrix Chain Multiplication finds the **optimal parenthesization** of matrices A1×A2×...×AnA1​×A2​×...×An​ to **minimize scalar multiplications**. Matrix multiplication is associative but different orderings have different costs.

**Given:** Dimensions array pp where matrix AiAi​ has size pi−1×pipi−1​×pi​  
**Goal:** Find minimum cost to compute the product

---

### **2. Algorithm**

MatrixChainOrder(p)
1.  n = length(p) - 1
2.  For i = 1 to n
3.      m[i,i] = 0
4.  For L = 2 to n              // L = chain length
5.      For i = 1 to n-L+1
6.          j = i + L - 1
7.          m[i,j] = ∞
8.          For k = i to j-1
9.              q = m[i,k] + m[k+1,j] + p[i-1]*p[k]*p[j]
10.             If q < m[i,j]
11.                 m[i,j] = q
12. Return m[1,n]

**Where:**

- `m[i,j]` = minimum cost to multiply Ai...AjAi​...Aj​
- `L` = chain length
- `k` = split point

### **3. Example**

**Matrices:** A1(10×30),A2(30×5),A3(5×60)A1​(10×30),A2​(30×5),A3​(5×60)  
**Dimensions:** p=[10,30,5,60]p=[10,30,5,60]

#### **Step 1: Initialize DP Table**

Diagonal elements = 0 (single matrix costs nothing)

   |  1    2    3

--|----------------

 1 |  0    

 2 |       0

 3 |            0

#### **Step 2: Chain Length L = 2**

**Compute A1A2A1​A2​:**

- Cost = 10×30×5=150010×30×5=1500
- m[1,2]=1500m[1,2]=1500

**Compute A2A3A2​A3​:**

- Cost = 30×5×60=900030×5×60=9000
- m[2,3]=9000m[2,3]=9000


	|  1     2     3

---|------------------

  1 |  0  1500

 2 |        0    9000

 3 |                  0

#### **Step 3: Chain Length L = 3**

**Compute A1A2A3A1​A2​A3​:**

Try k=1k=1: (A1)(A2A3)(A1​)(A2​A3​)  
q=m[1,1]+m[2,3]+p[0]×p[1]×p[3]q=m[1,1]+m[2,3]+p[0]×p[1]×p[3]  
q=0+9000+10×30×60=27000q=0+9000+10×30×60=27000

Try k=2k=2: (A1A2)(A3)(A1​A2​)(A3​)  
q=m[1,2]+m[3,3]+p[0]×p[2]×p[3]q=m[1,2]+m[3,3]+p[0]×p[2]×p[3]  
q=1500+0+10×5×60=4500q=1500+0+10×5×60=4500 ✓ **Minimum**

m[1,3]=4500m[1,3]=4500

   |  1     2     3

--|------------------

 1 |  0  1500  4500

 2 |        0    9000

 3 |                0


---

### **Final Result**

**Minimum Cost = 4500**  
**Optimal Parenthesization:** (A1A2)A3(A1​A2​)A3​

---

### **4. Complexity Analysis**

|Case|Time Complexity|Space Complexity|
|---|---|---|
|**Best**|O(n3)O(n3)|O(n2)O(n2)|
|**Average**|O(n3)O(n3)|O(n2)O(n2)|
|**Worst**|O(n3)O(n3)|O(n2)O(n2)|

**Reason:** Three nested loops (chain length → start index → split point) always execute fully regardless of input values.


# Traveling Salesman Problem (DP Approach)

## Definition

The Traveling Salesman Problem (TSP) requires finding the shortest possible route for a salesman to visit every city exactly once and return to the starting city. The goal is to minimize the total travel distance or cost of the tour.

---

##  State Representation (Bitmasking)

We use a DP table:

- **dp[mask][i]**  
    👉 Minimum cost to visit all cities in `mask` and end at city `i`

Where:

- `mask` = binary representation of visited cities
- `i` = current city

Example:  
If there are 4 cities,  
`mask = 1011` → cities {0,1,3} visited

---

##  Recurrence Relation

![[Pasted image 20260414082631.png]]

👉 Meaning:

- Try reaching city `i` from all possible previous cities `j`
- Take minimum cost

---

##  Algorithm (Stepwise)

1. Initialize:
    - dp[[1 << start][start] = 0
2. For all subsets (mask):
    - For each city `i` in mask:
        - Try all previous cities `j`
3. Update DP using recurrence
4. Final Answer:

![[Pasted image 20260414082704.png]]

---

##  Example

Consider 4 cities with distance matrix:

![[Pasted image 20260414083101.png]]

👉 Using DP, we compute all subsets and paths.

✔ Optimal tour:

0 → 1 → 3 → 2 → 0

✔ Minimum cost:

10 + 25 + 30 + 15 = 80

---

##  Complexity

**Time Complexity:**  
Best = Average = Worst = O(n² · 2ⁿ), since all subsets are evaluated.

**Space Complexity:**  
Best = Average = Worst = O(n · 2ⁿ), due to storage of DP table.


# Single Source Shortest Path – **Bellman-Ford Algorithm**

---

##  Definition (Strong & Precise)

The Single Source Shortest Path problem aims to determine the **minimum distance from a given source vertex to all other vertices in a weighted graph**.

The Bellman-Ford Algorithm solves this problem by **iteratively relaxing all edges**, and it is capable of handling **graphs with negative edge weights**, unlike **Dijkstra's Algorithm**.

---

##  Algorithm (Stepwise)

1. Initialize:
    - Distance of source vertex = 0
    - Distance of all other vertices = ∞
2. Repeat **(V − 1) times**:
    - For every edge (u → v) with weight w:
        
        if dist[u] + w < dist[v]  
            dist[v] = dist[u] + w
        
3. Check for negative weight cycle:
    - For every edge (u → v):
        
        if dist[u] + w < dist[v]  
            report negative cycle

##  Why **Dijkstra's Algorithm** Fails

Dijkstra’s Algorithm assumes that once a node is visited, its shortest path is final.

👉 This assumption breaks when **negative edges exist**.

Example:  
If a shorter path appears later due to a negative edge, Dijkstra **cannot update it**, leading to incorrect results.

✔ Bellman-Ford avoids this by **relaxing edges multiple times (V−1)**.

![[Pasted image 20260414084141.png]]

