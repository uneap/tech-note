- 배치 애플리케이션이란?
    
    단발성으로 대용량의 데이터를 처리하는 어플리케이션
    
    - 대용량 데이터 - 대량의 데이터를 가져오거나, 전달하거나, 계산하는 등의 처리
    - 자동화 - 심각한 문제 해결을 제외하고는 사용자 개입 없이 실행
    - 견고성 -  **잘못된 데이터를 필터링하거나 어떤 작업을 통해** **충돌/중단 없이 처리**
    - 신뢰성 - 무엇이 잘못되었는지를 로깅, 알림 통해 **추적** 가능
    - 성능 - **지정한 시간 안에 처리를 완료 &** 동시에 실행되는 다른 애플리케이션을 영향을 주지 않도록 **수행**
    - 실패 복구 — 만약 처리 중에 실패가 발생하면, **실패 지점부터 재시작** 할 수 있는 기능을 제공
- job, step, tasklet
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/33ee8fb8-7750-4080-9dcd-a97559596c7c/313f4627-17e6-4ac2-a6e2-5aa1714eff05/Untitled.png)
    
    - Job
        - 하나의 일을 말합니다.
    - Step
        - 하나의 일(Job) 안에서 단계를 의미합니다.
    - Tasklet
        - 하나의 단계(Step) 안에서 실질적으로 수행하는 작업 내용
- spring batch 구성도
    
    ### **Spring Batch 구성도**
    
    !https://blog.kakaocdn.net/dn/uIsqd/btrPJ3T3mmN/kFaARH0ZRBkj4UXkzlSXv1/img.png
    
    - **JobLauncher** : Job을 실행시키는 컴포넌트
    - **JobRepository** : Job의 실행 및 Job, Step 등을 저장
    - **Job** : Batch Processing
    - **Step** : Batch Job의 여러 단계 (Job과 1 : N)
    - **Item-Reader, Processor, Writer** : 데이터를 읽고, 처리하고, 쓰는 구성 (Step과 1:1)
- @EnableBatchProcessing 동작 순서
    - SimpleBatchConfiguration
        - JobBuilderFactory와 StepBuilderFactory 생성
        - 스프링 배치의 주요 구성 요소 생성(프록시 객체로 생성됨)하는 설정 클래스
    - BatchConfigurerConfiguration
        - BasicBatchConfigurer
            - SimpleBatchConfiguration에서 생성한 프록시 객체의 실제 target 객체를 생성하는 설정 클래스
            - 빈으로 의존성 주입 받아서 주요 객체들을 참조해서 사용할 수 있습니다.
        - JpaBatchConfigurer
            - BasicBatchConfigurer를 상속받아 JPA 관련 객체를 **생성**하는 **설정** 클래스
        - 위 두개는 BatchConfigurer 인터페이스를 구현했습니다.
    - BatchAutoConfiguration
        - **실제 애플리케이션의 Job 실행과 관련된 Launcher를 생성하는 설정 클래스**
- job
    - 하나의 배치작업 자체를 의미
    - 배치 작업을 어떻게 구성하고 실행할 것인지 전체적으로 설정하고 명세해 놓은 객체
    - • **여러 Step들을 포함**하는 컨테이너로 **반드시 한 개 이상의 Step으로 구성**해야 함
    
    **SimpleJob**
    
    !https://blog.kakaocdn.net/dn/sA2mz/btrP5q1J4yE/SnKoQkAgCHYnknE0QEizS1/img.png
    
    - 순차적으로 Step을 실행시키는 Job
    - Job에서 사용할 수 있는 표준 기능을 포함
    
    **FlowJob**
    
    !https://blog.kakaocdn.net/dn/dxl44Y/btrP191H1AA/plponp6UUX0DKI1GgKhix0/img.png
    
    - 특정 조건에 따라 Step을 구성하여 실행하는 Job
    - Flow 객체를 실행시켜 작업을 진행
- job instance?
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/33ee8fb8-7750-4080-9dcd-a97559596c7c/2a1c0faf-cd1d-4bb4-8784-25496674a63e/Untitled.png)
    
    - job이 실행되는 시점에서 생성되는 **Job의 논리적 작업 실행 개념**
    - Job이 실행되는 시점에 처리하는 내용은 각각 다르기 때문에 **Job의 실행을 구분**해야 함
        - 예를 들어, 하루에 한 번 매일 Job이 실행된다면, 실행되는 각각의 Job을 JobInstance라고 함
    - **Meta Data Schema**의 **BATCH_JOB_INSTANCE** 테이블과 매핑
    
    !https://blog.kakaocdn.net/dn/ep9OhM/btrP3ms3bae/YY4tFY2fZt5kT3lip44wy0/img.png
    

---

- job parameter
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/33ee8fb8-7750-4080-9dcd-a97559596c7c/a3b7d59a-b52e-4fd9-ade5-6fdc2577fd98/Untitled.png)
    
    - Spring Batch의 **Job을 실행할 때 외부에서 받는 파라미터**를 가진 도매인 객체
    - 하나의 Job에 존재할 수 있는 여러개의 JobInstance를 구분하기 위한 용도
    - JobInstance와 JobParameters는 1:1 관계
    - **Meta Data Schema**의 **BATCH_JOB_EXECUTION_PARAM** 테이블과 매핑
    
    멱등성: 연산을 여러번 적용하더라도 결과가 달라지지 않는 성질
    
    만일 어제 배치가 제대로 작동하지 않아서, 어제 날짜로 배치를 실행해야하는 경우에 적용시키기 위해 
    
    코드 내부에 LocalDateTime.now()로 실행시키기 보다는, 멱등성을 유지하기 위해 job parameter를 사용하여 배치를 실행시켜야 한다.
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/33ee8fb8-7750-4080-9dcd-a97559596c7c/2dfff6a1-379c-4fa0-8007-7dca1237fadc/Untitled.png)
    
- 동일 job parameter로 계속 실행이 되는 방법
    
    특정 데이터에 대한 검증 로직, 혹은 데이터 갱신 배치의 경우 같은 잡 파라미터로 계속 실행될 수 있길 원할 수 있음.
    
    incrementer 옵션에 JobParametersIncrementer를 구현하여 커스텀 설정함.
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/33ee8fb8-7750-4080-9dcd-a97559596c7c/b5e03161-63de-404b-80eb-1626e7b1139d/Untitled.png)
    
    job 수행 시 run.id가 추가되고 동일한 job을 수행할 때마다 LocalDateTime.now()가 찍힌 것을 확인할 수 있다.(같은 잡 파라미터라도 status에 상관없이 실행이 됨)
    
- job scope vs step scope
    - 공통점
        - bean 생성 시점을 지정된 scope가 실행되는 시점으로 지연시킴.
        - **JobParameter의 Late Binding**이 가능
            - jobParameter를 StepContext 나 JobExecutionContext레벨에서 할당시킬 수 있음
            - 혹은 controller나 service와 같은 비지니스 로직 처리 단계에서 jobParameter를 할당시킬 수 있음.
        - 동일한 컴포넌트를 병렬로 사용할 때 유용
            - Step 내의 tasklet에서 멤버 변수를 수정하는 코드가 있다고 생각해본다면,
            - StepScope로 선언되었을 경우, step이 실행되는 시점에 별도로 tasklet을 생성하고 관리하므로 서로의 상태를 침범할 일이 없다.
            - 그러나 선언되지 않으면, 별개의 step은 같은 tasklet에 접근해서 서로의 상태를 침범할 수 있다.
    - 차이점
        - job scope
            - Step 선언문에서 사용 가능
            - job 실행 시점에 Bean이 생성.
        - step scope
            - Tasklet, ItemReader, ItemWriter, ItemProcessor에서 사용 가능
            - Step 실행 시점에 해당 컴포넌트를 Spring Bean으로 생성
- scope 사용시 주의할 점
    - 스코프 어노테이션은 `proxyMode = ScopedProxyMode.TARGET_CLASS`로 설정
    - 실제 타겟 클래스를 리턴으로 설정해야 함.
        - 해당 proxy 모드는 클래스에 대한 가짜 프록시 객체를 내부에서 생성한다.
        - 요청이 오면 가짜 프록시 객체를 거쳐 타깃 클래스로 요청이 들어오기 때문에 반드시 인터페이스가 아닌 실제 타깃 클래스를 리턴으로 설정해야 함.
- spring batch 5 되면서 바뀐 잡 설정
    - spring boot 3환경을 필요로 하며, 최소 jdk 17을 필요로 함.
    - JobBuilderFactory, StepBuilderFactory deprecated
        - 팩토리는 Job, Step 구성 뿐 아니라 JobRepository를 생성하고 설정하는 기능도 포함.
        - 개발자가 직접 Job, Step 선언 뿐만 아니라, JobRepository 또한 명시적으로 제공 필요
        - step은 transaction manager를 명시적으로 제공해야 함.
    - @EnableBatchProcessing 선언할 필수가 아님.
    - 설정 커스터 마이징은 DefaultBatchConfiguration을 상속받아 할 수 있다.(필수 아님.)
        - 필요하다면 getTransactionManager(), getDataSource(), getCharset()등을 오버라이딩해서 사용 가능
        - 그러나 @EnableBatchProcessing과 동시에 사용하면 안됨.

---

- job execution
    - **Job에 대한 한 번의 실행**을 의미하여, **Job 실행 중 발생한 정보들을 저장**하고 있는 객체
        - 시작 시각, 종료 시각, 상태, 종료 상태 등의 정보
    - **BATCH_JOB_EXECUTION** 테이블과 매핑
    - **JobInstance와 JobExecution는 1 : N**의 관계
        - JobExecution의 실행 상태 결과가 **'COMPLETED' -> Job 재 실행 불가능**
        - JobExecution의 실행 상태 결과가 **'FAILED' -> Job 재 실행 가능**
        - JobExecution의 실행 상태 결과가 'COMPLETED'가 될 때까지 하나의 JobInstance로 여러 번 실행 가능
        1. JobLauncher가 Batch Job을 실행(Job & JobParameters)
        2. JobRepository가 Job & JobParameters로 DB 조회
        3. JobInstance가 존재할 경우 기존 JobInstance를 리턴, 존재하지 않을 경우 새로운 JobInstance를 리턴
        4. JobInstance가 생성되면 JobExecution도 새로 생성됨
        5. 이미 존재하는 JobInstance에서 BatchStatus의 조건에 따라 Job의 재 실행 가능 여부 판단 
        6. job 시도 결과가 성공 완료면 job instance 실행 불가, exception 발생
        7. job 시도 결과가 실패일 경우 job instance 재 실행 가능, job execution 새로 생성
- execution context
    
    !https://blog.kakaocdn.net/dn/markw/btrQsGpK16a/AOGh9hQgfvhGNIgn0WyROK/img.png
    
    BATCH_JOB_EXECUTION
    
    !https://blog.kakaocdn.net/dn/JYkli/btrQp31iV7K/kP9zwYbSDL4YygmsjNWyOK/img.png
    
    BATCH_JOB_EXECUTION_CONTEXT
    
    !https://blog.kakaocdn.net/dn/uZp25/btrQqkaEoSf/5S7dfuRHvzVH04WUmCNk60/img.png
    
    BATCH_STEP_EXECUTION
    
    !https://blog.kakaocdn.net/dn/boUQ5d/btrQq46hG7R/KnxYYMVC9ZX8kS3xQio9Y1/img.png
    
    BATCH_STEP_EXECUTION_CONTEXT
    
    - Spring Batch 에서 지원하는 **key/value 형식 공유 객체**로 Job을 실행하면서 필요한 데이터(시작 시각, 종료 시각, 상태, 종료 상태 등의 정보)를 **저장하는 공간**
    - 공유 범위
        - Job 범위 - 각 Job의 JobExecution에 저장되며 **서로 다른 Job 간에는 공유가 안되지만 동일한 Job 내의 서로 다른 Step 사이에서는 데이터 공유가 가능**
        - Step 범위 - 각 Step의 StepExecution에 저장되며 **Step 사이에서는 데이터 공유 불가능**
- job repository와 메타테이블
    - **인터페이스**로 배치 작업 중 모든 meta data를 저장하는 **저장소 역할**
        - Job, Step 구현을 위한 CRUD 작업
            - jobRepository에 존재하는 테이블
                
                `BATCH_JOB_INSTANCE`
                
                `BATCH_JOB_EXECUTION`
                
                `BATCH_JOB_EXECUTION_PARAMS`
                
                `BATCH_JOB_EXECUTION_CONTEXT`
                
                `BATCH_STEP_EXECUTION`
                
                `BATCH_STEP_EXECUTION_CONTEXT`
                
            - Job & JobParameters로 데이터베이스를 조회한 후 JobInstance의 여부에 따라 새로 생성하거나 기존에 존재하던 객체를 반환을 가능하게 해줌.
            - `JobExcution`을 생성하며, `Step`의 실행정보를 담고 있는 `StepExecution`도 저장소에 저장해 **전체 메타데이터를 저장/관리하는 역할을 수행**
    - JobRepository 설정은 Spring Batch 설정 어노테이션인 **[@EnableBatchProcessing](https://docs.spring.io/spring-batch/docs/current/api/org/springframework/batch/core/configuration/annotation/EnableBatchProcessing.html)**를 선언하면 JobBuilderFactory와 StepBuilderFactory에 의해 **자동으로 JobRepository Bean이 생성**
    - 구현체는 **[SimpleJobRepository](https://docs.spring.io/spring-batch/docs/current/api/org/springframework/batch/core/repository/support/SimpleJobRepository.html)**

---

- step
    - Step은 독립적이고 순차적인 Batch Job을 **캡슐화**하는 도메인 객체
    - Step에는 **Batch 처리를 수행하는데 필요한 모든 정보**들을 포함하고 있음
    - **모든 Job은 하나 이상의 Step으로 구성**됨
    - 구현체
        - **TaskletStep** : 가장 기본이 되는 클래스로 Tasklet 타입의 **구현체**들을 **제어**
        - **PartitionStep** : 멀티 스레드 방식으로 Step을 여러 개로 분리해서 실행
        - **JobStep** : Step 내에서 Job을 실행하도록 함
        - **FlowStep** : Step 내에서 Flow를 실행하도록 함
- step execution
    - StepExecution은 **Step의 실행을** 의미하는 객체로 **Step 실행에 대한 정보를 저장**함
        - commit, read count, write count, rollback count 등이 기록됨.
        - **BATCH_STEP_EXECUTION** 테이블과 매핑
    - JobExecution와 유사하게 **Step이 실행될 때마다 새로운 StepExecution이 생성**됨
    - 이전 단계의 Step이 실패해 현재 Step을 실행하지 않았다면, StepExecution을 생성하지 않음
    - JobExecution와의 관계
        - Step의 StepExecution이 모두 정상적으로 완료되어야 JobExecution이 정상적으로 완료됨
        - JobExecution와 StepExecution은 1 : N의 관계
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/33ee8fb8-7750-4080-9dcd-a97559596c7c/0f45020e-fed6-4ce8-8c5a-5a3691559e1d/Untitled.png)
    
- item processor
    - ItemProcessor는 데이터를 가공하거나 필터링하는 역할, 필수가 아님
    - writer에서 작업을 수행해도 되겠지만, 비지니스 코드가 섞이는 것을 방지하고자 ItemProcessor를 사용.
    - 2가지 목적을 가짐.
        - 변환
            - reader에서 읽은 데이터를 원하는 타입으로 **변환**하여 writer에 넘겨줌
        - 필터
            - reader에서 넘겨준 데이터를 writer로 넘겨줄지 결정
            - null 반환하면 넘겨주지 않음.
    - 트랜잭션 범위는 chunk 단위임.
- item reader
    - 데이터를 읽어오는 역할을 함.
    - DB외에 입력 데이터, 파일, Java Message Service등 다른 소스에서 읽어올 수 있음.
    - cursor와 paging 방식으로 데이터를 읽어올 수 있음.
    - 트랜잭션 범위는 chunk 단위
- **페이지 사이즈와 청크 사이즈를 일치시켜야 하는 이유**
    - 청크 사이즈가 50이고 페이지 사이즈가 10이면 5번의 read가 발생하면서 한 번의 트랜잭션 처리를 위해서 조회 쿼리를 5번하는 성능 이슈가 발생할 수 있다.
    - JPA를 사용하는 경우 영속성 컨텍스트가 깨지는 문제가 발생한다. JpaPagingItemReader의 경우 페이지를 읽을때, 이전 트랜잭션을 **flush, clear**로 초기화 시켜버린다. 그러다 보니 마지막에 읽은 페이지를 제외한 이전에 조회한 결과들의 트랜잭션 세션이 전부 종료되어 버리면서 processor에서 Lazy 로딩이 불가능하게 되는 현상이 발생한다.
- fetch 사이즈와 청크 사이즈를 일치시켜야 하는 이유
    - fetch 사이즈를 10으로, 청크 사이즈를 50으로 할 경우, 한번의 트랜잭션 처리를 위해 DB로부터 10만큼가져와 메모리에 로드하는 작업을 5번 후 procesor와 writer를 실행하므로, 메모리 여유가 된다면 fetch와 청크 사이즈를 일치 시키는 것이 성능 상 좋다.
- cursor
    - 모든 데이터를 처리할 때까지 커넥션 유지, 커넥션이 끊어지면 데이터에 접근하는 커서도 없어지기 때문에 계속 유지해야 한다. → connection timeout이 길어야 함.
    - 데이터를 호출하면 DB에서 메모리로 fetch 사이즈만큼 가져와서 할당 후, Spring Batch는 설정된 chunkSize에 따라 가져온 데이터를 처리한다. 그 후 다음 커서로 이동하는 스트리밍 방식.
    - 멀티스레드 환경에서 동시성 이슈 발생하므로 동기화 처리가 필요
- cursor 동작 방식
    
    execuQuery 를 하게 되면 보통 db에 다녀와서 rs 에다가 넣어둔다고 생각을 했었다.
    그러나 rs.next 메소드가 호출 되는 시점에 db에서 fetch size만큼 데이터를 가져오게 된다.
    
    일반적으로 청크 지향 배치에서는 청크를 트랜잭션 범위로 하지만, cursorItemReader는 청크가 트랜잭션 대상이 아니다. cursorItemReader는 DB connection이 끊어지게 되면, DB 내 cursor를 조회할 수 없어지기 때문에, 모든 결과를 다 읽을 때까지 커넥션을 유지한다. 
    
    chunk 사이즈만큼 차게 되면, processor와 writer를 수행한다. 
    
    그 
    
    그리고 모든 결과를 다 읽을 때까지 반복한다.
    
- cursor 재처리
    
    jdbcCursorItemReader 또한 실행 상태가 실패했을 때 재처리를 위해서는 order by를 통해 과거의 read완료되었음을 기록한 커서 순서대로 실행시킬 수 있다.
    
    paging과의 차이점은 paging은 page사이즈 만큼 매번 커넥션 연결하면서 order by  후 limit만큼 가져온다 , 결국 이 작업을 데이터 총 수 / 페이지 사이즈 만큼 해야한다. 따라서 훨씬 비효율적이다.
    
     cursor는 커넥션 연결 중 한번의 SQL 문을 요청 후, 결과값을 DB 메모리에 저장하고, fetch size만큼 서버메모리에 가져오는 방식이므로 한번 정렬 쿼리하므로 커넥션 time out이 충분히 길다면 성능 측면에서 빠르다.
    
- paging
    - 페이지 사이즈 만큼 데이터를 한 번에 처리
    - 페이지 사이즈 만큼 커넥션을 맺고 끊음
    - 페이징 결과만 메모리에 할당
    - 멀티 스레드 환경에서 스레드 안정성을 보장하기에 별도 동기화 처리 불필요
- tasklet과 청크 방식 차이
    - Tasklet 기반 처리
        - 배치 처리 과정이 비교적 쉬운 경우 쉽게 사용
        - 대량 처리를 하는 경우 더 복잡
        - 하나의 큰 덩어리를 여러 덩어리로 나누어 처리하기 부적합
    - chunk 기반 처리
        - ItemReader, ItemProcessor, ItemWriter의 관계 이해 필요
        - 대량 처리를 하는 경우 Tasklet 보다 비교적 쉽게 구현
        - chunk사이즈만큼 채워질 때까지 하나하나 읽거나 page size혹은 fetch size만큼 읽고, chunk를 트랜잭션으로 하여 processor로 변환 혹은 필터링 작업 수행, chunk사이즈만큼 쓰기 작업 수행
        - Chunk 단위로 트랜잭션을 수행, **실패할 경우엔 해당 Chunk 만큼만 롤백**이 되고, 이전에 커밋된 트랜잭션 범위까지는 반영됨.
- multi thread
