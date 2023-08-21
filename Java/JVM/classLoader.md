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

## 가시성 제한

- 클래스 로더가 클래스 로드를 요청받았을 때 위임 모델에 의해 클래스 로더 캐시 확인 후, 없으면 상위 클래스 로더를 확인하는데, 이 때 상위 클래스 로더는 하위 클래스 로더에 있는 클래스를 찾을 수가 없다.

## 언로드 불가

- 클래스를 로드하는 건 가능하나, 언로드는 불가하다. 대신, 현재 클래스 로더를 삭제 후, 아예 새로운 클래스 로더를 생성하는 방법을 사용할 수 있다.

## Name Space

- 클래스 로더들이 가지고 있는 공간으로 로드된 클래스를 보관하는 공간
- 클래스를 로드할 때 위임 모델을 통해 상위 클래스 로더를 확인하는데, 그 때 확인하는 공간임
- FQCN(Fully Qualified Class Name)을 기준으로 보관됨, 패키지 명까지 포함되어있는 식별자를 뜻함.
- 패키지 명까지 같은 FQCN이 같은 클래스라도, 네임 스페이스가 다르면(다른 클래스 로더가 로드한 클래스면) 다른 클래스로 간주함.

## 클래스 로드 과정

1. 인스턴스는 메소드를 호출함. 그러나 그 메소드를 가진 클래스 바이트 코드가 로딩된 적 없다면, JVM은 JRE 라이브러리 폴더에서 클래스를 찾음.
2. 없으면, ClassPath 환경 변수에 지정된 폴더에서 클래스를 찾음.

### 3. 로드

클래스 파일을 가져와서 JVM의 메모리에 로드함

### 4. 검증

클래스 로드 전 과정 중에서 가장 복잡하고 시간이 많이 걸리는 과정

읽어들인 클래스가 자바 언어 명세 및 JVM 명세에 명시된 대로 구성되어있는지 검사

### 5. 준비

클래스가 필요하는 메모리 크기 만큼 할당.

즉, 클래스에서 정의된 필드, 메서드, 인터페이스들을 나타내는 데이터 구조들이 필요로 하는 메모리 할당

(메소드 영역, 힙영역, 스택 영역 등..)

### 6. 분석

클래스의 상수 풀 내 모든 심볼릭 레퍼런스(참조하는 대상의 이름만을 지칭함)을 

메소드 영역에 있는 다이렉트 레퍼런스(실제 메모리 주소 값)로 변경

### 7. 초기화

클래스 변수들을 적절한 값으로 초기화 함.

static 필드들을 설정된 값으로 초기화 등..

## String constant pool

```java
String str = "hello"; // String constant pool에 저장
String str2 = "hello"; // String constant pool에서 재사용
String str3 = new String("hello"); // 별도의 Heap 메모리에 저장

System.out.println(str == str2);	// true (같은 객체를 재사용하기 때문에)
System.out.println(str == str3);	// false
System.out.println(str.equals(str3));	// true
```

- 불변성을 위해서는 new 생성자를 통해 값을 초기화하지 않고, 리터럴 그대로 값을 초기화해야 함.
    - 즉 동적으로 생성되는 문자열의 경우, string constant pool에 저장되는 대상이 아님.
- String constant pool은 참조값을 잃으면 GC에 의해 메모리에서 해제됨.
- 힙 메모리 영역에 있음.
- String.intern() 메서드를 사용할 경우, String constant pool에 해당 문자열이 있는지 검증 후, 상수풀에 저장 하고 레퍼런스를 전달함.
    - 그러나 이 메서드는 느리므로 권장되지 않음.
    - 그리고 어지간해서는 GC 대상이 되지 않고, 애플리케이션 종료될 때까지 메모리에 남아있기에 사용하지 않는 게 좋음.

## Class Constant Pool

- 컴파일 시 생성되며, class file에 저장됨. → 바이트 코드

```arduino
classHello {
publicstaticvoidmain( String[] args ) {
for( int i = 0; i < 10; i++ )
            System.out.println( "Hello from Hello.main!" );
  }
}
```

위 클래스를 컴파일한 후, 상수풀을 열어보면 이런 상수들로 저장되어 있음.

```java
#1 = Methodref          #6.#16         // java/lang/Object."<init>":()V
   #2 = Fieldref           #17.#18        // java/lang/System.out:Ljava/io/PrintStream;
   #3 = String             #19            // Hello from Hello.main!
   #4 = Methodref          #20.#21        // java/io/PrintStream.println:(Ljava/lang/String;)V
   #5 = Class              #22            // Hello
   #6 = Class              #23            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               main
  #12 = Utf8               ([Ljava/lang/String;)V
  #13 = Utf8               StackMapTable
  #14 = Utf8               SourceFile
  #15 = Utf8               Hello.java
  #16 = NameAndType        #7:#8          // "<init>":()V
  #17 = Class              #24            // java/lang/System
  #18 = NameAndType        #25:#26        // out:Ljava/io/PrintStream;
  #19 = Utf8               Hello from Hello.main!
  #20 = Class              #27            // java/io/PrintStream
  #21 = NameAndType        #28:#29        // println:(Ljava/lang/String;)V
  #22 = Utf8               Hello
  #23 = Utf8               java/lang/Object
  #24 = Utf8               java/lang/System
  #25 = Utf8               out
  #26 = Utf8               Ljava/io/PrintStream;
  #27 = Utf8               java/io/PrintStream
  #28 = Utf8               println
  #29 = Utf8               (Ljava/lang/String;)V
```

## Runtime Constant Pool

- 위에서 언급한 Class file constant pool이 런타임시, 클래스로더에 의해 클래스를 로딩할 때 이 영역으로 저장됨.
- java 8 이전 버전은 perm gen 영역에, java 8 이후엔 metaspace 영역에 저장됨.

---

https://goodgid.github.io/Java-Class-Loader/

https://steady-snail.tistory.com/67
