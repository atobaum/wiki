---
title: "베지어 곡선의 explicit form 증명"
summary: "베지어 곡선의 explicit form을 증명한다. 보지마세요."
layout: wiki
parent: 
tags: 
  - Math
  - Proof
toc: true
latex: true
date: 2020-08-09T17:14:56+0900
lastmod: 2020-08-09T17:14:56+0900
---

# Lemma

$$
\binom{n-1}{i-1} + \binom{n-1}{i} = \binom{n}{i}
$$

$\binom{n}{i}$는 binomial coefficient이다.

## Proof

$$
\begin{aligned}
\binom{n-1}{i-1} + \binom{n-1}{i} &= \frac{(n-1)!}{(i-1)!(n-i)!}+\frac{(n-1)!}{i!(n-i-1)!} \\\\
&=(n-1)!\frac{(n-i)+i}{i!(n-i)!} \\\\
&=\frac{n!}{i!(n-i)!} \\\\
&=\binom{n}{i} \quad \square
\end{aligned}
$$

#  Explicit form of Bézier curve


다음 두 식은 동치이다.
$$
\begin{aligned}
B_{P_0}^{(0)}(t) &= P_0,\ t\in[0,1] \\\\
B_{P_0,P_1,...,P_n}^{(n)}(t) &= (1-t)B_{P_0,...,P_{n-1}}(t)+tB_{P_1,...,P_n}(t),\ t\in[0,1] \qquad if \quad n > 0 \qquad -(1)
\end{aligned}
$$
***

$$
B_{P_0,...,P_n}^{(n)}(t) = \sum^n_{i=0} \binom{n}{i}(1-t)^{n-i}t^iP_i \qquad -(2)
$$

## Proof
간단하게 $B_{P_0,...,P_n}(t)$을 $B_{0...n}(t)$이라고 쓰자.

By induction.

i) When n = 0: trivial

ii) When n > 0:

$$
\begin{aligned}
B_{0...n}^{(n)}(t) &= (1-t)B_{0...(n-1)}+tB_{1...n} \\\\
&= [(1-t)\sum^{n-1}_{i=0} \binom{n-1}{i} (1-t)^{n-1-i}t^iP_i]+[t\sum^{n-1}_{i=0} \binom{n-1}{i} (1-t)^{n-1-i}t^iP_{i+1}] \\\\
&= \sum^{n-1}_{i=0} \binom{n-1}{i} (1-t)^{n-i}t^iP_i+\sum^{n}_{i=1} \binom{n-1}{i-1} (1-t)^{n-i}t^{i}P_{i} \\\\
&= \binom{n-1}{0}(1-t)^nP_0+\sum^{n-1}_{i=1} \left[ \binom{n-1}{i}+\binom{n-1}{i-1} \right] (1-t)^{n-i}t^iP_i+\binom{n-1}{n-1}t^nP_n \\\\
&= (1-t)^nP_0+\sum^{n-1}_{i=1}\binom{n}{i}(1-t)^{n-i}t^iP_i+t^nP_n \qquad by\ above\ lemma\\\\
&= \sum^{n}_{i=0}\binom{n}{i}(1-t)^{n-i}t^iP_i \qquad \square
\end{aligned}
$$

