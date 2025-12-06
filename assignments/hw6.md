# [Homework 6](https://github.com/hanggrian/IIT-CS535/blob/assets/assignments/hw6.pdf)

## Problem 1

> The `SET-COVERAGE` problem has as input a collection of $m$ sets
  $S_1, S_2, \ldots, S_m$, (given each as a list of distinct elements) and
  positive integers $K$ and $R$, and asks if there exists $K$ indices
  $1 \leq i_1 < i_2 < \ldots < i_K \leq m$ (called *subcollection*) such that
  $|\cup_{j = 1}^K S_{i_j}| \geq R$ (the union of the sets in the subcollection
  has size at least $R$).
>
> `MAXIMUM-SET-COVERAGE` has as input a collection of $m$ sets $S_1, S_2,
  \ldots, S_m$, (given each as a list of distinct elements) and positive
  integers $K$, and as objective find $K$ indices
  $1 \leq i_1 < i_2 < \ldots < i_K \leq m$ such that $|\cup_{j=1}^K S_{i_j}|$ is
  maximized. In words, choose $K$ sets such that they "cover" the maximum number
  of elements, an element being covered if it belongs to any of the chosen sets.
>
> Assume there is a black-box polynomial-time algorithm for `SET-COVERAGE`, the
  decision version of `MAXIMUM-SET-COVERAGE`. That is, the algorithm can answer
  in polynomial-time if, given a collection of $m$ finite sets
  $S_1, S_2, \ldots, S_m$ and positive integers $K$ and $R$, there is a
  subcollection with $K$ sets such that their union has size at least $R$. Use
  this algorithm (note that you can use it repeatedly) to get a polynomial-time
  algorithm for `MAXIMUM-SET-COVERAGE`, or in other words, find a sub-collection
  with $K$ sets whose union has the maximum size.
>
> Since you are likely to use subroutines, make sure you completely describe the
  input for each. Present pseudocode, analyze the running time and argue
  correctness.

The `SET-COVERAGE` determines whether there exists a selection of $K$ sets whose
union covers at least $R$ elements in polynomial time. Using this black-box
algorithm, `MAXIMUM-SET-COVERAGE` returns indices of $K$ sets whose union covers
the largest number of elements. The idea is to use binary search to find $R$,
the maximum number of elements that can be covered by any $K$ sets, from
$0 \ldots n$, where $n$ is the size of the union of all sets. Then, greedily
select sets while ensuring that the remaining sets can still cover at least $R$
elements.

```java
// Returns true if subset of K sets covers at least R elements.
boolean setCoverage(List<Set<Integer>> S, int K, int R);

List<Integer> maximumSetCoverage(List<Set<Integer>> S, int K) {
  Set<Integer> union = new HashSet<>();
  for (Set<Integer> set : S) {
    union.addAll(set);
  }
  int m = S.size();
  int n = union.size();
  int low = 0;
  int high = n;
  int R = 0;
  while (low < high) {
    int mid = low + (high - low) / 2;
    if (setCoverage(S, K, mid)) {
      R = mid;
      low = mid + 1;
    } else {
      high = mid - 1;
    }
  }

  List<Integer> result = new ArrayList<>();
  boolean[] taken = new boolean[m];
  for (int step = 0; step < K; step++) {
    for (int i = 0; i < m; i++) {
      if (taken[i]) {
        continue;
      }
      taken[i] = true;
      List<Set<Integer>> remaining = new ArrayList<>();
      for (int j = 0; j < m; j++) {
        if (!taken[j]) {
          remaining.add(S.get(j));
        }
      }
      boolean possible = setCoverage(remaining, K - step - 1, R);
      if (possible) {
        result.add(i);
        break;
      } else {
        taken[i] = false;
      }
    }
  }
  return result;
}
```

The union of all sets is computed in $O(n)$ time, the union size after inserting
all distinct elements from $m$ sets. Binary search is known to take $O(\lg(n))$
time, multiplied by the time for `SET-COVERAGE`, denoted as $P(m, n)$. The
greedy selection dominates the running time. It iterates $K$ times to select $K$
sets, and in each iteration, it may check up to $m$ sets. For each check, it
constructs the remaining list in $O(m)$ time and calls `SET-COVERAGE` once.

$$
\begin{align}
  T(\texttt{SET-COVERAGE}) &= P(m, n) \\
  T(\texttt{MAXIMUM-SET-COVERAGE}) &=
    T(\textsf{union}) +
    T(\textsf{binary search}) +
    T(\textsf{greedy selection}) \\
  &=
    O(n) +
    (O(\lg(n)) \cdot P(m, n)) +
    \underbrace{O(K \cdot m \cdot (m + P(m, n)))}_\textsf{dominant} \\
  &= \mathbf{O(K \cdot m^2 + K \cdot m \cdot P(m, n))}
\end{align}
$$

To prove that the greedy solution returns the correct indices, they must
achieve coverage of $R$ elements, and that $R$ is the maximum possible coverage
found by the binary search. If there is a larger coverage than $R$, the search
would have found it in the first `SET-COVERAGE` call. In the greedy selection,
the `SET-COVERAGE` ensures that the remaining sets can still cover at least $R$
elements after adding a new set.

## Problem 2

> For a collection of $m$ finite sets $S_1, S_2, \ldots, S_m$ (given each as a
  list of distinct elements), a *cover* is a subcollection of
  $S_1, S_2, \ldots, S_m$ such that the union of these sets equals the union of
  all sets (in which case we say that all elements are covered).
>
> Consider the following problem, called `MINIMUM-SET-COVER`: given a collection
  of $m$ finite sets $S_1, S_2, \ldots, S_m$, find a cover with minimum number
  of sets.
>
> `SET-COVER` is the decision version of `MINIMUM-SET-COVER`. In `SET-COVER`, a
  number $q$ is given as part of the input in addition to the $m$ sets, and the
  yes-no question is if there exists a cover with $q$ sets. Prove that
  `SET-COVER` is NP-hard, using only the handout (precisely, the NP-hard
  problems given there).
>
> **`MINIMUM-SET-COVER` and `MAXIMUM-SET-COVERAGE` are related but not the same
  problem!**
>
> > **Definition 9** A decision problem B is NP-complete if it satisfies two
    conditions:
> >
> > 1.  $B$ is in NP, and
> > 1.  every $A$ in NP is polynomial time reducible to $B$.
> >
> > **Theorem 10** If $B$ is NP-complete and $B \in P$, then $P = NP$.
> >
> > **Theorem 11** If $B$ is NP-complete and $B \leq_P C$ for $C$ in NP, then
    $C$ is NP-complete.
> >
> > **Theorem 12** $\texttt{CNF} - \texttt{SAT}$ is NP-complete. $\texttt{3SAT}$
    is NP-complete.
> >
> > This theorem is Theorem 7.27, the Cook-Levin theorem, in a stronger form.
> >
> > **Corollary 13** `CLIQUE` is NP-complete.
> >
> > **Theorem 14** `INDEPENDENT-SET` is NP-complete.
> >
> > **Theorem 15** `VERTEX-COVER` is NP-complete.
> >
> > **Theorem 16** `HAMPATH` is NP-complete.
> >
> > **Theorem 17** $\texttt{SUBSET} - \texttt{SUM}$ is NP-complete.

It is proven in the handout, specifically **Definition 9,** that one problem is
NP-complete if it is NP and there is a polynomial-time reduction from every
other NP problem to it. We know from **Theorem 15** that `VERTEX-COVER` is
NP-complete. If we can reduce `VERTEX-COVER` such that
$\texttt{VERTEX-COVER} \leq_P \texttt{SET-COVER}$, then by **Theorem 11,**
`SET-COVER` is NP-complete, and thus NP-hard.

The `VERTEX-COVER` takes an undirected graph $G = (V, E)$ and a positive integer
$k \leq |V|$ to check whether there exists a subset of vertices $V' \subseteq V$
such that every edge in $E$ is incident to at least one vertex in $V'$. On the
other hand, `SET-COVER` takes a collection of $m$ finite sets
$S_1, S_2, \ldots, S_m$ and an integer $q$ to check whether there exists a
cover with $q$ sets. Given $G$ for `VERTEX-COVER`, create a union $U$ of all
edges in $E$. For each vertex $v_i \in V$, create a set $S_i$ that contains all
edges incident to $v_i$. The selected sets in `SET-COVER` must have all edges in
$U$ covered.

$$
\begin{align}
  U &= E \\
  S_i &= \{ e \in E \mid e \text{ is incident to } v_i \} \\
  \texttt{VERTEX-COVER}(G, k) &\mapsto
    \texttt{SET-COVER}(\{S_i\}, k) \\
  \bigcup_{i} \texttt{SET-COVER}(\{S_i\}, k) &= U = E
\end{align}
$$

Since `VERTEX-COVER` can be reduced to `SET-COVER` in polynomial time,
`VERTEX-COVER` should only return true if `SET-COVER` also returns true. Suppose
$G$ has a cover $C \subseteq V$ of size at most $k$. Select the corresponding
sets $S_i$ for each vertex $v_i \in C$. Since every edge in $E$ is incident to
at least one vertex in $C$, every edge in $U$ is covered by at least one set in
the selected sets. Thus, `SET-COVER` returns true.

Conversely, suppose there exists a cover of size at most $k$ in `SET-COVER`. For
each selected set $S_i$, select the corresponding vertex $v_i$. Since every edge
in $U$ is covered by at least one selected set, every edge in $E$ is incident to
at least one selected vertex. Thus, `VERTEX-COVER` returns true. Therefore,
we conclude that `SET-COVER` is NP-hard since a polynomial-time reduction
$\texttt{VERTEX-COVER} \leq_P \texttt{SET-COVER}$ is possible.

## Problem 3

> Consider the following problem, called `LONGEST SIMPLE s-t PATH`: Given a
  directed graph $G = (V, E, w)$, where $w(e)$ is defined as a non-negative
  integer for each edge $e \in E$, vertices $s, t \in V$, and a positive integer
  $K$, answer `YES` if there is a simple $s - t$ path of total weight at least
  $K$. An $s - t$ path starts at $s$ and ends at $t$, a path is *simple* if it
  does not repeat any vertices, and the total weight of a path is the sum of the
  weights of its edges.
>
> Prove that the `LONGEST SIMPLE s-t PATH` problem is NP-hard. Simplest
  reduction is from `HAMPATH`.

`HAMPATH` is NP-complete according to **Theorem 16.** We need to reduce
`HAMPATH` so that $\texttt{HAMPATH} \leq_P \texttt{LONGEST SIMPLE s-t PATH}$.
Let $G = (V, E)$ be the directed graph and $s, t \in V$ be the vertices given to
`HAMPATH`. Construct a new graph $G' = (V, E', w)$, where each edge in $E'$ has
weight 1. The value of $K$ is set to $|V| - 1$, which is the maximum number of
edges the longest simple $s - t$ path can have.

$$
\begin{align}
  E' &= E \\
  K &= |V| - 1 \\
  w(e) &= 1 \\
  \texttt{HAMPATH}(G, s, t) &\mapsto
    \texttt{LONGEST SIMPLE s-t PATH}(G', s, t, K)
\end{align}
$$

If there exists a path from $s$ to $t$ that visits every vertex exactly once in
$G$, then it must have $|V| - 1$ edges. In $G'$, the total weight of this path
is also $|V| - 1$ since each edge has weight 1. In this case,
`LONGEST SIMPLE s-t PATH` returns true since the path has total weight at least
$K = |V| - 1$.

In the other direction, if there exists a simple $s - t$ path in $G'$ with total
weight at least $K = |V| - 1$, then the path must have at least $|V| - 1$ edges.
However, a simple path can have at most $|V| - 1$ edges since it cannot revisit
any vertex. Therefore, the path has exactly $|V| - 1$ edges and visits all $|V|$
vertices exactly once. The same path also exists in $G$ (since $E' = E$), making
`HAMPATH` return true. Thus, `LONGEST SIMPLE s-t PATH` is NP-hard because
polynomial-time reduction
$\texttt{HAMPATH} \leq_P \texttt{LONGEST SIMPLE s-t PATH}$ is possible.
