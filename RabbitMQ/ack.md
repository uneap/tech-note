# ack
## Producer Confirm

Producer가 RabbitMQ에게 Message를 전송한 후 RabbitMQ로부터 ACK를 받는 기법

Producer는 설정에 따라 RabbitMQ의 ACK를 기다리지 않을 수도 있음.

1. Producer는 RabbitMQ에게 메세지 전송
2. RabbitMQ는 수신한 메세지를 Exchange에게 전달
3. Exchange는 Exchange에 설정된 규칙에 따라 수신한 메세지를 버리거나, Queue 혹은 다른 Exchange에게 전달한다.
4.  Queue가 Mirroring되어 있을 경우
    1. 메세지는 미러링된 모든 큐에 복사된 이후 producer에게 ACK를 보낸다.
5. 그렇지 않은 경우
    1. 메세지가 버려진다면 RabbitMQ는 Producer에게 바로 ACK를 전송한다.
    2. 메세지가 큐로 전송되면 큐가 메세지를 저장한 이후에 Producer에게 ACK를 보낸다.

> rabbitMQ는 정상적으로 메세지를 수신했으나, Network 장애로 인해 ACK가 Producer에게 전송되지 못할 경우
> 
> 
> 연결이 재생성 된 이후 ACK를 받지 못한 메세지를 재전송함.
> 
> 따라서 RabbitMQ는 중복한 메세지를 수신할 수 있음.
> 

## Consumer Acknowledgement

RabbitMQ가 Consumer에게 메세지를 전송한 후 Consumer로부터 ACK를 받는 기법

RabbitMQ는 Consumer의 설정에 따라서 Consumer의 ACK를 기다리지 않을 수도 있음.

또한 ACK의 타임 아웃은 존재하지 않음.

1. RabbitMQ가 Consumer에게 메세지 전송
2. Consumer는 수신한 메세지 처리 후 RabbitMQ에게 ACK 전송
3. Consumer가 메세지를 정상적으로 수신하였어도 Consumer에 의해 메세지는 reject 혹은 nack될 수 있음.

reject: 하나의 message에 대해서만 거절하는 응답.

nack: ack를 전송하지 않은 모든 message에 대해 거절하는 응답.

> RabbitMQ는 Message를 전송한 Consumer로부터 ACK를 받지 못한 상태에서
> 
> 
> Consumer와의 연결이 끊어지게 되면, Consumer가 Message를 제대로 처리하지 않는것으로 간주함.
> 
> Consumer와의 연결이 재생성된 이후에 Consumer에게 Message를 재전송한다.
> 

### reject/nack - dead letter or queue

거절된 Message는 Reject/nack 응답과 함께 전송된 Requeue 옵션에 따라 처리가 달라진다.

1. Requeue = true 일 때
    1.  원래 있던 Queue로 돌아간 후 다시 Consumer로 재전송된다.
2. false일 때
    1. dead letter exchange 옵션이 설정되어있을 경우 dead letter로 전송되고, 그렇지 않을 경우 message는 버려진다.

### dead letter

메세지를 처리하다 문제가 생길 경우 다음 메세지를 처리하기 위해 메세지를 건너뛰어야 하는 경우가 있다.

일시적으로 다른 API 호출이나 연결 문제가 있는 메세지는 나중에 재시도 하면 정상처리될 수 있으므로 잠시 다른 곳에 보관한다.

그 다른 곳이 **dead letter**이다.

### dead letter로 처리되는 경우

- requeue = false 로 basic.reject 혹은 basic.nack 처리
- queue에서 메세지 TTL이 다 됨
- queue가 가득차서 넘침 (x-max-length)

dead letter 옵션이 설정된 queue를 선언 후 nack이나 reject을 보내면, 

x-dead-letter-exchange로 설정된 exchange에 보내지는 것을 확인할 수 있음.

```java
Map props1 = new HashMap();
props1.put("x-dead-letter-exchange", "x1");
channel.queueDeclare("q1", false, false, false, props1);
```

```java
DefaultConsumer consumer = new DefaultConsumer(channel) {
  @Override
  public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
    String message = new String(body, "UTF-8");
    System.out.println(" [x] Received '" + message + "'");

// requeue=false
    channel.basicNack(envelope.getDeliveryTag(), true, false);
// channel.basicReject(envelope.getDeliveryTag(), false);
    System.out.println(" Message is rejected: " + message);
  }
};
```