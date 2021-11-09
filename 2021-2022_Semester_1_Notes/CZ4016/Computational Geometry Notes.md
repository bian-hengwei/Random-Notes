# Computational Geometry

[toc]

# Introduction

## Previously Covered Topics

-   Mathematical tools for complexity analysis
    -   Big-O notations
    -   Recurrence equations
        -   Master Theorem $T(n) = a T({n \over b}) + f(n)$
            -   $f(n) = O(n^{\log_b a - \epsilon})$, $T(n) = \Theta (n^{\log_b a})$
            -   $f(n) = \Theta(n^{\log_b a})$, $T(n) = \Theta (n^{\log_b a} \lg n)$
            -   $f(n) = \Theta(n^{\log_b a} \log^k n)$, $T(n) = \Theta ((n^{\log_b a} \log^{k+1} n)$
            -   $f(n) = \Omega(n^{\log_b a + \epsilon})$, $T(n) = \Theta (n)$
-   Computational complexity theory
    -   Problem reductions
        -   If A can be reduced to B in linear time, then the lower bound of A is a lower bound of B
    -   NP-hard & NP-complete
-   Problem solving strategies
    -   Dynamic Programming

## Problem Solving Techniques

-   divide and conquer
-   problem reduction
-   greedy algorithms
-   output sensitive algorithms
-   incremental methods
-   approximation
-   randomization

## What Is Computational Geometry

A field of computer science that studies algorithms for solving geometric problems

-   Combinatorial computational geometry deals with geometric objects as discrete entities
-   Numerical computational geometry deals with representing real-world objects in forms suitable for computer computations in computer-aided design and manufacturing systems

## Examples

### Closest Pair of Points

-   Bruteforce complexity $O(n)$
-   Lower bound $O(n \log n)$

### Voronoi Diagrams

-   Partition a plane according to which parts are closest to what points. For each site there is a corresponding region, called a Voronoi cell, consisting of all points in the plane that are closer to that site than to any other sites

-   Lower bound $O(n \log n)$

### Path Planning

-   Can be solved using the visibility graph

### Convex Hull

-   Find the smallest convex polyhedron  that contains all points in a set
-   Lower bound for 2D problem $O(n \log n)$
-   To be discussed and tested later

### Art Gallery Problem

-   Upper bound $n \over 3$ guards are always sufficient to guard a polygon with n vertices

### Mixing Ratios

-   Mixing a mixture with a few other mixtures of different ratios of solution

-   Can be converted to a geometric interpretation

-   Can be solved using convex hull problem

### Linear Programming

-   Finding the values of variables $x_1, x_2, ..., x_d$ that satisfy a collection of $n$ linear
    inequalities

## Coverage

-   Convex hull
-   Closest pair of points
-   Robot path planning
-   Linear programming

# Preliminaries

## Points, Segments and Vectors

-   Point $p = (x, y) \in \R^2$
-   Segment between two points: $\overline{p_1 p_2}$
-   Directed segment from $p0$ to $p1$: $\overrightarrow{p_0 p_1}$
-   A vector $\overrightarrow{p} = \overrightarrow{(x, y)}$ is a directed segment from the origin $O = (0, 0)$ to point $p = (x, y)$

## Convex Combination

-   A convex combination of a set of 2D points $S = \{ p_1,...,p_n\}$ is any point of the form $p = \sum\limits_{i=1}^n \alpha_i p_i$ where $\alpha i \geq 0$ and $\sum \limits_{i = 1}^n \alpha_i = 1$

-   Given $S = \{ p_1, p_2\}$, the convex combination of $S$ is $p = \alpha p_1 + (1 - \alpha) p_2$, for $\alpha \in [0, 1]$
-   Given $S = \{ p_1, p_2, p_3\}$, the convex combination of $S$ is $p = \alpha_1 p_1 + \alpha_2 p_2 + \alpha_3 p_3$, for $\alpha \in [0, 1]$ and $\sum \limits_{i = 1}^3 \alpha_i = 1$

-   Given $S = \{ p_1, p_2, ..., p_n\}$, a convex combination of $S$ is the region bordered by the convex hull of $S$, denoted by $CH(S)$

## Cross Product

-   In $\R^2$, we define the cross product of two 2D vectors as a scalar $x_1y_2 - x_2y_1$
    -   If Result < 0, $p_1$ is CW from $p_2$ w.r.t O
    -   If Result = 0, $p_1$, $p_2$ and O are collinear
    -   If Result > 0, $p_1$ is CCW from $p_2$ w.r.t O
-   Given two line segments $\overline{p_0 p_1}$ and $\overline{p_1 p_2}$, if we traverse them from $p_0$ to $p_2$, do we make a left or a right turn at $p_1$? We compute $\overline{p_0 p_1} \times \overline{p_1 p_2}$
    -   If Result < 0, CW. right turn
    -   If Result = 0, forward or backward
    -   If Result > 0, CCW, left turn

## Lines

-   Parametric:
    -   $p(\lambda) = \lambda p_1 + (1 - \lambda) p_2$

## Segments Intersection

-   Computing intersection point
-   Computing line segment turns

# Convex Hull

## Definitions

### Convex Set

-   A set is convex if for any pair of points, the entire line segment is inside the set

### Convex Hull

-   A convex hull of a set of points is the smallest convex set that includes the set
-   Equivalent definitions
    -   intersection of all convex sets that includes the set
    -   set of all convex combinations of points in the set
    -   smallest perimeter polygon containing all points in the set

## Problem Statement

-   Given a finite set $S$ pf all points in $\R^2$, find its convex hull $CH(S)$, i.e. the smallest convex set that contains all points of $S$
-   Methods
    -   Brute force $O(n^3)$
    -   Rotational sweep technique
        -   Graham's Scan $O(n \log n)$
        -   Jarvis March (a.k.a the gift wrapping algorithm): an output sensitive method with time complexity $O(nh)$
    -   Divide and conquer techniques $O(n \log n)$
    -   Incremental method $O(n \log n)$

## Brute Force

-   For each line segment, test all points to find out that if they are on the same side
-   $O(n^2)$ combinations, each takes $O(n)$ time

## Rotational Sweep - Graham's Scan

-   Based on sorting the points and then inserting them into the hull in sorted order

-   Intuition: rubber band

-   Algorithm:

    -   Start at a point guaranteed to be on the hull (e.g., the point with minimum y value) $O(n)$

    -   Sort the remaining points by polar angles of vertices relative to the first point $O(n \log n)$

    -   Go through the sorted points, keep the vertices of points that have left turns and drop those that have right turns $O(n)$

        -   Use a stack to detect and remove cancavities in the boundary; and record the vertices of $CH(S)$

        

## Rotational Sweep - Jarvis March

-   Intuition: gift wrapping
-   Algorithm:
    -   Find the lowest point, which is guaranteed to be on the convex hull $O(n)$
    -   Rotate CCW a line through the last point until it touches any one of the other points $O(n)$
    -   Repeat the step until the lowest point is reached again $O(h)$
-   Worst case: $O(n^2)$
-   Worse case scenario can be used to solve onion layers

## Divide and Conquer Algorithm

-   Algorithm
    -   If there are only 3 points, form a triangle
    -   Else
        -   Divide set into two disjoint subsets of the same size
        -   Compute their convex hulls
        -   Compute the convex hull by merging the two results
-   Merging algorithm:
    -   Find the middle points for the two convex hulls
    -   raise the first copy until raising both vertices would make an illegal turn
    -   Complexity: $O(n)$
-   Recurrence equation: $T(n) = 2T({n \over 2}) + O(n)$

## Incremental Method

-   Two cases:
    -   If the new point is inside, do nothing
    -   Else, compute the visible region for the point
-   Order:
    -   Sorted order
    -   Random order
-   Complexity:
    -   Best case $O(n)$
    -   Worst case $O(n^2)$
    -   Average case  $O(n \log n)$

## Onion Layers

-   Divide the set into $k$ depths of layers of convex hulls
-   Optimal $O(n \log n)$

