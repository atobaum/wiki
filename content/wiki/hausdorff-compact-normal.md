---
title: "Compact Hausdorff space is normal"
summary: "Compact Hausdorff space is normal."
layout: wiki
parent: ''
tags: [Math, Topology]
toc: false
latex: true
date: 2020-06-04T02:58:05+09:00
lastmod: 2020-06-04T02:58:05+09:00
---
The term "space" in this article refers to "topological space".

# Definition
### Hausdorff space
Hausdorff space is a space whoose every two points can be separated by disjoint neighborhoods. In other words:
> A space $X$ is called a Hausdorff space if every distinct points $x$, $y$ in $X$, there are neighborhood U of x and neighborhood V of y which are disjoint.

### Normal space
Normal space is similar but strict than Hausdorff space:

> A space $X$ is said to be normal if every pair of disjoint closed sets $A$ and $B$, there are disjoint neighborhoods $U \supset A$ and $V \supset B$.

# Theorem
> Compact Hausdorff space is normal. In fact, every disjoint compact sets $A$, $B$ of a Hausdorff space, there is disjoint open sets $U \supset A$ and $V \supset B$.

## Proof
Let $X$ be a Hausdorff space and let $A, B \subset X$ be disjoint compact subsets. Since $X$ is Hausdorff, for every pair of points $a \in A$ and $b \in B$, there exist disjoint open subsets, say $U_{ab}, V_{ab}$ so that $a \in U_{ab}$ and $b \in V_{ab}$.

First, fix $a$ and consider a collection $\mathcal{V_a} = \\{ V_{ab}\ |\ b \in B\\}$. This forms an open cover for $B$, so that has a finite subcover $V_a$. WLOG, assume $\mathcal{V_a}$ is finite.

Consider corresponding open set $U_{a} = \bigcap_{V_{ab} \in \mathcal{V_A}} U_{ab}$ (open since finite intersection). Bing $\\{ U_a | a \in A \\}$ an open cover for $A$, there is a finite subcover $U$ for A.

Let $V$ be the intersection of corresponding $V_a$'s. Now we have disjoin open set $U$ and $V$ containing $A$ and $B$ respectively. □

# Reference
* [Compact + Hausdorff = Normal - Math3ma](https://www.math3ma.com/blog/compact-hausdorff-normal)
