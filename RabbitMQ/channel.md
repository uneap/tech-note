# channel

**Connection - Application과 RabbitMQ Broker사이의 TCP 연결**

**Channel - connection내부에 정의된 가상의 연결. queue에서 데이터를 손볼 때 생기는 일종의 통로같은 개념**

connection은 TCP 연결인데, Channel 이 없을 경우 TCP 연결을 무수히 많게 연결해야할 것이다. 그러나 그렇게 되면 성능이 급속도로 낮아질 것이다.

Channel은 가상 연결로써 오버헤드를 낮출 수 있다.

[https://faun.pub/rabbitmq-connection-vs-channel-aed1188a7f3a](https://faun.pub/rabbitmq-connection-vs-channel-aed1188a7f3a)

채널이 병렬 작업을 수행하는 데 도움이 되는 방법. 

아래 그림과 같이 하나의 TCP 연결이 있는 RabbitMQ 인스턴스를 통해 두 개의 앱 'A'와 'B'가 서로 대화하기를 원한다고 가정합니다.

 예를 들어 앱 'A'가 대기열에서 수신 대기 중이고 앱 'B'가 메시지를 수신하면 처리를 시작한다고 가정해 보겠습니다.

 만약 'A' 앱이 현재 동작을 승인하기 전에 'B' 앱에 메시지를 보내려고 한다면 어떻게 될까요? 

안 됩니다. 해당 채널에서 수행된 이전 트랜잭션에 대한 처리가 완료될 때까지 기다려야 합니다.

![https://miro.medium.com/max/1262/1*E97dIEkmAjuY2zu9jLmCsA.jpeg](https://miro.medium.com/max/1262/1*E97dIEkmAjuY2zu9jLmCsA.jpeg)

아래 그림과 같이 다른 채널(개수 제한 없음)을 생성하여 실행 속도를 향상시킬 수 있습니다. 

따라서 앱 'A'는 대기열에서 메시지를 소비하는 동시에 다른 채널에서 메시지를 게시할 수도 있습니다!

![https://miro.medium.com/max/1280/1*f0F8G8EBh6YOk6v5v_25Ew.jpeg](https://miro.medium.com/max/1280/1*f0F8G8EBh6YOk6v5v_25Ew.jpeg)

이것이 RabbitMQ에서 연결과 채널이 관련되는 방식입니다. 연결과 채널 간의 차이점에 대한 생각을 제공하기를 바랍니다.