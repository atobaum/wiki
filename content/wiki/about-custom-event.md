---
title: "Javascript에서 이벤트를 만들어보자."
summary: "Let's create and emit custom event in javascript"
layout: wiki
parent:
tags: ["Javascript", "Event"]
toc: true
latex: false
date: 2020-07-23
lastmod: 2020-07-25
---

## 개요

자바스크립트는 이벤트를 많이 이용한다. 이 글에서는 커스텀 이벤트를 만들어보고 이를 이용해 삶을 편하게 만들어보자.

## 이벤트 만들기

```javascript
const event = new Event("testevent1", { bubbles: true });
```

첫번째 인자는 이벤트의 이름이고 두번째 인자 이벤트의 옵션이다. 옵션은 생략할 수 있다.
기본값은 버블링이 안되기 때문에 `bubbles: true`를 주어야 버블링이 일어난다. `bubbles` 외에 `cancelable`과 `composed`도 올 수 있다.

자세한 사항은 [MDN](https://developer.mozilla.org/ko/docs/Web/API/Event/Event)을 참고하자.

## 이벤트 발생시키기

DOM 객체의 dispatchEvent를 이용해 다음과 같이 이벤트를 발생시킬 수 있다.

```javascript
document.body.addEventListener("testevent1" (evt)=>{alert("testevent1이 발생되었어요.")})
document.body.dispatchEvent(event);
```

## 이벤트에 추가 정보 담아서 보내기

CustomEvent를 이용하면 이벤트에 사용자가 추가 정보를 담아서 만들수도 있다. 자세한 내용은 [MDN CustomEvent()](https://developer.mozilla.org/ko/docs/Web/API/CustomEvent/CustomEvent)을 참고하자.

```javascript
const customEvent = new CustomEvent("customevent", {
  bubbles: true,
  detail: { id: 1 },
});
```

그리고 핸들러에서 다음과 같이 데이터를 얻을 수 있다:

```javascript
document.body.dispatchEvent(customEvent);
document.body.addEventListener("customevent", (evt) => {
  alert(evt.detail.id); //1
});
```

## 어디에 쓸까?

[우아한테크코스 투두 프로젝트](https://github.com/woowa-techcamp-2020/todo-10)에서 사용자가 한 일을 옆에 표시해야하는 일이 있었다. 아이템을 만들고 삭제하거나 옮길 때마다 로그를 추가해야하는 데 각 행동을 처리하는 코드는 여러군데에 있었다. 이 때 각각의 장소에서 `log` 이벤트를 만들어서 발생시키고 상위 DOM에서 이벤트 핸들러를 등록시켜 모든 로그 이벤트를 처리했더니 상호 의존성이 줄어서 관리하기 편했다.

![스크린샷](https://user-images.githubusercontent.com/8086328/88454260-360f7900-cea9-11ea-82c8-0de21112cd14.png)

### 예제

<p class="codepen" data-height="512" data-theme-id="dark" data-default-tab="js,result" data-user="atobaum" data-slug-hash="mdVoMxW" style="height: 512px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CustomEventExample">
  <span>See the Pen <a href="https://codepen.io/atobaum/pen/mdVoMxW">
  CustomEventExample</a> by atobaum (<a href="https://codepen.io/atobaum">@atobaum</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
