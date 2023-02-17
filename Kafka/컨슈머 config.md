# 컨슈머 주요 옵션

## 필수 옵션

- bootstrap.servers
    - 프로듀서가 데이터를 전송할 대상 카프카 클러스터에 속한 브로커의 호스트 이름: 포트를 1개 이상 작성해야 함.
    - 2개 이상 브로커 정보를 입력하여 일부 브로커에 이슈가 발생하더라도 접속하는 데 이슈가 없도록 설정 가능
- key.deserializer, value.deserializer
    - 레코드 메세지 키, 값을 역직렬화 하는 클래스를 지정(프로듀서가 직렬화 한 대로 역직렬화)

## 선택 옵션

### Throughput 연관 옵션

![https://i.imgur.com/6spvHQG.jpg](https://i.imgur.com/6spvHQG.jpg)

- `fetch.max.wait.ms`
    - 브로커가 Fetch API 요청을 받았을 때 `fetch.min.bytes` 값만큼 데이터가 없는 경우 응답을 주기까지 최대로 기다릴 시간이다.
    - 기본값은 500ms(0.5초)이다.
- `fetch.min.bytes`
    - Fetch API 요청이 왔을 때 브로커는 최소한 `fetch.min.bytes` 값만큼 데이터를 반환해야 한다.
    - 반환할 만큼 데이터가 충분하지 않다면 브로커는 데이터가 누적되길 기다린다.
    - 기본값은 1이다.
- `fetch.max.bytes`
    - Fetch API 요청에 대해 브로커가 반환해야 하는 최대 데이터 크기이다.
    - 이 값은 절대적으로 적용되는 값은 아니다. 첫 번째 파티션의 첫 번째 메시지가 이 값보다 크다면 컨슈머가 계속 진행될 수 있도록 데이터가 반환된다.
    - 브로커가 허용하는 최대 메시지 크기는 `message.max.bytes`와 `max.message.bytes`를 통해 설정한다.
    - 기본값은 52428800(50MiB)이다.
- `max.partition.fetch.bytes`
    - 브로커가 반환할 파티션당 최대 데이터 크기이다.
    - `fetch.max.bytes`와 동일하게 첫 번째 파티션의 첫 번째 메시지가 이 값보다 크다면 컨슈머가 계속 진행될 수 있도록 데이터가 반환된다.
    - 기본값은 1048576(1MiB)이다.
- max.poll.records
    - 한번의 poll()메서드를 통해 반환되는 레코드 개수를 지정
    - 기본값은 500
    
    > fetch.max.bytes는 절대적으로 설정되는 값이 아니므로, 다음의 정보들을 통해 클라이언트 메모리 사용량을 조절 할 수 있다.
    > 
    
    > min(num brokers * `fetch.max.bytes` , max.partition.fetch.bytes * num_partitions)
    > 
    
    > 또한 max.poll.records까지 조정을 해야 메모리 사용량 조절이 가능하다.
    > 

### 컨슈머 생명 연관 옵션

- max.poll.interval.ms
    - 그룹에 속한 컨슈머들은 리밸런스가 발생 시, 해당 시간 내에 join 요청을 보내야 함.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b8d77cbd-1ceb-40ea-96f8-f5c5b9565ae3/Untitled.png)
    
    - 컨슈머는 메시지를 가져오기위해 브로커에 poll()요청을 보내고, 컨슈머는 가져온 메시지를 처리한 후, 해당 파티션의 offset을 커밋을 반복함.
    - 자동 offset이 아니라서, poll 메서드도 늦게 호출이 되었고, 컨슈머 그룹에서 컨슈머가 제외되어 리밸런스가 일어나게 되었고, offset이 커밋되지 않았으므로, 중복적인 데이터 처리가 일어나게 됨.
    - poll 메서드는 해당 시간 내에 호출되어야 함.
    - 해당 시간 내에 보내지 않은 컨슈머는 컨슈머 그룹에서 제외 됨.
    - 기본 값은 300000(5분)
- session.timeout.ms
    - 해당 시간 내에 heatbeat 요청을 그룹 코디네이터에게 보내지 않으면 그룹 코디네이터는 해당 컨슈머가 죽은 것으로 판단.
    - 즉, 컨슈머가 살아있는것으로 판단하는 시간
    - 이 옵션은 heartbeat 없이 얼마나 오랫동안 컨슈머가 있을 수 있는지를 제어하며 heartbeat.interval.ms와 밀접한 관련이 있어서 일반적으로 두 속성이 함께 수정된다.
    - 기본값은 10000(10초)
- hearbeat.interval.ms
    - 하트 비트를 전송하는 시간 간격
    - 일반적으로 [session.timeout.ms](http://session.timeout.ms) 보다 1/3 정도로 설정함.
    - 기본 값은 3000(3초)
- enable.auto.commit
    - 자동 커밋할 지, 수동 커밋할 지 선택
    - 기본값은 true
    - 자동 커밋할 경우 발생하는 문제 [https://firststep-de.tistory.com/41](https://firststep-de.tistory.com/41)
        1. poll()호출을 통해 record 100개 가져옴
        2. record 100개 중 30개 처리 완료(ex. 데이터 저장완료)
        3. offset 자동커밋 시간 지나기 직전 어떤 이유(topic partition개수 증가 혹은 consumer 개수 증감)로 rebalancing 시작
        4. consumer들이 re-assign됨
        5. consumer는 1)에서 commit된 offset부터 다시 데이터를 polling
        6. 다시가져온 record를 처리 수행(중복발생)
        
        데이터 저장 완료하고 offset을 커밋했더라면 이와 같은 문제가 발생하지 않는다.(그렇지만 poll 관련  config 설정으로 인해 중복처리 발생 가능)
        
- auto.commit.interval.ms
    - 자동 커밋일 경우, 오프셋 커밋 간격을 지정함.
    - 기본 값은 5000(5초)이다.

### offset 연관 옵션

- auto.offset.reset
    
    컨슈머 그룹이 특정 파티션을 읽을 때 저장된 컨슈머 오프셋이 없을 경우(커밋 기록이 없을 경우), 어느 오프셋부터 읽을지 선택하는 옵션, 이미 컨슈머 오프셋이 있다면 이 옵션 값은 무시 됨.
    
    - latest
        - 가장 최근에 넣은 오프셋부터 읽기 시작
    - earliest
        - 가장 offset이 낮은 것부터 읽기 시작
    - none
        - 컨슈머 그룹이 커밋한 기록이 있는지 찾아봄. 만일 커밋 기록이 없을 경우 오류를 반환, 커밋 기록이 있다면 기존 커밋 기록 이후 오프셋부터 읽기 시작. 기본 값은 latest임.
- **ackMode**
    - RECORD: 수신기에 의해 각 레코드가 처리된 후 ofiset을 commit 한다.
    - BATCH : 다음 폴링 전에 직전까지 처리된 메시지 offset을 commit 한다.
    - TIME: AckTime 마다 직전까지 처리된 메시지 offset을 commit 한다.(AckTime 는 밀리세컨드초로 지정하며 기본값은 5초이다)
    - COUNT : AckCount 마다 직전까지 처리된 메시지 offset을 commit 한다.(AckCount 기본값은 1이다)
    - COUNT_TIME : COUNT 또는 TIME 중 하나의 조건이 충족할 경우 메시지 offset을 commit 한다.
    - MANUAL : BATCH 방식과 비슷한데 다른 점은 Ack 처리를 컨슈머에서 직접 해줘야 한다.
    - MANUAL_IMMEDIATE : Ack 처리를 컨슈머에서 직접 해줘야 하고 즉시 offiset이 commit 된다.
