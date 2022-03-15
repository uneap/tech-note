## 영속성 컨텍스트의 특징

### 1차 캐시

- 트랜잭션의 범위 안에서만 사용하는 짧은 캐시이다. 스레드가 종료되면 사라진다.
- Save 메서드와 같이 DB 변경하는 메서드를 실행했을 때, 영속성 컨텍스트 내부에 있는 캐시를 거쳐 DB가 업데이트 된다.
- 1차 캐시는 Map의 형태이며, key는 id값, value는 해당 entity값이 들어있다.
- key값이 id라서 id로 조회하면, 영속성 context에 있는 1차 캐시에 entity가 있는지 확인해보고,
값이 있다면 DB 조회 없이 return한다.
- 없으면 쿼리 문으로 조회 후, 1차 캐시에 저장 후 return 한다.
    - 그러나 id가 아닌 다른 값을 이용하여 조회하면 1차 캐시는 적용되지 않는다.
- 이러한 점으로 인해 하나의 transaction(@Transactional)에서 id값으로 조회하는 데이터들은 1차 캐시에서 관리하므로 JPA의 성능이 올라간다.

### 1차 캐시에서 조회하는 법
```
// entityManager.find(엔티티 클래스 타입, 식별자 값);
User findUser = entityManager.find("User.class", "1L");
```
data를 id값으로 반복적으로 찾을 때 @Transactional 이 붙는다면 JPA는 데이터를 조회하며 cache에 저장 후 다음 조회 때는 cache에 있는 값을 보낸다.
```
@Test
    void cacheFindTest() {
        System.out.println(userRepository.findById(1L).get());
        System.out.println(userRepository.findById(1L).get());
        System.out.println(userRepository.findById(1L).get());
    }
```
![](https://i.imgur.com/9QdoLOE.png)
이와 같이 한번 조회로 3개의 결과가 나온다.