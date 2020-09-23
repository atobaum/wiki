---
title: "웹소켓에 대한 소고"
summary: "웹소켓을 알아보자"
layout: wiki
parent: 
tags:
  - Web
  - Web Socket
toc: true
latex: false
date: 2020-09-23T17:31:30+0900
lastmod: 2020-09-23T17:31:30+0900
---
## 개요
웹소켓은 먼저 HTTP로 handshake를 한다.

클라이언트는 먼저 HTTP 요청을 서버로 보내 프로토콜을 websocket으로 바꾸기를 요청한다:
```http
GET /chat HTTP/1.1
Host: example.com:8000
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13

```

이때 `Sec-WebSocket-Key`는 서버와 웹소켓 악수(handshake)를 정상적으로 하기 위한 값이다. 이 값은 클라이언트에서 랜덤으로 만든 16 byte 값을 base64로 인코딩 한 것이다.

서버는 요청을 검증하고 다음 응답을 보낸다.
```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=

```

서버가 보내는 `Sec-WebSocket-Accept`는 클라이언트의 `Sec-WebSocket-Key`에다가 `258EAFA5-E914-47DA-95CA-C5AB0DC85B11`를 연결(문자열로)하고 SHA-1 해시값을 base64로 인코딩한 값이다.

이 값은 클라이언트가 웹소켓을 지원하지 않는 서버에 연결한 것으로 착각하는 것을 막기 위한 값이다.


![요청 예시](/images/chatting-with-websocket/request-example.jpg)
- 요청 예시

## Subprotocol
웹소켓은 subprotocol을 가질 수 있다. 클라이언트가 요청 시 `Sec-WebSocket-Protocol` 헤더이 지정하면(comma-seperated list) 서버는 그에 따라 적절하게 처리할 수 있다.

## 브라우저에서 사용해보자 
### 연결하기
```javascript
let ws = new WebSocket("ws://localhost:3000");
let ws = new WebSocket("ws://localhost:3000", "echo"); // 프로토콜을 줄 수 있다.
let ws = new WebSocket("ws://localhost:3000", ["echo", "chat"]); // 여러개 줄 수도 있다.
```

WebSocket은 `CONNECTING, OPEN, CLOSING, CLOSED`의 네가지 상태를 가지고 있고 값은 `WebSocket.<status>`에 정의되어있다.

연결이 되면 `OPEN` 상태로 바뀌고 그때부터 메시지를 전송할 수 있다.
ws.readystate로 현재 상태를 알 수 있다.

### 전송하기
```javascript
ws.send("message");
```

보낼 수 있는 데이터는 String, Blob, ArrayBuffer로 JSON을 보내려면 stringify 해서 보내면 된다.

### 전송 받기
```javascript
ws.onmessage = (evt) => {
	console.log(evt.data);
}
```

### 연결 끊기
```javascript
ws.close();
```

### 핸들러
WebSocket은 `onopen, onclose, onerror, onmessage` 핸들러를 가질 수 있다.


## Node로 서버 구현
많은 코드가 생략되었습니다.
```javascript
var http = require("http");
var webSocketServer = require("websocket").server;

var connections = [];
var server = http.createServer();
var wsServer = new webSocketServer({
  httpServer: server,
});

wsServer.on("request", (req) => {
  // chatting 프로토콜을 받는다.
  var connection = req.accept("chatting");

  connections.push(connection);
  console.log(new Date() + " Connection accepted. Now " + connections.length);

  connection.on("message", (message) => {
    if (message.type === "utf8")
      // 브로드캐스팅
      connections.forEach((c) => c.send(message.utf8Data));
  });

  connection.on("close", () => {
    connections = connections.filter((c) => c != connection);
    console.log(
      new Date() + " Connection disconnected. Now " + connections.length
    );
  });
});

server.listen(3000, () => {
  console.log("Express start to lsten port 3000");
});
```

## Example
[Github](https://github.com/atobaum/simple-chatting-websocket-example)에 간단한 예제를 올려놓았다.

## Reference
- [MDN](https://developer.mozilla.org/ko/docs/WebSockets/Writing_WebSocket_client_applications)
- [High Performance Browser Networking](https://hpbn.co/websocket/)
- [RFC6455](https://tools.ietf.org/html/rfc6455)
