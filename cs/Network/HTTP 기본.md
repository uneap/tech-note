## 모든 것이 HTTP

- HTML, TEXT
- IMAGE, 음성, 영상, 파일
- JSON, XML (API)
- 거의 모든 형태의 데이터 전송 가능
- 서버간의 데이터 주고 받을 때도 대부분 HTTP 사용

### HTTP 역사

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c8850611-6208-4f27-b4f4-3724281d8dc8/Untitled.png)

2와 3는 성능 개선에 초점이 맞춰져 있기에, 1이 가장 중요.

### 기반 프로토콜

- TCP: HTTP/1.1, HTTP/2
- UDP: HTTP/3

## 클라이언트 서버 구조

- Request Response 구조
- 클라이언트는 서버에 요청을 보내고 응답을 대기
- 서버가 요청에 대한 결과를 만들어서 응답

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b82eab2f-b64f-4708-9490-39908b85374f/Untitled.png)

클라이언트와 서버 양쪽이 독립적으로 진행할 수 있다.

## Stateful, Stateless

고객이 점원에게 요청할 때 이야기 도중 점원이 바뀌지 않는 경우 (상태 유지)

고객이 점원에게 요청할 때 이야기 도중 점원이 계속 바뀌는 경우(무상태)

- 점원은 고객이 원하는 바를 모르기에, 고객은 지속적으로 본인의 원하는 바를 표현해야한다.

### 무상태일 경우

- 갑자기 고객이 증가해도 점원을 대거 투입할 수 있다.
- 갑자기 클라이언트 요청이 증가해도 서버를 대거 투입할 수 있다.
- 무상태는 응답 서버를 쉽게 바꿀 수 있다. → 무한한 서버 증설 가능

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/909cd576-2c07-4389-824c-ab3338412f18/Untitled.png)

어떠한 문맥이 필요가 없으므로 장애가 나더라도 다른 서버로 요청할 수 있게 된다.

## Stateless 실무 한계

- 무상태
    - 로그인이 필요없는 단순한 서비스 소개 화면
- 상태 유지
    - 로그인
- 로그인한 사용자의 경우 로그인 했다는 상태를 서버에 유지
- 일반적으로 브라우저 쿠키와 서버 세션을 사용하여 상태 유지
- 상태 유지는 최소한만 사용

## 비 연결성(connectionless)

### 연결을 유지하는 모델

서버는 연결을 계속 유지한다. 

클라이언트가 놀고 있더라도 계속 유지해야하기 때문에, 서버 자원을 소모하게 된다.

### 연결을 유지하지 않는 모델

서버는 연결을 유지하지 않는다.

따라서 최소한의 자원을 사용하게 된다.

- HTTP는 기본이 연결을 유지하지 않는 모델이다.
- 일반적으로 초 단위의 이하의 빠른 속도로 응답을 한다.
- 1시간 동안 수천명이 서비스를 사용해도 실제 서버에 동시에 처리하는 요청은 수십개 이하로 매우 작다.
- 따라서 서버 자원을 매우 효율적으로 사용할 수 있다.

### 한계와 극복

- TCP/IP 연결을 새로 맺어야 한다. - 3 way handshake 하는 시간이 추가된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06b05df6-1195-45bb-b364-f00c224bc78d/Untitled.png)

- 웹 브라우저로 사이트를 요청하면 HTML 뿐만 아니라 자바스크립트, css, 추가 이미지 등 수많은 자원이 함께 다운로드 된다.
- 지금은 HTTP 지속 연결로 문제 해결할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf0a91ea-5458-49bf-b7bb-73112875aecd/Untitled.png)

- HTTP/2, HTTP/3로 최적화 됨.

### 서버 개발자들이 어려워하는 업무

- 정말 같은 시간에 딱 맞추어 발생하는 대용량 트래픽
- 저녁 6시 선착순 1000명 치킨 할인 이벤트 → 수만명 동시 요청

## HTTP 메시지

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/40932e96-506d-4e0d-9d4e-e8eba9f86981/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00c09c89-bb65-41fd-ba2a-8ecd6c8829df/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f98f6f9-4c2b-4379-a0d8-c783ae59beee/Untitled.png)

## 시작 라인

### 요청 메세지

- start-line = **request-line** / status-line
- **request-line** = method SP(공백) request- target SP HTTP-version CRLF(엔터)

**HTTP 메서드**

- 종류: GET, POST, PUT, DELETE...
- 서버가 수행해야 할 동작 지정
- GET: 리소스 조회
- POST: 요청 내역 처리

**요청 대상**

- 절대 경로 = “/” 로 시작하는 경로
- 참고: *, http://...?x=y와 같이 다른 유형의 경로 지정 방법도 있다.

### 응답 메시지

- start-line = request-line/ status-line
- status-line = HTTP-version SP status-code SP reason-phrase CRLF
- HTTP 버전
- HTTP 상태 코드: 요청 성공, 실패를 나타냄
    - 200: 성공
    - 400: 클라이언트 요청 오류
    - 500: 서버 내부 오류
    - 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글
    

### HTTP 헤더

- header-filed = field-name “:” OWS field-value OWS (OWS: 띄어쓰기 허용)
- field-name은 대소문자 구문 없음

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/27a329e3-42bf-4fa2-a8f7-ea1f55bc7c64/Untitled.png)

- HTTP 전송에 필요한 모든 부가 정보
- 메세지 바디 내용, 메세지 바디 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보...
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능

### HTTP 메세지 바디

- 실제 전송할 데이터
- HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능
