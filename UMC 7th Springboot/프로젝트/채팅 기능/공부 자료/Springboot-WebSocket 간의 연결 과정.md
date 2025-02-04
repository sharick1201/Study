
   Spring Boot는 실시간 채팅을 위한 WebSocket 요청을 처리하기 위해 다음 두 가지 주요 구성 요소를 요구한다.
 - WebSocketConfig:
	 - WebSocket 요청의 진입점을 설정하고 관리하기 위해 필요하다.
	 - WebSocket 핸들러를 등록하고 URL 매핑 및 CORS 정책 등을 설정한다.
 - WebSocketHandler:
	 - 실질적으로 메시지를 처리하는 역할
	 - 클라이언트와 서버 간 메시지를 처리하는 로직을 정의한다.

### 왜 WebSocketConfig와 WebSocketHandler가 필요한가?

#### 1. WebSocketConfig
   - 핸들러 등록 및 URL 매핑
     - `WebSocketConfig`는 `WebSocketConfigurer` 인터페이스를 구현하여, 특정 URL로 들어오는 요청을 처리할 핸들러를 등록한다.
     - ex. `registry.addHandler(webSocketHandler, "/ws/chat")`는 `/ws/chat` 경로로 들어오는 WebSocket 요청을 `webSocketHandler`로 처리하도록 지정한다.
   - CORS 설정
     - WebSocket은 CORS 정책을 우회하거나 설정해야 한다. 
     - `setAllowedOrigins("*")` 등을 통해 허용 도메인을 지정한다.

#### 2. WebSocketHandler
   - 메시지 처리
     - `TextWebSocketHandler` 또는 `BinaryWebSocketHandler`를 상속받아 클라이언트에서 전송된 메시지를 수신하고 응답하는 로직을 작성한다.
     - ex. `handleTextMessage(WebSocketSession session, TextMessage message)` 메서드를 오버라이드하여 메시지를 처리한다.
   - 연결 관리
     - `afterConnectionEstablished`와 `afterConnectionClosed` 메서드를 통해 클라이언트 세션을 관리하며, 연결이 열리거나 닫힐 때의 동작을 정의할 수 있다.


### Spring Boot-WebSocket 로직

1. 클라이언트가 지정된 URL(`ws://...`)로 연결 요청을 보낸다.
2. Spring Boot는 `WebSocketConfig`에 정의된 URL 매핑을 확인하고 해당 요청을 등록된 핸들러(`WebSocketHandler`)로 전달한다.
3. 핸들러는 메시지 송수신 및 연결 관리를 수행하며, 필요 시 다른 클라이언트에게 메시지를 브로드캐스팅하거나 특정 로직을 실행한다.





#### 참고 자료
* spring 공식 문서: org.springframework.web.socket
	https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/socket/package-summary.html

* https://yjkim-dev.tistory.com/65?t