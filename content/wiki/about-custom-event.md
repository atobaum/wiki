---
title: "Javascript에서 이벤트를 만들어보자."
summary: "Let's create and emit custom event in javascript"
layout: wiki
parent:
tags: ["Javascript", "Event"]
toc: true
latex: false
date: 2020-07-22T22:49:55+0900
lastmod: 2020-07-22T22:49:55+0900
---

# 개요

자바스크립트는 이벤트를 많이 이용한다. 이 글에서는 커스텀 이벤트를 만들어보고 이를 이용해 삶을 편하게 만들어보자.

# 이벤트 만들기

```javascript
const event = new Event("testevent1", { bubbles: true });
```

두번째 인자는 생략할 수 있다. 참고: [MDN Event()](https://developer.mozilla.org/ko/docs/Web/API/Event/Event)
기본값은 bubbling이 안된다.

# 이벤트 발생시키기

```javascript
document.body.dispatchEvent(event);
document.body.addEventListener("testevent1" (evt)=>{alert("testevent1이 발생되었어요.")})
```

# 이벤트에 추가 정보 담아서 보내기

이벤트에 사용자가 추가 정보를 담아서 만들수도 있다. 자세한 내용은 [MDN CustomEvent()](https://developer.mozilla.org/ko/docs/Web/API/CustomEvent/CustomEvent)을 참고하자.

```javascript
const customEvent = new CustomEvent("customevent", {
  bubbles: true,
  detail: { id: 1 },
});
```

이제 커스텀 이벤트에 있는 데이터를 얻을 수 있다.

```javascript
document.body.dispatchEvent(customEvent);
document.body.addEventListener("customevent", (evt) => {
  alert(evt.detail.id); //1
});
```

<p class="codepen" data-height="265" data-theme-id="dark" data-default-tab="js,result" data-user="atobaum" data-slug-hash="mdVoMxW" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CustomEventExample">
  <span>See the Pen <a href="https://codepen.io/atobaum/pen/mdVoMxW">
  CustomEventExample</a> by atobaum (<a href="https://codepen.io/atobaum">@atobaum</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
