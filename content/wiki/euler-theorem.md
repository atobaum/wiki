---
title: "오일러 정리"
summary: '오일러 정리에 대해 알아보자'
layout: wiki
parent: ''
tags: [수학]
toc: false
latex: true
date: 2020-10-27T18:22:40+09:00
lastmod: 2020-10-27T18:22:40+09:00
---

- 간단한 군론과 정수론이 필요합니다.

## 오일러 정리

a, n이 서로소인 정수이면, $a^{\phi(n)} = 1 mod n$ 이다.

### lemma
$\overline{0} \neq \overline{a} \in \mathbb{Z}_n$일 때 다음은 동치
1. a와 n은 서로소
2. $\overline{a}$ is a unit in $\mathbb{Z}_n$
3. $\overline{a}$ is a zero divisor in $\mathbb{Z}_n$

증명은 숙제

### 증명

$(\mathbb{Z}_n)^{\times}$는 group이고 $|(\mathbb{Z}_n)^{\times}| = \phi(n)$이다(위의  lemma).

한편 $\overline{a} \in (\mathbb{Z}_n)^{\times}$이고(a하고 n이 서로소이므로), $| \overline{a} | = |(\mathbb{Z}_n)^{\times}|$이므로(Lagrange theorem), $\overline{a}^{\phi(n)} = \overline{1}$이다. 

### Cor] 페르마 소정리

p는 소수, a는 그냥 정수. a하고 p가 서로소이면 $a^{p-1} = 1 mod p$ 이다.

## Reference

- 이인석, 대수학