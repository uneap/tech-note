# isolation level

동시에 여러 트랜잭션이 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있도록 허용할지 말지를 결정하는 것

격리 수준이 높아질 수록, 동시 처리 성능이 낮아진다.

| 격리 수준 | DIRTY READ | NON-REPEATABLE READ | PHANTOM READ |
| --- | --- | --- | --- |
| READ UNCOMMITTED | O | O | O |
| READ COMMITTED |  | O | O |
| REPEATABLE READ |  |  | O(InnoDB는 발생 X) |
| SERIALIZABLE |  |  |  |

### ReadUncommitted

transaction A와 transaction B가 있다고 가정하자.

```java
transaction A
update ~~
commit
```

```java
transaction B
select ~~
```

transaction A가 수행될 때, commit 전에 transaction B가 실행된다고 하면, 해당 select문을 통해 조회된 결과는 commit 후 결과이다.

해당 level 에서는 dirty read라는 문제점이 있다.

dirty read란, 트랜잭션이 커밋하지 않은 결과를 다른 트랜잭션에서 조회할 수 있는 것을 의미한다.

따라서 A트랜잭션을 롤백시켜버린다고 하더라도,

 B에서 업데이트 된 정보를 조회해버려서 DB상에 저장되지 않은 정보를 처리하게될 수 있다.

, 정합성에 문제가 많은 격리 수준이다.

### ReadCommitted

위와 같은 가정이라고 한다면,

 undo 영역이 존재하여 transaction A에서 update문을 수행 하기 전 데이터를 undo 영역에 저장하고 있는다.

<aside>
💡 undo 영역이 있음으로써 생기는 특징은,
롤백을 가능케 한다. 롤백 시, 해당 영역에 기록된 데이터로 롤백한다.
트랜잭션의 격리 수준을 높임으로써 높은 동시성을 보장한다. 
(커밋 전, update와 select는 서로 영향을 받지 않는다.)

</aside>

transactionB는 undo 영역에 저장하고 있는 데이터를 조회하게 된다.

만일 이슈로 인해 transactionA가 롤백 되더라도, 아까와 같이 롤백되었는지를 모르는 상황을 예방할 수 있다.

dirty read에 대해서는 정합성을 보장한다고 할 수 있겠지만,

 정합성에 어긋나는 상황이 존재한다.

transaction A와 transaction B가 있다고 가정하자.

```java
transaction A
Update ~~~
commit

transaction B
select ~~
select ~~
```

transaction A가 commit 전 transaction B에서 수행되는 select 문이 있고, commit 이후 수행되는 select 문이 있다.

commit 전 select 문에서는 undo 영역을 조회한다.

그러나 non Repeatable read 로, 부정합성의 문제를 야기한다.

commit 이후엔 update 문으로 변경된 데이터를 조회한다.

> 즉, 같은 트랜잭션에서 실행된 쿼리 문은 언제나 같은 결과를 반환해야한다는 조건에 부합하지 않는다.
> 

## Repeatable Read

해당 격리레벨에서는 ReadCommitted에 도입된 UnDO 영역에 백업된 업데이트 전 데이터를 활용하여 no repeatable read에 대한 정합성을 유지한다.

ReadCommitted와의 차이점은, REPEATABLE READ 격리 수준에서는 실행중인 트랜잭션 가운데 가장 오래된 트랜잭션 번호보다 트랜잭션 번호가 앞선 언두 영역의 데이터는 삭제할 수 없다.

즉, 언두 영역에 백업된 이전 데이터를 활용하여, 동일한 트랜잭션 내에서는 동일한 결과를 보여줄 수 있도록 보장한다.

<aside>
💡 그러나, 만일 한 사용자가 트랜잭션을 장시간동안 종료하지 않을 경우, 언두 영역에 백업된 레코드가 많아지면서, MySQL서버의 처리 성능이 떨어질 수 있다. (메모리의 데이터가 많아질 수록, 검색 시간이 길어짐)

</aside>

또한, 쓰기 작업에 대해서는 잠금이 걸리지 않는데,

```java
transaction A
Insert ~~~
commit

transaction B
select ~~
select ~~
```

transactionA를 수행하는 동시에 transactionB의 첫번째 select문을 수행하고, A가 commit을 한 뒤 B에서 두번째 select문을 수행한다고 가정했을 때, undo 영역에는 쓰기 잠금이 걸리지 않아, 같은 트랜잭션임에도 불구하고 다른 결과를 보여주게 된다.

phantom row라 부르며, 정합성을 잃는다.

## Serializable

한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없다.
