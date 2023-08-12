✅ 개요

✅ 계층구조

✅ 위임모델

✅ 가시성 제한

✅  언로드 불가

✅  name space

---

# 개요
<img width="747" alt="Untitled (2)" src="https://github.com/uneap/tech-note/assets/25525648/9aa88e62-ec12-462b-9030-8813b56658c0">



## 과정

로딩 → 링크 → 초기화

## 로딩

1. 클래스 로더가 .class 파일을 읽음
2.  그 내용에 따라 적절한 바이너리 데이터를 만듦.
3. 해당 데이터를 method 영역에 저장
- method 영역
    - Type 정보 (클래스, 인터페이스, Enum)
    - 메소드와 변수
    - FQCN (Fully Qualified Class Name)

### 순서

1. Bootstrap
2. Extension
3. Application

순차적으로 1번에 없으면 2번 2번에 없으면 3번 조회

일반적으로 99%는 Application에 존재한다.

> 만약 찾지 못했을 경우 ClassNotFoundException
> 

### 로딩이 끝난 후

- 해당 Class Type의 **Class 객체**를 생성하여
- **힙 영역**에 저장한다.

---

# 디테일

1. 계층 구조 
: 클래스 로더 끼리 부모 - 자식 관계를 이루어 계층 구조로 생성됨
2. 위임 모델
: 계층 구조를 바탕으로 클래스 로더 끼리 로드를 위임하는 구조
3. 가시성 제한
: 하위 클래스 로더는 상위 클래스 로더의 클래스를 찾을 수 있지만, 상위 클래스 로더는 하위 클래스 로더의 클래스를 찾을 수 없다.
4. 언로드 불가
: 클래스 로더는 클래스를 로드할 수 있지만 언로드할 수 없다. 언로드 대신, 현재 클래스 로더를 삭제 후, 새로운 클래스 로더를 생성하는 방법을 사용할 수 있다.
5. 이름 공간(Name Space)
: 로드된 클래스를 보관하는 공간이다, 클래스를 로드할 때 이미 로드된 클래스인지를 확인하기 위해 네임 스페이스에 보관 된 FQCN(Fully Qualified Class Name)을 기준으로 클래스를 찾는다. 

## 계층 구조

![Untitled (3)](https://github.com/uneap/tech-note/assets/25525648/714a1d1a-4ede-4003-92f8-b5dfc493479d)


> BootStrapClassLoader, ExtensionClassLoader는 jvm 구성 요소 로드, SystemClassLoader는 사용자가 만든 object 로드
> 

### Bootstrap Class Loader

- 최상위 클래스 로더이며, 유일하게 java가 아니라 네이티브 코드로 구현됨
- jvm 실행될 때 같이 메모리에 올라감
- Object와 같은 java 기본 class 로딩

### Extension Class Loader

- 기본 class를 제외한 확장 class 로딩
- ex) 보안 확장 기능

### System Class Loader

- jvm 구성요소가 아닌 어플리케이션의 클래스들 로딩
- 사용자가 지정한 class path 내의 class

### User-Defined Class Loader

- 어플리케이션 사용자가 직접 코드 상에서 생성하여 사용하는 클래스 로더

## 위임 모델

> 로딩 요청을 위계적으로 전달하는 방식에 대해 다룬다.
> 

### 쓰이는 곳

- 처음 바이트 코드를 넘겨받은 클래스 로더가 필요한 클래스를 로드할 때
- 실행 엔진이 바이트 코드를 실행하면서 처음으로 참조하는 클래스를 클래스 로더에게 로드를 요청할 때

### 순서

1. 클래스 로더 캐시
2. 상위 클래스 로더
3. 자기 자신

### 특징

클래스 로더는 클래스를 로드할 때 최상위 클래스 로더(부트 스트랩 클래스로더)까지 순회 한다.

 만일 요청받은 클래스가 시스템 클래스 로더에 있더라도, 부트 스트랩 클래스 로더까지 확인하고, 부트 스트랩 클래스 로더에 있는 클래스를 로드한다.

---

https://goodgid.github.io/Java-Class-Loader/

https://steady-snail.tistory.com/67
