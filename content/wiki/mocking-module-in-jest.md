---
title: "Jest에서 모듈 목킹하기"
summary: ""
layout: wiki
parent: 
tags: ["Javascript", "Jest", "Test"]
toc: true
latex: false
draft: true
date: 2020-09-22T21:19:27+0900
lastmod: 2020-09-22T21:19:27+0900
---

# 개요
부수효과가 있는 함수를 임포트해서 사용하는 함수를 어떻게 테스트할까? 눈앞이 깜깜해진다. Jest는 테스트를 위한 여러가지 유틸리티를 제공해준다. 역시 모듈을 mocking하는 함수도 제공해준다.

```javascript
// dependency.js

export function log(){
	// some side effect
	return "side effect"
}
export default function(){
	return "side effect1"
}

// module.js
import dependency, { log } from "./dependency";

export defualt function exampleFunc(){
	return [dependency(), log()];
}
```

```javascript
// test.js
import exampleFunc from "./module";

jest.mock("./dependency", ()=>{
	log: () => "mocked log"
});

test("", ()=>{

})

```
