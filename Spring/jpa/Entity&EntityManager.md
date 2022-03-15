## Entity

DB의 테이블과 매칭되는 개념.

## EntityManager

Entity를 관리하는 역할을 수행하는 클래스이다.

- 엔티티 매니저 내부에는 영속성 컨텍스트를 두어 관리한다.
    - 영속성 컨텍스트
        
        비휘발성 컨텍스트
        

- 여러 엔티티 매니저는 하나의 영속성 컨텍스트를 공유할 수도 있다.
- 영속성 컨텍스트를 관리하는 모든 엔티티 매니저는 초기화 및 종료되지 않는 한 엔티티를 영구적으로
 저장한다.
- 엔티티 매니저를 초기화 및 종료 시키는 코드가 실행되면 영속성 컨텍스트는 소멸된다.
## EntityManager와 영속성 컨텍스트의 Entity 관리
```
public class Join {
    public void join(String name, int age) {
        // 아직까지는 해당 엔티티를 엔티티 매니저가 관리하지 않는다.
        Member member = new Member();
        member.setName(name);
        member.setAge(age);
        
        // 엔티티 매니저가 있다고 가정.
        // 추후에 엔티티 매니저 팩토리와 함께 엔티티 매니저를 어떻게 생성하는지 설명.
        EntityManager em;
        EntityTransaction tx = em.getTransaction();
        
        try {
            // 엔티티 매니저에서 수행하는 모든 로직은 트랜잭선 안에서 수행돼야 한다.
            tx.begin();
            
            // 이렇게 하면 해당 엔티티 매니저의 영속성 컨텍스트에 위에서 만든 member 객체가 저장된다.  
            // 이제 member 엔티티는 엔티티 매니저의 관리 대상이 되고, 영속성을 가졌다고 말할 수 있다.
            em.persist(member);
            
            // 트랜잭션을 커밋한다.
            tx.commit();
        } catch(Exception e) {
            tx.rollback();
        } finally {
            // 엔티티 매니저를 종료시켜줘야 한다.  
            // 아마 더 이상 사용하지 않는 자원이므로 더 이상 사용하지 않는 자원이라고 표시하는 것 같다.
            // 그럼 아마 GC가 해당 엔티티 매니저 자원을 수거해가서 메모리에 반환하지 않을까??
            // 성능 상 문제가 있어서 이렇게 종료시켜줘야 하는 건지 모르겠다. 
            em.close();
        }
    }
}
```
## Entity LifeCycle

- 비영속
    - 영속성 컨텍스트와 전혀 관계가 없는 상태
    - 객체를 생성만 한 상태
    
    ```java
    Member member = new Member();
    member.setId("member1");
    member.setUsername("회원1");
    ```
    
- 영속
    - 영속성 컨텍스트에 저장된 상태
    - Entity가 영속성 컨텍스트에 의해 관리되는 상태
    - 영속 상태가 된다고 바로 DB에 저장되는 것은 아니다.
    - commit 시점에 해당 정보들을 DB에 쿼리한다.
    - `entityManager.find()` 로 조회할 때 영속성 컨텍스트 1차 캐시에 없어서 DB에 조회한 후 해당 Entity가 1차 캐시에 올라간 상태
    
    ```java
    EntityManager entityManager = entityManagerFactory.createEntityManager();
    entityManager.getTransaction().begin();
    // 객체를 저장한 상태 (영속)
    entityManager.persist(member);
    ```
    
- 준영속
    - 영속성 컨텍스트에 저장되었다가 분리된 상태
    - 영속성 컨텍스트에서 지운 상태
    - 준 영속 상태에서는 영속성 컨텍스트가 제공하는 기능을 사용하지 못한다.
        - Dirty Checking, Update Query
        - Dirty Checking이란? - 변경감지
            - 이름 변경에 대한 Entity 데이터 변화를 감지한다.
            - 1차 캐시의 Entity와 Snapshot이 다른 것을 감지하고 update query를 날린다.
    
    ```java
    entityManager.detach(member);
    ```
    
- 영속 → 준영속 상태로 만드는 법
    - entityManager.detach(entity): 특정 entity를 준영속 상태로 전환
    - entityManger.clear() : 영속성 컨텍스트를 초기화
    - entityManager.close(): 영속성 컨텍스트를 종료
- 삭제
    - 실제 DB 삭제를 요청한 상태
    
    ```java
    entityManager.remove(member);
    ```
    

## 쓰기 지연 SQL 저장소

영속성 컨텍스트 내부엔 쓰기 지연 SQL 저장소라는 공간이 존재한다.

만일 해당 공간이 없을 경우,

같은 트랜잭션 단위로 이루어져야 할 SQL이 여러개인데, 그 중 하나가 질의 되는 시점에서 롤백해야한다면, 그 전에 질의되었던 것들까지 다시 롤백해야하게 된다.

그러나 해당공간이 있어서, 

1. 트랜잭션이 커밋되기 직전까지 모든 쿼리문은 영속성 컨텍스트 내부의 쓰기 지연 SQL 저장소에 저장된다.
2. 트랜잭션이 커밋되는 순간 DB로 저장된 SQL 쿼리한다.
3. 만약 트랜잭션 내부에 오류가 나서 롤백해야 한다면 커밋 전에 해당 롤백을 해야한다는 사실을 알거고, 쓰기 지연 SQL 저장소 내 데이터만 날리면 되니까 번복하는 절차가 없어진다.

 

## EntityManageFactory

엔티티 매니저에 여러 스레드가 동시에 접근할 경우 동시성 문제가 발생한다.

- 동시성 및 병렬
    
    동시성: 싱글 코어에서 멀티 스레드를 구현하기 위해 쓰이는 기법, 유저가 체감할 수 없을만큼 짧은 시간 단위로 작업들을 번갈아가면서 수행
    
    병렬: 멀티 코어에서 멀티 스레드를 구현할 때 쓰이는 기법, 동시에 여러 작업이 수행되는 것
    

엔티티 매니저 팩토리는 엔티티 매니저를 찍어내는 개념으로, 여러 스레드가 동시에 접근해도 안전하다.

엔티티 매니저 팩토리는 DB 당 하나 밖에 사용하지 않는다.

만일 동일한 엔티티 매니저 팩토리를 공유해서 사용하려면 싱글턴 인스턴스나 의존성 주입등을 통해 의식적인 노력이 필요하다.