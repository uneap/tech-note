# URI

URI는 로케이터, 이름 또는 둘다 추가로 분류될 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4c9265ca-05ac-4716-8cca-480921dee761/Untitled.png)

- Uniform: 리소스 식별하는 통일된 방식
- Resource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- Identifier: 다른 항목과 구분하는데 필요한 정보
- URL - Locator: 리소스가 있는 위치를 지정
- URN - Name: 리소스에 이름 부여
- 위치는 변할 수 있지만, 이름은 변하지 않는다.
- URN 이름 만으로 실제 리소스를 찾을 수 있는 방법이 보편화되지 않음.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/353687e5-4c4c-46da-8797-bbb0e65e5f6b/Untitled.png)

## scheme

- 주로 프로토콜 사용
- 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
- http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능

## userinfo

- URL에 사용자 정보를 포함해서 인증
- 거의 안씀

## host

- 호스트 명
- 도메인 명 혹은 iP 주소를 직접 입력

## port

- 일반적으로 생략, 생략시 http는 80, https는 443

## path

- 리소스 경로, 계층적 구조
- /home/file1.jpg
- /members/100
- items/iphone12

## query

- key=value 형태
- query parameter, query string등으로 불림, 웹서버에 제공하는 파라미터, 
또한 숫자로 입력하더라도 모두  문자로 넘어가기에

## fragment

- 서버에 전송하는 정보 아님

# 웹 브라우저 요청 흐름

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e53dc961-9edf-4ecb-a662-1239a1926d3e/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/be88e129-60a6-4d7f-aeae-70d2ee74d3e9/Untitled.png)

HTTP 요청 메세지는 이런 식으로 만들어진다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9b6649cc-15dc-4081-9528-36b9e2952dd1/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5f444246-2007-4a0f-bb7e-c7b79dfea3a1/Untitled.png)

요청 서버가 해당 패킷을 전송하면, 응답 서버는 해당 패킷을 받은 후 HTTP 메세지를 꺼낸 뒤, 해석을 통해 알맞은 응답 메세지를 전송한다.
