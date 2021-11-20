# Quiz 5 Notes

[toc]

## Linear Programming

### Definition

**Given a set of linear constraints $H$ and a linear objective function $c$ in $\R^d$, a *linear programming* (LP) is formulated as follows:**

​	maximize $c_1x_1 + c_2x_2 + ... + c_dx_d$

​	subject to $a_{n, 1}x_1 + ... + a_{n, d}x_d \leq b_n$

-   $H$ is a set of half-space in $\R^d$
-   We are searching for a point $x \in \cap_{h \in H}$ that maximizes $c^T x$
-   Linear programming is a central method in operations search



### Algorithms

-   Simplex algorithm
-   Ellipsoid method
-   Interior-point method



### Special Case for $d = 2$

-   Solving 2D LP is equivalent to finding the intersecting convex regions.
-   Possible solution space
    -   $\cap H = \emptyset$
    -   $\cap H$ is unbounded
    -   $\cap H$ is bounded but not unique
    -   $\cap H$ is bounded and unique



### Divide-and-Conquer

-   The half-planes are convex

```ps
IntersectHalfplanes(H)
	if |H| = 1 then
		c <- H
	else
		(H1, H2) <- SplitInHalves(H)
		C1 <- IntersectHalfplanes(H1)
		C2 <- IntersectHalfplanes(H2)
		C <- IntersectConvexRegions(C1, C2)
	endif
	return C
```

-   `IntersectConvexRegions(C1, C2)`
    -   Sweep line method
    -   Move the sweep line $l$ frim top to bottom and save crossed edges $(\leq 4)$
    -   The nodes of $C_1 \cup C_2$ define events
    -   We process every event in $O(1)$ time, depending on the type of the edges incident to the event vertex



### Complexity Analysis

-   Intersecting convex regions takes $O(n)$
-   $T(n) = 2T({n \over 2}) + O(n)$
-   $O(n \log n)$ time



### Other Methods

-   Incremental: $O(n^2)$
-   Randomized: $O(n)$



## Max-Flow and Min-Cut

### Definition

-   Let $G = (V, E)$ be a direct graph with the source $s$ and the sink $t$
-   Edge capacity $c : E \rightarrow \R^+$ is the maximum amount of flow that can pass through an edge
-   A flow is a map $f : E \rightarrow \R_{\geq 0}$ that satisfies the following:
    -   Capacity constraint: $f(e) \leq c(e)$
    -   Conservation constraint: $\sum_{e \text{ into } v} f(e) = \sum_{e \text{ out of } v} f(e)$
-   The value of a flow, denoted by $|f|$, is the amount of flow passing from the source to the sink
    -   $|f| = \sum_{e \text{ out of } s} f(e)$



### Problem Statement

-   Let $G$ be a flow network
-   We assume
    -   No edges enter $s$
    -   No edges leave $t$
    -   There is at least one edge incident on each vertex
    -   All edge capacities are integers (for FF algorithm to execute)
-   The maximum flow problem is to route as much flow as possible from the source to the sink (maximize $|f_\text{max}|$)



### Greedy Algorithm

-   Makes a sequence of myopic and irrevocable decisions
-   Does not guarantee best-possible solution
-   Steps:
    -   Start with zero flow along all edges
    -   Find an unsaturated path from $s$ to $t$ and push as much flow along it as possible
    -   Repeat until no augmenting paths can be found



### Residual Graph

-   Allow undoing the flow
-   Given a flow network $G = (V, E)$ and a flow $f : E \rightarrow \R$, the residual graph $G_f$ of $G$ w.r.t. $f$ is a directed graph such that
    -   The note set of $G_f$ is the same as $G$
    -   For each edge $e$ from $u$ to $v$ with capacity $c(e)$ and flow f(e)
        -   a forward edge $(u, v)$ with capacity $c(e) - f(e)$
        -   a backward edge $(v, u)$ with capacity $f(e)$



### Ford-Fulkerson Algorithm

-   Idea: augment flow $f$ along any existing augmenting path $P$ then update the residual graph

-   Pseudo code:

    ```ps
    initialize f(e) = 0 for all e in E
    repeat
    	search for a path P from s to t in the residual graph such that f(e) < c(e) for every e in P
    	if no such path then
    		halt with current flow {f(e)} e in E
    	else
    		compute the bottleneck delta = min e in P (c(e) - f(e)) in P
    		for all edges e in P do
    			increase f(e) by delta and update the forward and backward edges for e
    until forever
    ```

-   Complexity analysis

    -   BFS in $O(|V| + |E'|)$ where $|E'| \leq 2|E|$
    -   Runs at most $|f_\text{max}|$ iterations
    -   Overall time complexity: $O(|f_\text{max}| \times |E|)$

-   Non-integer may cause a non-stopping loop



### Edmonds-Karp

-   maximum bottleneck
-   sufficient large capacity
-   **fewest number of edges (shortest path)**: $O(|V| \times |E|^2)$



### Cut

-   Given a flow network, a cut $(S, T)$ is a partition of $V$ into two disjoint subsets $S$ and $T$ such that $s \in S$ and $t \in T$
-   Let $f$ be a flow thorugh $G$, The **net flow** through the cut is the sum of flows that leave $S$ minus the sum of flows that go into $S$
-   The **capacity** of a cut is the sum of capacities on the edges from a vertex in $S$ to a vertex in $T$
-   A **minimum cut** is a cut with the minimum capacity of all network cuts



### Max-Flow Min-Cut Duality Theorem

-   Let $f$ be a flow in network $G = (V, E, c)$ from source $s$ to target $t$. The following conditions are equivalent:
    -   $f$ is a maximum flow in $G$
    -   The residual network $G_f$ contains no augmenting paths
    -   There exists a cut $(S, T)$ of $G$, of which the flow equals the capacity



### Correctness of FF

-   FF stops
-   FF stops with max flow



### Maximum Bipartite Maching

-   Bipartite graph: an undirected graph $G = (V, E)$ is called bipartite if there is a partition $V = L \cup R$, s.t. $L \cap R = \empty$ and for all $e = (u, v) \in E$ holds $u \in L \longleftrightarrow v \in R$.
-   A matching is a set of edges chosen in such a way that no two edges share an endpoint
-   A maximum matching is a matching of maximum size



### Job Application

-   $M$ job applicants and $N$ jobs
-   Got a map of interests
-   An assignment that maximizes the number of applicants that get jobs
-   **Add source $s$ and sink $t$ such that each capacity is 1 unit and find the max flow**



### Trans-shipment

-   $M$ suppliers and $N$ customers
-   Got a map of delivers
-   Maximizes suppliers output and customers input
-   **Add source $s$ and sink $t$ such that $s \rightarrow M_a$ is the amound $M_a$ holds and $t \rightarrow N_a$ is the amound $N_a$ needs**
-   Capacity between the deliver relationships is $\infin$
