# 트랜잭션

- 무결성을 유지하기 위해 논리적으로 묶은 작업의 단위

예를 들면 master db가 있고 slave db가 있을 경우, 가격 할인을 master db엔 반영이 되었으나 slave db에는 반영이 되지 않으면 안되므로, 이 경우 트랜잭션으로 두 db에 반영하여 무결성을 유지하도록 한다.

# Atomic : 원자성
트랜잭션에 정의된 연산들은 모두 성공적으로 실행되던지, 아니면 전혀 실행되지 않은 상태로 남아야 한다. (All or Nothing)

# Consistency: 일관성

- 모든 트랜잭션은 일관성 있는 데이터베이스 상태를 유지해야한다. 데이터베이스에서 정한 무결성 제약 조건을 항상 만족해야 한다.

ex) 모든 학생은 반드시 이름이 있어야 한다는 DB 제약이 있을 때,다음의 트랜잭션은 일관성을 위반한다.
1. 이름 없는 새로운 학생 추가하는 쿼리
2. 학생의 이름을 삭제하는 쿼리


# Isolation: 격리성

- 트랜잭션이 실행되는 중에 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안된다.
- 동시에 실행되는 트랜잭션들이 서로에게 영향을 미치지 않도록 격리해야 한다.
- 격리성은 동시성과 관련된 성능 이슈로 인해 격리 수준을 택할 수 있음.

https://zzang9ha.tistory.com/381

### **트랜잭션의 모든 쿼리는 성공해야 합니다.**

원자성이란 어떤 것이 더 이상 쪼개질 수 없는 성질을 말합니다. 

트랜잭션에서는 여러 가지 작업이 하나의 작업으로 간주됩니다.

 원자(atom)와 같이 쪼갤 수 없다는 뜻

### **만약 트랜잭션 도중 하나의 쿼리가 실패한다면, 해당 트랜잭션 모든 쿼리들은 ROLLBACK 되어야 함.**

쿼리가 실패하는 경우는 다양한 이유가 될 수 있습니다. 

예를 들어, 계좌 잔액처럼 0 밑으로 내려가면 안 되는 상황에 잔액이 음수가 된다던지,

 중복 고유 키,

 잘못된 SQL 구문

### **트랜잭션을 커밋하기 전에 데이터베이스에 CRASH가 발생해 (실패한 쿼리가 아닌 경우) 멈추게 된다면 트랜잭션 내 성공한 모든 쿼리가 ROLLBACK 되어야 합니다.**

 문제를 해결하고 복구되었을 때 데이터베이스는 트랜잭션 중 발생한 오류를 감지하고 **ROLLBACK** 해야 합니다.
 만일 계좌 이체를 하던 중 실패가 된다면, 계좌 이체 전에 남아 있던 금액으로 돌아가야 한다. 다른 트랜잭션에 영향을 받아 또 다른 금액이 되서는 안된다.

# Durability: 지속성

- 트랜잭션이 성공적으로 수행되면, 갱신된 데이터베이스의 내용은 영구적으로 저장된다.



