**Connection - Application과 RabbitMQ Broker사이의 TCP 연결**

**Channel - connection내부에 정의된 가상의 연결. queue에서 데이터를 손볼 때 생기는 일종의 통로같은 개념**

connection은 TCP 연결인데, Channel 이 없을 경우 TCP 연결을 무수히 많게 연결해야할 것이다. 그러나 그렇게 되면 성능이 급속도로 낮아질 것이다.

Channel은 가상 연결로써 오버헤드를 낮출 수 있다.

프로듀스와 컨슘을 같이 할 수 있게끔 하는 connection 내부에 정의된 가상의 연결.

![https://faun.pub/rabbitmq-connection-vs-channel-aed1188a7f3a](https://faun.pub/rabbitmq-connection-vs-channel-aed1188a7f3a)

채널이 병렬 작업을 수행하는 데 도움이 되는 방법. 

아래 그림과 같이 하나의 TCP 연결이 있는 RabbitMQ 인스턴스를 통해 A서버와 B서버가 서로 대화하기를 원한다고 가정

 예를 들어 A서버는 컨슘 대기 중이고 B서버가 큐에 메시지를 전송하면 처리를 시작한다고 가정

 만약 A서버가 컨슘 대기 전에 rabbitMQ에 메세지를 전송하고자 한다고 가정하면,

 해당 채널에서 수행된 이전 트랜잭션에 대한 처리가 완료될 때까지 기다려야 함.

![https://miro.medium.com/max/1262/1*E97dIEkmAjuY2zu9jLmCsA.jpeg](https://miro.medium.com/max/1262/1*E97dIEkmAjuY2zu9jLmCsA.jpeg)

아래 그림과 같이 채널(개수 제한 없음)을 생성하여 실행 속도를 향상시킬 수 있습니다. 

따라서 A서버는 큐에서 메시지를 컨슘하는 동시에 다른 채널에서 메시지를 전송할 수도 있다.

![https://miro.medium.com/max/1280/1*f0F8G8EBh6YOk6v5v_25Ew.jpeg](https://miro.medium.com/max/1280/1*f0F8G8EBh6YOk6v5v_25Ew.jpeg)

이것이 RabbitMQ에서 연결과 채널이 관련되는 방식입니다. 연결과 채널 간의 차이점에 대한 생각을 제공하기를 바랍니다.

- 클라이언트가 수행하는 모든 프로토콜 작업은 채널에서 발생한다.
- 채널 안에 연결할 Queue를 선언할 수 있으며, 채널 하나당 하나의 Queue만 선언이 가능하다.
