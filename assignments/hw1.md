# [Homework 1](https://github.com/hanggrian/IIT-CS535/blob/assets/assignments/hw1.pdf)

## Problem 1

> Show that there is no comparison sort whose running time is linear for at
  least half of the $n!$ inputs of length $n$. What about a fraction of $1/n$ of
  the inputs of length $n$? What about a fraction $1/(2^n)$?

Binary decision tree of height $h$ has at most $2^h$ leaves: $h \geq \lg(n!)$.
For half of $n!$, there are $\frac{n!}{2}$ leaves:

$$
\begin{align}
  h &\geq \lg\left(\frac{n!}{2}\right) \\
  &\geq \lg(n!) - \lg(2) \\
  &\geq \underbrace{\lg(n!)}_\textsf{Stirling} - 1 \\
  &\geq \underbrace{n \lg(n)}_\textsf{dominant} - n \lg(e) - 1 \\
  h &= \mathbf{O(n \lg(n)) \neq O(n)}
\end{align}
$$

In case of fraction of $1/n$, there are $\frac{n!}{n}$ leaves:

$$
\begin{align}
  h &\geq \lg\left(\frac{n!}{n}\right) \\
  &\geq \underbrace{\lg(n!)}_\textsf{Stirling} - \lg(n) \\
  &\geq \underbrace{n \lg(n)}_\textsf{dominant} - n \lg(e) - \lg(n) \\
  h &= \mathbf{O(n \lg(n)) \neq O(n)}
\end{align}
$$

There are $\frac{n!}{2^n}$ leaves for fraction of $1/(2^n)$:

$$
\begin{align}
  h &\geq \lg\left(\frac{n!}{2^n}\right) \\
  &\geq \lg(n!) - \lg(2^n) \\
  &\geq \lg(n!) - n \lg(2) \\
  &\geq \underbrace{\lg(n!)}_\textsf{Stirling} - n . 1 \\
  &\geq \underbrace{n \lg(n)}_\textsf{dominant} - n \\
  h &= \mathbf{O(n \lg(n)) \neq O(n)}
\end{align}
$$

Therefore, the theorem holds for all three cases.

## Problem 2

> Our SELECTION algorithms uses partitioning into groups of 5, computing the
  median in each group, followed by two recursive calls (with some work between
  them). Analyze the running time for two variants of this algorithm, where the
  groups are of size 3, and of size 7 respectively.

The SELECT algorithm can be described by the recurrence:
$T(n) = T(\frac{n}{g}) + T(r) + O(n)$, where $g$ is the group size and $r$ is
the recursive call on partition. When group size $g$ is 3, the median of medians
is at least $2 . \frac{1}{2} . \frac{n}{3}$.

$$
\begin{align}
  r &= n - \left(\left\lceil \frac{g}{2} \right\rceil . \frac{1}{2} . \frac{n}{g}\right) \\
  &= n - \left(2 . \frac{1}{2} . \frac{n}{3}\right) \\
  &= n - \frac{n}{3} \\
  &= \frac{2n}{3} \\
  T(n) &= \underbrace{T(\frac{n}{3}) + T(\frac{2n}{3})}_\textsf{subproblems} + O(n) \\
  &= \frac{1}{3} + \frac{2}{3} \\
  &= 1 = 1 \\
  &= \mathbf{O(n \lg(n))}
\end{align}
$$

There are at least $4 . \frac{1}{2} . \frac{n}{7}$ elements with group size $g$
of 7:

$$
\begin{align}
  r &= n - \left(\left\lceil \frac{g}{2} \right\rceil . \frac{1}{2} . \frac{n}{g}\right) \\
  &= n - \left(4 . \frac{1}{2} . \frac{n}{7}\right) \\
  &= n - \frac{2n}{7} \\
  &= \frac{5n}{7} \\
  T(n) &= \underbrace{T(\frac{n}{7}) + T(\frac{5n}{7})}_\textsf{subproblems} + O(n) \\
  &= \frac{1}{7} + \frac{5}{7} \\
  &= \frac{6}{7} < 1 \\
  &= \mathbf{O(n)}
\end{align}
$$

In a recursion tree method, the runtime is $O(n \lg(n))$ when the size of the
subproblems is equal to 1. The work decreases at each level when the subproblem
size is less than 1, resulting in $O(n)$ runtime.

## Problem 3

> Assume that in a weighted $G = (V, E)$ with weights $w$ on the edges, for
  every cut (as defined in `blue_rule.pdf`), among the edges crossing the cut
  only one has minimum weight. Let $T$ be a minimum spanning tree in $G$. Prove
  that no other minimum spanning tree exists. Give a counterexample to the
  converse statement; that is give an example of a weighted graph $G$ with
  minimum spanning tree $T$ and cut $(S, \bar{S})$ such that no other minimum
  spanning tree exists and there are two edges of minimum weight crossing
  $(S, \bar{S})$.

Graph $G$ is being divided into two disjoint sets ($S, \bar{S}$) with minimum
weight edge $e$ crossing the cut. Suppose that $e \notin T$, then adding $e$ to
$T$ will create a cycle. There must be another edge $e_2$ in $\{e\} \cup T$ that
crosses the cut, removing it will create another MST $T_2$. Since $e$ has the
smallest weight $w(e) < w(e_2)$:

$$
\begin{align}
  w(e) - w(e_2) &< 0 \\
  W(T_2) &= W(T) + w(e) - w(e_2) \\
  &= W(T) - 1 \ldots \\
  W(T_2) &< W(T)
\end{align}
$$

However, $W(T_2)$ cannot be less than $W(T)$ because $T$ is the MST.

When there are two minimum weight edges crossing the cut $(e_1, e_2)$:

$$
\begin{align}
  w(e_1) &= w(e_2) \\
  W(T_2) &= W(T) + w(e_1) - w(e_2) \\
  &= W(T) + 0 \\
  W(T_2) &= W(T)
\end{align}
$$

Therefore, $T_2$ is also an MST because $W(T_2) = W(T)$, which contradicts the
statement that no other MST exists.

## Problem 4

> Given an edge-weighted undirected graph $G = (V, E, w)$, and a spanning tree
  $T$ in $G$, define the $width$ of $T$ to be the weight of its minimum-weight
  edge. Describe an efficient algorithm that, given $G$, finds a spanning tree
  $T$ of maximum width. Present pseudocode, analyize the running time and prove
  correctness.

Kruskal's algorithm defines a disjoint-set data structure to keep track of
connected nodes.

```java
class Edge {
    int u;
    int v;
    int weight;

    // Create a weighted edge (u, v).
    Edge(int u, int v, int weight) { /* ... */ }
}

class DisjointSet {
    // Create disjoint set of n elements.
    DisjointSet(int n) { /* ... */ }

    // Get the root of the set containing u.
    int find(int u) { /* ... */ }

    // Returns true if union was successful.
    boolean union(int u, int v) { /* ... */ }
}
```

To find the maximum width spanning tree, we modify the rest of algorithm to sort
edges in decreasing order. Then, add edges to the spanning tree until all
vertices are connected, returning the tree instead of the total weight in the
original algorithm.

```java
List<Edge> maxWidthSpanningTree(int n, List<Edge> T) {
    T.sort((e1, e2) -> e2.weight - e1.weight);

    List<Edge> result = new ArrayList<>();
    DisjointSet set = new DisjointSet(n);

    for (Edge e : T) {
        if (set.find(e.u) == set.find(e.v)) {
            continue;
        }
        set.union(e.u, e.v);
        result.add(e);
        if (result.size() == n - 1) {
            break;
        }
    }
    return result;
}
```

Calculating the running time, considering $n = \lvert V \rvert$ and
$m = \lvert E \rvert$.

$$
\begin{align}
  \textsf{sorting} + \textsf{union-find loops} &= \\
  \underbrace{O(m \lg(m))}_\textsf{dominant} + O(m . \alpha(n)) &= \\
  O(m \lg(m))
\end{align}
$$

Let $e$ be the minimum weight edge in the final tree $e \leq (E \cup \{e\})$. If
there is another spanning tree with a larger weight than edge $e$, then its
edges would have been collected earlier in the sorted list.

## Problem 5

> Let $G = (V, E, w)$ be a weighted undirected graph with edge costs $we$, for
  $e \in E$, and let $T$ be a minimum spanning tree in $G$, given in the
  adjacency list representation. Add an edge $e'$ to $E$ of cost $we'$,
  obtaining a new weighted graph $G' = (V, E \cup {e'}, w)$. Give an efficient
  algorithm to compute the minimum spanning tree in $G'$. Present pseudocode,
  analyize the running time and prove correctness. Give a
  $O(\lvert V \rvert)$-algorithm for full credit.

The function below uses Kruskal's algorithm to create a new MST with an extra
edge. When a cycle is found, the largest weight edge is removed from the final
tree.

```java
List<Edge> createMstWithEdge(int n, List<Edge> T, Edge newEdge) {
    List<Edge> combined = new ArrayList<>(T);
    combined.add(newEdge);
    combined.sort((e1, e2) -> e1.weight - e2.weight);

    List<Edge> result = new ArrayList<>();
    DisjointSet union = new DisjointSet(n);

    for (Edge e : combined) {
        if (!union.union(e.u, e.v)) {
            continue;
        }
        result.add(e);
        if (result.size() == n - 1) {
            break;
        }
    }
    return result;
}
```

Like the original Kruskal's algorithm, the running time is $O(m \lg(m))$ where
$m = \lvert E \rvert$. This solution is not optimal because it recreates the MST
instead of modifying the input tree. To modify existing tree, MST $T$ needs to
be converted to an adjacency list for easier traversal and modification.

```java
List<List<Edge>> buildAdjacencyList(int n, List<Edge> T) {
    List<List<Edge>> result = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        result.add(new ArrayList<>());
    }

    for (Edge e : T) {
        result.get(e.u).add(e);
        result.get(e.v).add(e);
    }
    return result;
}
```

Using DFS, the improved algorithm finds the largest weight edge in the cycle to
replace with the new edge.

```java
boolean updateMstWithEdge(int n, List<List<Edge>> T, Edge newEdge) {
    int[] parents = new int[n];
    Edge[] parentEdges = new Edge[n];
    boolean[] visits = new boolean[n];
    Arrays.fill(parents, -1);

    Stack<Integer> stack = new Stack<>();
    stack.push(newEdge.u);
    visits[newEdge.u] = true;
    while (!stack.isEmpty()) {
        int current = stack.pop();
        if (current == newEdge.v) {
            break;
        }
        for (Edge e : T.get(current)) {
            int next = e.u == current ? e.v : e.u;
            if (visits[next]) {
                continue;
            }
            visits[next] = true;
            parents[next] = current;
            parentEdges[next] = e;
            stack.push(next);
        }
    }

    Edge maxEdge;
    int maxWidth = Integer.MIN_VALUE;
    int current = newEdge.v;
    while (current != newEdge.u) {
        Edge e = parentEdges[current];
        if (e.weight > maxWidth) {
            maxWidth = e.weight;
            maxEdge = e;
        }
        current = parents[current];
    }

    if (newEdge.weight >= maxWidth) {
        return false;
    }
    T.get(maxEdge.u).remove(maxEdge);
    T.get(maxEdge.v).remove(maxEdge);
    T.get(newEdge.u).add(newEdge);
    T.get(newEdge.v).add(newEdge);
    return true;
}
```

The running time is dominated by the DFS traversal, which is $O(n)$ where
$n = \lvert V \rvert$.
