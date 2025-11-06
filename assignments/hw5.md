# [Homework 5](https://github.com/hanggrian/IIT-CS535/blob/assets/assignments/hw5.pdf)

## Problem 1

> A jogger wants to follow the least undesirable cycle of roads starting at her
  home. Each road has an "index of undesirability" (a positive integer) and can
  be traversed in either direction; the jogger must follow a nonempty cycle of
  roads and no road can be used twice. Formulated as a graph problem, the jogger
  has an undirected weighted multigraph $G = (V, E)$, and must determine the
  nonempty cycle of minimum weight starting (and ending) at vertex s, where the
  weight of a cycle is the sum of the weights of its edges.
>
> 1.  Show how to use multiple applications of Dijkstra’s shortest path
      algorithm to obtain the optimum jogger’s route in time
      $O(|V|^2 \cdot \lg(|V|) + |E||V|)$.
>
>     Be precise: each time you want to use Dijkstra’s explain which graph is
      the input of the algorithm. Justify the overall running time.
> 1.  Prove the following. Let $T$ be the shortest path tree constructed by
      Dijkstra’s shortest path algorithm for starting vertex s in G. Then there
      exists an optimum jogger’s route that has all but one of its edges in $T$,
      and furthermore, s is the only common ancestor in $T$ of the endpoints of
      the edge not in $T$.
> 1.  Use the result above and Dijkstra’s shortest path algorithm and your own
      algorithm (which you need to describe) to find an optimum jogger’s route
      in time $O(|V| \cdot \lg(|V|) + |E|)$. Discuss the running time and
      correctness.

## Problem 2

> Let $G = (V, E, c, s, t)$ be a flow network with integer capacities, and let
  $f$ be an integral maximum flow in $G$. Let $G' = (V, E, c', s, t)$ differ
  from $G$ on one single edge $e: c'(e) = c(e) − 1$. Give a $O(|V| + |E|)$-time
  algorithm to obtain a maximum flow $f'$ in $G'$.

## Problem 3

> Dining problem. Several families go out to dinner together. To increase their
  social interaction, they would like to sit at tables so that no two members of
  the same family are at the same table. Show how to formulate finding a seating
  arrangement that meets this objective as a maximum flow problem. Assume that
  $q$ tables are available and that the $j$<sup>th</sup> table has seating
  capacity of $b(j)$. Also assume there are $p$ families and that the
  $i$<sup>th</sup> family has $a(i)$ members.
