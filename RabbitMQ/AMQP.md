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

RabbitMQ Server가 재기동할 경우, Queue는 모두 제거되는데, 이를 막기 위해서 두가지 처리가 필요

1. Durable 옵션에 true를 주고 큐를 생성
2. Producer가 메세지를 발송할 때, `PERSISTENT_TEXT_PLAIN` 옵션을 줘야 메세지가 보존됨.

### Pre-Fetch

프리페치 값은 **동시에 전송되는 메세지 수를 지정**하는데 사용함.

일단, consumer 1,2만 있는 상태에서, prefetch가 250이고, 프로듀서가 rabbitMQ에 500개를 전송했다고 가정하면,

1. consumer1,2는 각자 250씩 가져 간다.
2. 처리 속도를 높이기 위해 consumer하나를 추가했다고 가정하자.
3. 컨슈머 3번은 가져갈 메세지가 없다. 따라서 유휴한 상태로 남게 된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f34ef98f-e857-4e07-b048-053c8166fab4/Untitled.png)

따라서, consumer가 많거나 처리 시간이 긴 경우 메세지가 모든 consumer에게 고르게 전달될 수 있도록 제한을 거는 게 좋음.

- 그리고 처리 시간이 매우 느릴 경우, 프리페치 값을 1로 설정해야 함. 미리 컨슈머에게 데이터를 줄 이유가 없으므로.
- 처리되기 원하는 시간을 기준으로, min (평균적으로 보내는 양/consumer 개수, 컨슈머 클라이언트가 처리하는 양)

클라이언트 처리 시간 대비 값이 너무 낮으면, consumer에 메시지가 도착할 때까지 기다려야 하기 때문에 많은 유휴 상태를 유지함.

값이 너무 높으면 한 consumer 바쁘게 유지되는 반면 다른 consumer 유휴 상태로 유지될 수 있음.
