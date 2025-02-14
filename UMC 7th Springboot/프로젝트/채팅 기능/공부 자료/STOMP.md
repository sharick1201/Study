## STOMP란?

- Simple Text Oriented Messaging Protocol
    
- WebSocket 위에서 동작하는 텍스트 기반 메시징 프로토콜
    
- 중계 서버(메세지 브로커)를 통해 클라이언트들 간에 비동기 메세지를 전송할 수 있도록 설계되었다.
    
    - 이를 통해 클라이언트-서버 간의 메세지 교환을 표준화하고, 효율적으로 관리할 수 있다.
- 특징
    
    1. HTTP를 모델로 한 프레임 기반 메세징
        - 메시지를 COMMAND, HEADER, BODY로 구성된 표준화된 프레임 형태로 전송한다.
        - WebSocket이 제공하지 않는 메시지 형식을 정의하여, 메세지 프로토콜을 표준화할 수 있다.
    2. Pub/Sub 모델
        - 메시지 브로커(ex. RabbitMQ, ActiveMQ)를 통해 클라이언트 간에 비동기적으로 메시지를 주고받는다.
        - 클라이언트는 특정 주제(topic)를 구독하고, 발행된 메시지를 수신한다.
    3. 상호 운영성이 좋다.
        - 어떤 STOMP 브로커와도 통신할 수 있어 유연하게 사용 가능하다.
        - Spring은 WebSocket 사용 시, STOMP를 지원한다.

## STOMP 동작 방식

1. Publisher (발행자)
    
    - 메시지를 생성하고 `SEND` 프레임을 사용하여 서버의 특정 목적지로 메시지를 전송
    - 메시지에는 `destination` 헤더가 포함되어 있으며, 이 헤더는 메시지가 전달될 목적지를 지정한다.
    - 서버는 수신한 메시지를 해당 목적지를 구독하고 있는 모든 구독자들에게 전달한다.
2. Subscriber (구독자)
    
    - `SUBSCRIBE` 프레임을 통해 서버의 특정 목적지를 구독
    - 구독 요청을 받은 서버는 해당 구독자를 목적지의 구독 리스트에 추가한다.
    - 그 이후로 해당 목적지로 발행된 모든 메시지는 서버를 통해 구독자에게 `MESSAGE` 프레임으로 전달된다.
3. 메세지 브로커
    

- 발행자와 구독자 사이에서 메시지 중계
- 발행자로부터 받은 메시지를 목적지에 따라 분류하고, 해당 목적지를 구독하고 있는 구독자들에게 전달한다.
- 이를 통해 Pub/Sub 구조를 지원 다수의 클라이언트 간에 메세지 효율적으로 전송

## STOMP Frame 구조

명령, 헤더, 본문으로 구성된다.

```makefile
COMMAND
header1:value1
header2:value2

Body^@
```

- **값 인코딩**:
    
    - 모든 명령과 헤더는 UTF-8로 인코딩
    - 특정 문자(캐리지 리턴, 줄 바꿈, 콜론)는 이스케이프 처리
    - 헤더 절단 금지
- **본문(Body)**:
    
    - SEND, MESSAGE, ERROR 프레임만 본문을 가질 수 있다.
- **표준 헤더**:
    
    - **content-length**: 메시지 본문의 옥텟 수를 나타내며, 본문이 있을 경우 포함되어야 한다.
        
        - 옥텟(Octet)
            - 8비트(1Byte)의 데이터 단위
            - 네트워크 프로토콜에서, 데이터의 크기를 측정할 때 옥텟 단위를 사용하여 전송되는 데이터의 양을 나타낸다.
            - 이미지, 비디오 등 다양한 형식의 바이너리 데이터에서 옥텟 단위로 데이터를 처리한다.
    - **content-type**: 프레임 본문이 있는 경우, 본문 해석을 돕기 위한 MIME 타입을 지정한다. 텍스트 기반 콘텐츠는 UTF-8로 인코딩하는 것이 권장된다.
        
        - MIME(Multipurpose Internet Mail Extensions) 타입
            - 인터넷에서 전송되는 파일의 형식을 정의하는 표준
            - 클라이언트와 서버 간에 데이터가 어떻게 처리되고 표시되어야 하는지를 알려준다.
            - ex.
                - `text/html`: HTML 문서
                - `image/jpeg`: JPEG 이미지
                - `application/json`: JSON 데이터
                - `audio/mpeg`: MPEG 오디오 파일
    - **receipt**: 클라이언트 프레임에 포함하여 서버의 처리를 확인받을 수 있다. 서버가 클라이언트 프레임의 처리를 확인하기 위해 RECEIPT 프레임으로 응답하게 한다.
        
- **반복 헤더 항목**: 동일한 헤더가 여러 번 나타날 경우 첫 번째 값만 사용한다.
    
- **크기 제한**: 서버는 프레임 헤더 수, 헤더 길이, 본문 크기에 대한 최대 제한을 설정할 수 있다.
    
    - 악의적인 클라이언트가 서버의 메모리 할당을 악용하지 않도록, 설정해두는 것이 좋다.

### Frame 예시

1. CONNECT 프레임

클라이언트가 서버에 연결 요청을 보낼 때 사용

```makefile
CONNECT
accept-version:1.2    # STOMP 버전
host:localhost        # 서버의 호스트 이름
login:user            # 클라이언트의 로그인 사용자 이름
auth-token:your_token   # 클라이언트의 인증 토큰
# (passcode:pass 처럼 클라이언트의 비밀번호를 사용할 수도 있지만, 보안 상 인증 토큰 사용을 권장)

^@
```

2. CONNECTED 프레임

서버가 클라이언트의 연결 요청에 응답할 때 사용

```makefile
CONNECTED
session:12345                 # 클라이언트와 서버 간의 연결 세션 ID
server:MyServer               # 서버의 이름 또는 ID
heart-beat:10000,10000        # 클라이언트-서버 간의 하트비트 간격 (ex. 클라이언트와 서버가 서로에게 신호를 보내는 주기)

^@
```

3. SEND 프레임

메시지를 특정 큐에 전송할 때 사용

```makefile
SEND
destination:/queue/myQueue       # 메시지를 전송할 대상 큐 또는 주제
content-type:text/plain          # 메시지 본문의 MIME 타입
content-length:13                # 메시지 본문의 길이

Hello, World!^@
```

4. MESSAGE 프레임

큐에서 소비자에게 전달된 메시지를 나타냄

```makefile
MESSAGE
destination:/queue/myQueue  
message-id:1                     # 메시지의 고유 식별자
content-type:text/plain
content-length:13

Hello, Consumer!^@
```

5. SUBSCRIBE 프레임

클라이언트가 특정 주제를 구독할 때 사용

```makefile
SUBSCRIBE  
id:sub-0                         # 구독자의 고유 식별자
destination:/topic/myTopic
ack:auto                         # 메시지 수신 확인 방식(ex. auto, client, client-individual

^@
```

6. UNSUBSCRIBE 프레임

클라이언트가 특정 주제의 구독을 취소할 때 사용합니다.

```makefile
UNSUBSCRIBE
id:sub-0         # 구독자의 고유 식별자. 이 ID를 통해 어떤 구독을 취소할지 지정

^@
```

7. DISCONNECT 프레임

클라이언트가 서버와의 연결을 종료할 때 사용합니다.

```makefile
DISCONNECT
receipt:1        # 클라이언트가 요청한 연결 종료에 대한 확인을 받을 수 있는 고유 식별자. 서버가 이 ID를 사용하여 클라이언트에게 응답

^@
```

## STOMP를 SpringBoot 채팅 기능에 활용하기

채팅 기능에 사용한다면,

- 연결 관리와 메세지 형식 정의를 간단하게 구현할 수 있다.
- 채팅방 생성, 사용자 인증, 메시지 기록 로딩 등의 기능도 쉽게 구현 가능하도록 지원해준다.

Spring과 함께 사용하는 경우,

- `@Controller`를 활용해 채팅 로직을 효율적으로 관리할 수 있다.
    - WebSocket만 사용하면 Handler를 사용해야 한다.
- WebSocket과 함께 STOMP를 완벽히 지원한다.
- 내부 `SimpleBroker`나 외부 브로커(Redis, RabbitMQ 등)를 통해 Pub/Sub 구조를 구현할 수 있다.

## 참고자료

- STOMP 공식문서
    
    [http://stomp.github.io/](http://stomp.github.io/)
    
- [https://innu3368.tistory.com/213](https://innu3368.tistory.com/213)