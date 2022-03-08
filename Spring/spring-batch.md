# batch

## 배치 어플리케이션이란?

배치는 큰 데이터를 한번에 저장하거나 처리하는 프로젝트.

Job은 여러개의 Step으로 구성됨. Step은 Tasklet으로 구성됨.

### Tasklet을 사용한 Task 기반 처리

- 배치 처리 과정이 비교적 쉬운 경우 쉽게 사용
- 하나의 큰 덩어리를 여러 덩어리로 나누어 처리하기 부적합
- 대량 처리를 하는 경우 Chunk 보다 더 복잡함.

### Chunk를 사용한 chunk 기반 처리

- ItemReader, ItemProcessor, ItemWriter 관계 이해 필요
- 대량 처리를 하는 경우 Tasklet보다 비교적 쉽게 구현
- 한 번에 모든 행을 읽고 처리하고 쓰는 대신 한 번에 고정 된 양의 레코드 (청크)를 읽고 처리하고 씀.

## Job Parameter란?

Job의 구현체를 구분하는 방법은 Job + Job Parameter이기 때문에, Parameter없이는 같은 job을 계속 실행하면 안된다.

Spring Batch는 일반적으로 동일 Job Parameter로 실행시 어떻게 처리할 것인지 여러가지 옵션을 제공함.

- 해당 파라미터로 최근 실패한 이력이 있다면 이어서 실행할 것인지
- 해당 파라미터로 최근 실패 혹은 성공한 이력이 있다면 실행하지 않을 것인지
- 해당 파라미터로 최근 실행한 이력이 있어도 무시하고 다시 실행할 것인지

재시작 가능한 job이라면 첫 실행에서 성공했는 지와는 상관없이 항상 실행해야하는 Step도 있을 수 있다. 따라서 allowStartIfComplete가 true일 경우 step은 항상 실행된다.
