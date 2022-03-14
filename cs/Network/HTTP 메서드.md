## HTTP API를 만들어보자

### 요구 사항 - 회원 정보 관리 API를 만들어라

- 회원 목록 조회 /read-member-list
- 회원 조회 /read-member-by-id
- 회원 등록 /create-member
- 회원 수정 /update-member
- 회원 삭제 /delete-member

### API URI 설계

가장 중요한 것은 **리소스 식별**

- 리소스의 의미는 뭘까?
    - 회원을 등록하고 수정하고 조회하는 게 리소스가 아니다.
    - 회원이라는 개념 자체가 바로 리소스
- 리소스를 어떻게 식별하는 것이 좋을까?
    - 회원을 등록하고 수정하고 조회하는 것을 모두 배제
    - 회원이라는 리소스만 식별하면 된다. → 회원 리소스를 URI에 매핑
    - 회원 목록 조회 /members
    - 회원 조회 /members/{id}
    - 회원 등록 /members/{id}
    - 회원 수정 /members/{id}
    - 회원 삭제 /members/{id}
    - 참고: 계층 구조상 상위를 컬렉션으로 보고 복수단어 사용 권장(member → members)
    

### 리소스와 행위를 분리

- URI는 리소스만 식별.
- 리소스와 해당 리소스를 대상으로 하는 행위를 분리
    - 리소스: 회원
    - 행위: 조회, 등록, 삭제, 변경

## HTTP 메서드 - GET, POST

- GET: 리소스 조회
- POST: 요청 데이터 처리, 주로 등록에 사용
- HEAD: GET과 동일하나, 메시지 부분을 제외하고, 상태 줄과 헤더만 반환
- OPTIONS: 대상 리소스에 대한 통신 가능 옵션(메서드)를 설명(주로 CORS에서 사용)
- CONNECT: 대상 자원으로 식별되는 서버에 대한 터널을 설정
- TRACE: 대상 리소스에 대한 경로를 따라 메세지 루프백 테스트를 수행

CONNECT, TRACE는 거의 사용 안함.

### GET

- 리소스 조회
- 서버에 전달하고 싶은 데이터는 query를 통해 전달
- 메세지 바디를 사용하여 데이터를 전달할 수 있으나, 지원하지 않는 곳이 많아서 권장하지 않음.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd3d65df-8833-41d0-a149-567c69a64bd8/Untitled.png)

### POST

- 요청 데이터 처리
- 메시지 바디를 통해 서버로 요청 데이터 전달
- 서버는 요청 데이터를 처리
    - 메세지 바디를 통해 들어온 데이터를 처리하는 모든 기능을 수행한다.
- 주로 전달된 데이터로 신규 리소스 등록, 프로세스 처리에 사용

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d87bc5f9-1b2f-4f24-9fbb-9c53a73ce24f/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c133f151-d7f5-4190-a609-f3195d0cdda8/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/980c87f4-c7dc-45dc-adff-69170480c557/Untitled.png)

- 스펙: POST 메서드는 대상 리소스가 리소스의 고유한 의미 체계에 따라 요청에 포함된 표현을 처리하도록 요청한다.
1. 새 리소스 생성(등록)
    - 서버가 아직 식별하지 않은 새 리소스 생성
2. 요청 데이터 처리
    - 단순히 데이터를 생성하거나, 변경하는 것을 넘어 프로세스를 처리해야할 경우
    - 주문에서 결제 완료 → 배달 시작 → 배달완료 처럼 단순히 값 변경을 넘어 프로세스 상태가 변경되는 경우
    - POST 의 결과로 새로운 리소스가 생성되지 않을 수도 있음.
    
    /orders/{orderId}/start-delivery(컨트롤 URI)
    
3. 다른 메서드로 처리하기 애매한 경우
    - JSON으로 조회 데이터를 넘겨야 하는데, GET 메서드를 사용하기 어려운 경우
    - 애매하면 POST

## HTTP 메서드 - PUT, PATCH, DELETE

- PUT: 리소스를 대체, 해당 리소스가 없으면 생성
- PATCH: 리소스 부분 변경
- DELETE: 리소스 삭제

### PUT

- 쉽게 이야기 해서 덮어씀

!**클라이언트가 리소스를 식별함!**

- 클라이언트가 리소스 위치를 알고 URI 지정
- POST와 차이점
- 리소스를 완전히 대체

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8eb20821-b3df-4ac0-8baf-091989c774aa/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4c47c59e-8573-4af9-918b-c8cd2be60c92/Untitled.png)

### PATCH

- 리소스 부분 변경

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cec6c445-2fd5-4dd1-88d3-c7ecde00ff53/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c0e8ef2c-990f-49aa-aed9-5d233d5151a0/Untitled.png)

### DELETE

- 리소스 제거

## HTTP 메서드의 속성

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0cb0ba83-9365-42d0-b04d-8fb0e682b933/Untitled.png)

### 안전

- 호출해도 리소스를 변경하지 않는다.
    - 만일 계속 호출해서, 로그가 많이 쌓여 메모리가 꽉찼을 경우, 장애가 발생하면?
        - 안전은 해당 리소스만 고려한다. 그 외에 것은 고려하지 않는다.

### 멱등

- 한번 호출하든 두번 호출하든 n번 호출하든 결과는 다 똑같다.
- GET: 같은 결과가 조회된다.
- PUT: 같은 요청을 여러번 해도 최종 결과는 같다.
- DELETE: 같은 요청을 여러번 해도 삭제 결과는 같다.
- POST: 멱등이 아니다. 두번 호출하게 되면 같은 결제가 중복해서 발생할 수 있다.
- 활용
    - 자동 복구 메커니즘
        - 서버가 TIMEOUT 등으로 정상 응답을 주지 못했을 때, 클라이언트가 같은 요청을 다시 해도 되는가?
- 재요청 중간에 다른 곳에서 리소스를 바꿔버리면?
    - GET → username:A, age:20
    - PUT → username:A, age:30
    - GET → username:A, age:30 → 2번 영향으로 바뀐 데이터 조회
    - 멱등은 외부 요인으로 중간에 리소스가 변경되는 것까지 고려하지 않는다.

### 캐시 가능

- 응답 결과 리소스를 캐시해서 사용해도 되는가?
- GET, HEAD, POST, PATCH 캐시 가능
- 실제로는 GET, HEAD 정도만 캐시로 사용
    - POST, PATCH는 본문 내용까지 캐시 키로 고려해야하는데, 구현이 쉽지 않음
