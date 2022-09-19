RFC 6455에 정의되어있는 웹소켓 프로토콜은 클라이언트와 서버간 전이중 양방향통신을 제공합니다. HTTP와는 다른 TCP 프로토콜이지만 80번 포트와 443번 포트를 사용하며, 기존의 방화벽 규칙 재사용을 위해 HTTP에서 동작하도록 설계되었습니다.

## 웹소켓 Handshake

![image](https://user-images.githubusercontent.com/28651727/190156650-ae8af73c-8665-4cb1-88d8-d3aed9961da1.png)


웹소켓을 사용하기 위해서는 다음과 같은 핸드쉐이크 과정을 거칩니다.

클라이언트가 다음과 같이 HTTP Upgrade 헤더를 사용하여 HTTP요청을 보냅니다.

```java
GET /spring-websocket-portfolio/portfolio HTTP/1.1
Host: localhost:8080
Upgrade: websocket 
Connection: Upgrade 
Sec-WebSocket-Key: Uc9l9TMkWGbHFD2qnFHltg==
Sec-WebSocket-Protocol: v10.stomp, v11.stomp
Sec-WebSocket-Version: 13
Origin: http://localhost:8080
```

요청에 대한 응답으로 웹소켓 서버는 101 상태코드를 응답하고 TCP소켓은 클라이언트와 서버간 메시지 송수신을 위해 열린상태로 남아있게 됩니다.

```java
HTTP/1.1 101 Switching Protocols 
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: 1qVdfYHU9hPOl4JYYNXF623Gzn0=
Sec-WebSocket-Protocol: v10.stomp
```

## HTTP vs Websocket

URL과 메서드에따라 적절한 핸들러로 라우팅하는 HTTP방식과 달리 웹소켓의 경우 최초 커넥션을 위한 한개의 URL만을 가집니다. 이러한 단순한 통신구조로 인해 메시지를 원하는 핸들러로 라우팅 하기 위해서는 추가적인 코드 구현이 필요합니다. 이러한 작업을 편리하게 하기 위해 STOMP와 같은 프로토콜을 사용할 수 있습니다.

## STOMP

STOMP는 Websocket상위에서 동작하는 프로토콜로 메시지의 종류, 형식, 내용을 정의하여 메시지 효과적으로 처리할 수 있게 합니다. 

STOMP는 프레임 기반의 프로토콜로 다음과 같은 형태의 STOMP 프레임을 가집니다.

```
COMMAND
header1:value1
header2:value2

Body^@
```