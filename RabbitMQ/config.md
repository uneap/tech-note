# config

## batch sync

`ha-sync-batch-size`

### 고려 사항

- 평균 메세지 크기
- RabbitMQ 노드 간의 네트워크 처리량
- net_ticktime 값

→ 배치 사이즈 만큼 데이터를 동기화

### 큐 동기화 방식

**새로운 slave queue가 추가되는 경우**

`ha-sync-mode: manual`

- default 모드임.
- 새로운 slave queue는 master queue가 갖고 있던 기존 메세지를 복제하지 않음.
- 생성된 이후 master queue가 받은 새로운 메세지만 복사함 → unsynchronised 상태
- 시간이 지나면서 consume 됨에 따라 synchronised slave queue가 됨

`ha-sync-mode: automatic`

- unsynchronised 상태를 해소하기 위한 대안으로 ha-sync-mode를 설정하면 되지만, 설정을 키게 되면, sync하는 동안 queue는 무응답 상태가 됨.(가용성에 좋지 않음.)

### 동기화되지 않은 슬레이브에 리더 승격 허용 여부 제어

- **always**
    - 승격 됨
    - 대기열의 가용성 유지, 일관성 낮음
- w**hen-synced**
    - 승격 안됨
    - 큐의 리더가 클러스터에서 삭제될 경우, 큐를 삭제하고 다시 만들지 않는 이상, 큐를 사용할 수 없음
    - 따라서 이 경우 모든 큐의 메세지를 잃는 것과 같음
    - 대기열의 일관성 유지(최악일 경우 모든 메세지를 잃을 수 있음), 가용성 낮음

`ha-promote-on-failure`

: 네트워크 커넥션을 잃거나, 클러스터로부터 리더가 삭제되었을 때

- default: always(when-synced로 될 경우, 최악일 경우 모든 메세지를 잃을 수 있으므로 always)

`ha-promote-on-shutdown`

: 노드가 정상적으로 종료될 경우

- default: when-synced

### ha-mode

- **exactly**
    - 복제본 개수를 지정(master + slave)
    - `ha-promote-on-shutdown`: always 일 때, count에 맞춰 클러스터 전체로 큐가 미러링 될 수 있고, 새롭게 미러링 된 slave가 리더로 승격될 수 있으므로 주의해야 함.
        - 근데 이건 클러스터로부터 리더가 삭제되어있을 때도 적용되는 것일 듯.
- **all**
    - 클러스터에 모든 노드에서 미러링 됨
    - 매우 보수 적임
    - 네트워크 I/O, 디스크 I/O 및 디스크 공간 사용 비용을 따졌을 때, 부담있음
- **nodes**
    - 미러링 할 노드 지정
    - 큐가 생성될 때, 지정된 노드 중 온라인 상태가 아닌 노드더라도, 연결된 노드 한정으로 큐가 생성됨.