# rabbitMQ

rabbitMQ는 AMQP를 따르는 오픈소스 메세지 브로커이다.

## AMQP(Advanced Message Queing Protocol)

- AMQP의 라우팅 모델은 3개의 요소들로 구성된다.
- AMQP란 3가지의 요소들이 서로 어떻게 통신하는지 정의한 프로토콜이라 볼 수 있다.

### 용어 정리

Queue: Producer가 전송한 메세지가 컨슘 되기 전까지 보관되는 장소이다.

Exchange: Producer 로부터 받은 메세지를 어떤 Queue에 전송할지 결정하는 객체, **라우터** 개념.

Binding: exchange와 큐와의 관계를 정의한 일종의 라우팅 테이블 개념.

> Headers의 경우, 모든 header가 일치할 때, 하나라도 일치할 때 메세지 전송 등의 옵션을 줄 수 있다고 함.
> 

Broker

Producers(publisher) -> [Exchange -- Binding --> Queue] -> Consumers

Producer 에서 Broker 의 Exchange 로 메시지를 전달하면,

**Binding 규칙에 의해** 연결된 Queue 로 메시지가 저장된다.

메세지는 consumer가 소비할 때까지 Queue에 대기하게 된다.

Consumer 에서는 브로커의 Queue를 통해 메시지를 받아가서 처리한다.


### 수신 확인 모델

- 컨슈머는 메시지를 받으면 브로커에게 알리고, 브로커는 ack를 받았을 때만 큐에서 메시지를 삭제
- Broker가 메시지를 전달하면 자동으로 삭제

### Message 보존

RabbitMQ Server가 재기동할 경우, Queue는 모두 제거되는데, 이를 막기 위해서는, Durable 옵션에 true를 주고 생성해야 하며, Producer가 메세지를 발송할 때, PERSISTENT_TEXT_PLAIN 옵션을 줘야 메세지가 보존됨.

### Pre-Fetch

프리페치 값은 **동시에 전송되는 메세지 수를 지정**하는데 사용함.

default 값은 무제한임.

기본적으로 rabbitMQ는 메세지를 수락할 준비가 된 것으로 보이는 consumer에게 최대한 많은 메세지를 보냄.

이 값을 제한하면, 브로커가 승인을 기다리기 전에 수신된 메세지 수가 제한됨.

![https://blog.kakaocdn.net/dn/cA0BXj/btrClmAcNF8/lgybvL6wSLliEPoWFT9rlk/img.png](https://blog.kakaocdn.net/dn/cA0BXj/btrClmAcNF8/lgybvL6wSLliEPoWFT9rlk/img.png)

pre-fetch 수가 많을 경우 Queue에서 많은 메세지를 가져와 한 consumer에게 모두 전달해, 다른 consumer를 유휴 상태로 유지할 수 있음.

![https://blog.kakaocdn.net/dn/1ygeH/btrCokPd5Sd/uBWZsMM6K55bXYBBtHkEwk/img.png](https://blog.kakaocdn.net/dn/1ygeH/btrCokPd5Sd/uBWZsMM6K55bXYBBtHkEwk/img.png)

따라서, consumer가 많거나 처리 시간이 긴 경우 메세지가 모든 consumer에게 고르게 전달될 수 있도록 제한을 거는 게 좋음.

동일한 처리 시간이 있고 네트워크 동작이 동일하게 유지되는 경우,

왕복 시간을 계산하고 각 메세지에 대한 클라이언트의 처리 시간으로 나누어 예상 프리페치 값을 얻을 수 있음.

값이 너무 낮으면 consumer에 메시지가 도착할 때까지 기다려야 하기 때문에 많은 유휴 상태를 유지함.

값이 너무 높으면 한 consumer 바쁘게 유지되는 반면 다른 consumer 유휴 상태로 유지될 수 있음.
