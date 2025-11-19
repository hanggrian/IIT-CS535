# [Homework 5](https://github.com/hanggrian/IIT-CS535/blob/assets/assignments/hw5.pdf)

## Problem 1

> A jogger wants to follow the least undesirable cycle of roads starting at her
  home. Each road has an "index of undesirability" (a positive integer) and can
  be traversed in either direction; the jogger must follow a nonempty cycle of
  roads and no road can be used twice. Formulated as a graph problem, the jogger
  has an undirected weighted multigraph $G = (V, E)$, and must determine the
  nonempty cycle of minimum weight starting (and ending) at vertex $s$, where
  the weight of a cycle is the sum of the weights of its edges.
>
> > ```
> > DIJKSTRA(G, w, s)
> > 1   INITIALIZE-SINGLE-SOURCE(G, s)
> > 2   S = ∅
> > 3   Q = G.V
> > 4   while Q ≠ ∅
> > 5     u = EXTRACT-MIN(Q)
> > 6     S = S ∪ {u}
> > 7     for each vertex v ∈ G.Adj[u]
> > 8       RELAX(u, v, w)
> >
> > INITIALIZE-SINGLE-SOURCE(G, s)
> > 1   for each vertex v ∈ G.V
> > 2     v.d = ∞
> > 3     v.π = NIL
> > 4   s.d = 0
> >
> > RELAX(u, v, w)
> > 1   if v.d > u.d + w(u, v)
> > 2     v.d = u.d + w(u, v)
> > 3     v.π = u
> > ```
>
> 1.  Show how to use multiple applications of Dijkstra's shortest path
      algorithm to obtain the optimum jogger's route in time
      $O(|V|^2 \cdot \lg(|V|) + |E||V|)$.
>
>     Be precise: each time you want to use Dijkstra's explain which graph is
      the input of the algorithm. Justify the overall running time.

Dijkstra's algorithm finds the shortest path from source vertex $s$ to all other
vertices in graph $G$. Given that indices of undesirability are the weights
of the edges, we can find the shortest cycle by considering every vertex $v$
neighbor to source $s$. For every route $e = (s, v) \in E$, create a new graph
$G' = (V, E - \{e\})$ and run `DIJKSTRA` on $G'$ with source $v$. After
execution, the value of `s.d` is the shortest distance from $v \to s$. If
distance $d$ is not infinity, then $w(s, v) + d$ is the weight of the cycle that
starts and ends at $s$.

In a simple graph, there can be at most $|V| - 1$ vertices connected to source
$s$. Dijkstra's algorithm is known to run in $O(|V| \cdot \lg(|V|) + |E|)$ time
using a binary min-heap priority queue.

$$
\begin{align}
  T &=
    (|V| - 1) \cdot
    (T(\textsf{creating}\ G') + T(\texttt{DIJKSTRA(G')})) \\
  &=
    (\underbrace{|V|}_\textsf{dominant} - 1) \cdot
    (O(|E|) + \underbrace{O(|V| \cdot \lg(|V|) + |E|)}_\textsf{dominant}) \\
  &= \mathbf{O(|V|^2 \cdot \lg(|V|) + |E||V|)}
\end{align}
$$

> 2.  Prove the following. Let $T$ be the shortest path tree constructed by
      Dijkstra's shortest path algorithm for starting vertex $s$ in $G$. Then,
      there exists an optimum jogger's route that has all but one of its edges
      in $T$, and furthermore, $s$ is the only common ancestor in $T$ of the
      endpoints of the edge not in $T$.

We need to show that there is an optimal cycle using edge $e = (u, v) \notin T$,
such that $u$ and $v$ have lowest common ancestor $s$ in shortest-path tree $T$.
Consider any optimal cycle $C$ from $s \to u \to v \to s$, which can be
calculated as $d(s, u) + w(u, v) + d(v, s)$. We define another cycle $C'$ that
potentially has shorter distance $d(s, u)$ or $d(s, v)$. All edges except
$e \in C'$ are in $T$.

To prove that $e$ cannot be in $T$, suppose there is a unique path between two
vertices $u$ and $v$. If $u$ is an ancestor of $v$, the path from $v \to s$ must
pass through $u$ in cycle $C'$. This means that edge $e = (u, v)$ is traversed
twice, which is not allowed.

Finally, prove that $s$ is the lowest common ancestor of $u$ and $v$. Let $x$ be
another common ancestor of $u$ and $v$. Then, $x$ must be on the path from
$s \to u$ and $s \to v$ in $T$. The edges on the path between $s$ and $x$ are
traversed twice in $C'$. Therefore, $s$ must be the only lowest common ancestor.

$$
\begin{align}
  C' &: d(s, u) + w(u, v) + d(v, s) \\
  &:
    s \to \ldots \to x \to \ldots \to u \to
    v \to \ldots \to x \to \ldots \to s \\
\end{align}
$$

> 3.  Use the result above and Dijkstra's shortest path algorithm and your own
      algorithm (which you need to describe) to find an optimum jogger's route
      in time $O(|V| \cdot \lg(|V|) + |E|)$. Discuss the running time and
      correctness.

First, run Dijkstra's algorithm as described in the textbook once on graph $G$
with source $s$, obtaining shortest-path tree $T$ and distances $d(s, v)$ for
each vertex $v \in V$. Next, calculate the cycle weight for every edge
$e \notin T$. We keep a pointer to the minimum-weight cycle found so far.

```java
class Graph {
  List<Vertex> V; // vertices
  List<Edge> E; // edges
}

class Edge {
  int u; // starting vertex
  int v; // ending vertex
  int w; // edge weight
}

Edge findOptimalCycle(Graph G, int s) {
  List<Edge> optimalEdges = DIJKSTRA(G, s).E;
  int minCycleWeight = Integer.MAX_VALUE;
  Edge optimalCycle = null;

  for (Edge edge : G.E) {
    if (optimalEdges.contains(edge)) {
      continue;
    }
    int cycleWeight = d(s, edge.u) + edge.w + d(s, edge.v);
    if (cycleWeight < minCycleWeight) {
      minCycleWeight = cycleWeight;
      optimalCycle = edge;
    }
  }
  return optimalCycle;
}
```

Our algorithm considers every $e \notin T$. Since $d(s, u)$ and $d(s, v)$ are
the shortest distances, we are guaranteed to find the optimal cycle, which must
use some non-tree edge.

$$
\begin{align}
  T &= T(\texttt{DIJKSTRA(G)}) + T(\textsf{iterating non-tree edges}) \\
  &= \underbrace{O(|V| \cdot \lg(|V|) + |E|)}_\textsf{dominant}
    + O(|E| - |V| - 1) \\
  &= \mathbf{O(|V| \cdot \lg(|V|) + |E|)}
\end{align}
$$

## Problem 2

> Let $G = (V, E, c, s, t)$ be a flow network with integer capacities, and let
  $f$ be an integral maximum flow in $G$. Let $G' = (V, E, c', s, t)$ differ
  from $G$ on one single edge $e: c'(e) = c(e) − 1$. Give a $O(|V| + |E|)$-time
  algorithm to obtain a maximum flow $f'$ in $G'$.

The algorithm starts by checking if the flow $f$ on edge $e$ is still feasible
for graph $G'$. If $f(e) \leq c(e) - 1$, then $f$ is still feasible because
decreasing capacity $c(e)$ does not increase the max-flow value. Otherwise,
decreasing $c(e)$ makes $f$ infeasible because $e$ is saturated.

In such a case, we reduce $f(e)$ to $c(e) - 1$ to restore feasibility. This
creates an imbalance where vertex $u$ has excess flow, while $v$ has deficit
flow. To maintain max-flow value, construct the residual graph after updating
$f(e)$. Then, use BFS to find an augmenting path from $v \to u$. If such a path
exists, a flow from $v \to u$ can be routed to rebalance both vertices.

```java
class Graph {
  List<Vertex> V; // vertices
  List<Edge> E; // edges
  int s; // source vertex
  int t; // sink vertex
}

class Edge {
  int u; // starting vertex
  int v; // ending vertex
  int c; // edge weight
  int f; // flow on edge
}

class ResidualEdge {
  int v; // ending vertex
  int c; // residual capacity
  Edge originalEdge; // reference to original edge
  boolean isForward; // true if (u, v) is forward edge
}

void updateMaxFlow(Graph G, Edge e) {
  e.c -= 1;
  if (e.f <= e.c) {
    return;
  }
  e.f = e.c;

  List<List<ResidualEdge>> residual = new ArrayList<>();
  for (int i = 0; i < G.V.size(); i++) {
    residual.add(new ArrayList<>());
  }
  for (Edge edge : G.E) {
    int forward  = edge.c - edge.f;
    int backward = edge.f;
    if (forward > 0) {
      residual
        .get(edge.u)
        .add(new ResidualEdge(edge.v, forward, edge, true));
    }
    if (backward > 0) {
      residual
        .get(edge.v)
        .add(new ResidualEdge(edge.u, backward, edge, false));
    }
  }

  int[] parent = new int[G.V.size()];
  ResidualEdge[] parentEdges = new ResidualEdge[G.V.size()];
  Arrays.fill(parent, -1);

  Queue<Integer> queue = new LinkedList<>();
  queue.add(e.v);
  parent[e.v] = e.v;

  while (!queue.isEmpty() && parent[e.u] == -1) {
    int u = queue.remove();
    for (ResidualEdge edge : residual.get(u)) {
      int v = edge.v;
      if (parent[v] == -1) {
        parent[v] = u;
        parentEdges[v] = edge;
        queue.add(v);
      }
    }
  }

  if (parent[e.u] == -1) {
    return;
  }
  int v = e.u;
  while (v != e.v) {
    ResidualEdge edge = parentEdges[v];
    if (edge.isForward) {
      edge.originalEdge.f += 1;
    } else {
      edge.originalEdge.f -= 1;
    }
    v = parent[v];
  }
}
```

The running time of the algorithm is dominated by the BFS operation to find the
augmenting path in the residual graph. The BFS traversal takes $O(|V| + |E|)$
time to visit each vertex and edge at most once.

$$
\begin{align}
  T &=
    T(\textsf{residual graph}) +
    T(\textsf{find augment}) +
    T(\textsf{update flow}) \\
  &= O(|E|) + \underbrace{O(|V| + |E|)}_\textsf{dominant} + O(|V|) \\
  &= \mathbf{O(|V| + |E|)}
\end{align}
$$

## Problem 3

> Dining problem. Several families go out to dinner together. To increase their
  social interaction, they would like to sit at tables so that no two members of
  the same family are at the same table. Show how to formulate finding a seating
  arrangement that meets this objective as a maximum flow problem. Assume that
  $q$ tables are available and that the $j$-th table has seating capacity of
  $b(j)$. Also assume there are $p$ families and that the $i$-th family has
  $a(i)$ members.

The seating arrangement problem can be represented as a max-flow network
$G = (V, E, c, s, t)$. There are $p$ families and $q$ tables. The set of
vertices $V$ consists of source vertex $s$, sink $t$, $F_i$ for each family
$i$ of $p$ and $T_j$ for each table $j$ of $q$.

The set of edges $E$ corresponds to the seating constraints. First, there is an
edge from source $s$ to each family $F_i$ with capacity $c(s, F_i) = a(i)$,
which is the total number of members in family $i$. Next, two members from the
same family cannot sit at the same table, so there is an edge from family $F_i$
to table $T_j$ with capacity $c(F_i, T_j) = 1$ for all $i$ and $j$. Finally,
each table $T_j$ has an edge to sink $t$ with capacity
$c(T_j, t) = b(j)$, which is the seating capacity of table $j$.

$$
\begin{align}
  i &= 1, 2, \ldots, p \\
  j &= 1, 2, \ldots, q \\
  V &= \{s, t\} \cup \{F_i\} \cup \{T_j\} \\
  E &= \{(s, F_i)\} \cup \{(F_i, T_j)\} \cup \{(T_j, t)\} \\
  c(s, F_i) &= a(i) \\
  c(F_i, T_j) &= 1 \\
  c(T_j, t) &= b(j)
\end{align}
$$

In this network, flow $f = (F_i, T_j)$ is either 0 or 1, indicating whether
there is a member from family $i$ seated at table $j$. Since there is an edge
from family $F_i$ to table $T_j$ with a capacity 1, no two members from the same
family can be seated at the same table.
