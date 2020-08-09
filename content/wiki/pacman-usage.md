---
title: "pacman 사용법"
summary: 'Arch linux의 패키지매니저 pacman을 사용해보자.'
layout: wiki
parent: "Linux"
tags: ["Arch", "pacman", "Guide"]
toc: true
latex: false
date: 2020-06-01T23:14:00+09:00
lastmod: 2020-06-01T23:25:00+09:00
---
## 패키지 설치
* pacman -S <pkg_name>

## 패키지 검색
* pacman -Ss <pkg_name>
* pacman -Si <pkg_name>: Display detail infomation about a package.
* pacman -Qi <pkg_name>: Same as above but for locally installed.
* pacman -Qdt: List all packages no longer required as dependencies.
* pacman -Qet: List all packages explicitly installed and not required as dependencies.

## 패키지 삭제
* pacman -R <pkg_name>: 패키지만 삭제
* pacman -Rs <pkg_name>: 해당 패키지와 다른 패키지에 필요하지 않은 패키지들도 삭제.

## 업데이트
* pacman -Syu: 전체 업데이트
