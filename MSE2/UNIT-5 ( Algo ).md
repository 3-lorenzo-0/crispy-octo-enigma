

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