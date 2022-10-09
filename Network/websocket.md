## 웹소켓

**[웹소켓이란?]**

HTTP의 경우 클라이언트의 요청이 있을때만 서버가 응답하고, 매번 연결을 맺고 끊는 비용이 발생하기 때문에 실시간성을 보장해야하는 서비스에서는 적합하지 않습니다. 이경우 양방향 통신 방식을 사용하는것이 적합한데 이때 사용할 수 있는 프로토콜이 **웹소켓**입니다.

웹소켓은 2011년 IETF에 의해 표준화(RFC 6455)된 양방향 통신 프로토콜입니다. 웹소켓 연결 수립을 위해 HTTP프로토콜을 사용하며, 기존 HTTP프로토콜의 포트(80, 443)과 호환되어 추가적인 방화벽 설정이 필요하지 않다는 장점이 있습니다. HTML5를 지원하는 대부분의 브라우저가 웹소켓을 지원하고 있습니다.

**[HTTP 과 Websocket의 비교]**

| HTTP | Websocket |
| --- | --- |
| 단방향(클라이언트 → 서버) | 양방향(클라이언트 ↔ 서버) |
| 비연결성 | 연결지향 |
| 필요한 경우에만 서버에 접근하는 서비스에 적합 | 서버와 실시간으로 데이터를 주고받아야 하는 서비스에 적합 |

**[웹소켓과 소켓 연결]**

웹소켓과 소켓은 IP와 PORT를 사용한다는 점에서 유사하지만, **소켓 연결**은 TCP/IP 프로토콜을 기반으로 연결을 맺는 네트워크 연결방식으로 **전송계층**에서 동작합니다. 반면 **웹소켓**의 경우 HTTP를 통해 연결을 수립하며 **애플리케이션 계층**에서 동작한다는 차이가 있습니다.

## 웹소켓의 동작 방식

![test](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fl9GGB%2FbtrN9rhDBBI%2FxtehsqMUbsBycn9E5koCu1%2Fimg.png)

**[연결 단계]**

**클라이언트의 웹소켓 연결 요청**

```json
GET /chat HTTP/1.1
Host: example.com:8000
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

클라이언트는 웹소켓 연결을 수립하기 위해서 HTTP 요청보내야 합니다. 이때 반드시 **HTTP 1.1 버전 이상**이어야 하며, **GET 메서드**를 사용하여 연결을 요청해야 합니다. **Upgrade, Connection 헤더**를 통해 웹소켓 프로토콜로의 업그레이드 요청을 하게됩니다.

**서버측 웹소켓 연결 요청 응답**

```json
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

서버가 클라이언트로부터 웹소켓 연결 요청을 받게되면, 서버는 **101 상태코드**로 프로토콜을 변경하겠다는 응답을 하게 됩니다. 이때 `Sec-WebSocket-Accept` 의 값은 클라이언트가 보낸 `Sec-Websocket-Key` 로부터 계산된 값으로 클라이언트가 계산한 값과 일치하지 않으면 연결이 수립되지 않습니다.

**데이터 프레임**

연결이 수립되고 나면 다음과 같은 데이터 프레임을 통해 데이터를 주고 받게됩니다.

```json
Frame format:

      0                   1                   2                   3
      0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
     +-+-+-+-+-------+-+-------------+-------------------------------+
     |F|R|R|R| opcode|M| Payload len |    Extended payload length    |
     |I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
     |N|V|V|V|       |S|             |   (if payload len==126/127)   |
     | |1|2|3|       |K|             |                               |
     +-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
     |     Extended payload length continued, if payload len == 127  |
     + - - - - - - - - - - - - - - - +-------------------------------+
     |                               |Masking-key, if MASK set to 1  |
     +-------------------------------+-------------------------------+
     | Masking-key (continued)       |          Payload Data         |
     +-------------------------------- - - - - - - - - - - - - - - - +
     :                     Payload Data continued ...                :
     + - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
     |                     Payload Data continued ...                |
     +---------------------------------------------------------------+
```

**웹소켓 종료 요청, 응답**

웹소켓 종료 요청은 클라이언트, 서버 상관없이 보낼수 있습니다. 요청을 받은 상대방은 웹소켓 종료 요청에 대한 응답을 보내고 웹소켓 연결을 종료합니다.

## Websocket Emulation : SockJS

**[Websocket Emulation]**

만약 브라우저에서 websocket을 지원하지 않는다면, **Websocket Emulation**기술을 사용해 웹소켓을 사용하는것처럼 사용자 경험을 제공할 수 있습니다. 대표적으로 SockJS와 Socket.io가 있는데 **SockJS는 스프링**에서, S**ocket.io는 node.js**에서 주로 사용됩니다.

**[SockJS의 동작 방식]**

SockJS클라이언트는 `GET /info` 요청을 통해 브라우저가 어떤 전송방식을 지원하는지 확인합니다. 만약 브라우저가 Websocket을 지원하지 않는다면 WebSocket → HTTP Streaming → HTTP Long Polling 순으로 연결 방식을 바꿉니다. SockJS를 사용하기 위해서는 SockJS 클라이언트를 통해 연결을 요청해야 하며, 서버측은 SockJS를 지원해야 합니다.

## Polling, Long Polling, Streaming

**[Polling]**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbLzV7f%2FbtrOdg7IhCx%2FuRHMzCJy0dMfWHaNMbWrs0%2Fimg.png)

브라우저가 새로운 정보를 확인하기 위해 요청을 계속 전송하고 서버는 이에 대해 매번 즉시 응답하는 방식입니다. 클라이언트가 정보를 확인하는 빈도가 빈번하지 않은경우에 적합합니다. 클라이언트가 매번 요청을 보내기 때문에 클라이언트의 수가 많아지면 서버의 부담이 커진다는 단점이 있습니다.

**[Long Polling]**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbn21yA%2FbtrOcf2jnY5%2FYtfwLUvRgRgmSgK8WreH90%2Fimg.png)

Polling 방식과 동일하게 브라우저가 새로운 정보를 확인하기 위해 요청을 계속 전송하지만, 서버는 새로운 정보가 업데이트 될때까지 요청에 대해 응답하지 않는 방식입니다. 데이터 업데이트가 빈번한경우 Polling 방식에 비해 성능 이점이 크지 않습니다. 다수의 클라이언트에 대해 동시에 이벤트가 발생할 경우 서버의 부담이 커진다는 단점이 있습니다.

**[Streaming]**

![img_2.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbadeW2%2FbtrN9qbZuAI%2FyX2PegRZaeQHlrbOIMDWNk%2Fimg.png)

서버가 클라이언트에 응답을 보낼때 연결을 닫지않고 계속해서 응답을 보내는 방식입니다. 응답시 연결을 유지하기 때문에 Polling 방식에 비해 요청이 줄어든다는 장점이 있지만, 일반적인 HTTP통신 방식이 아니기 때문에 범용성이 떨어집니다.

## Reference

- [https://levelup.gitconnected.com/websockets-demystified-part-1-understanding-the-protocol-fccca2ca75eb](https://levelup.gitconnected.com/websockets-demystified-part-1-understanding-the-protocol-fccca2ca75eb)
- [https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers)
- [https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-fallback](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-fallback)
- [https://www.theserverside.com/news/1363576/What-is-the-Asynchronous-Web-and-How-is-it-Revolutionary](https://www.theserverside.com/news/1363576/What-is-the-Asynchronous-Web-and-How-is-it-Revolutionary)
