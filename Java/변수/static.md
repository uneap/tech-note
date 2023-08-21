# static

## static 이란?

- 모든 객체가 공유하는 변수
- 인스턴스를 생성하지 않고서도 메모리 할당됨.
- 보통 상수 혹은 통용적으로 쓰이는 계산식 함수로 사용됨.
- 클래스가 메모리에 올라갈 때, 클래스 로더에서 로딩, 링크, 초기화 작업이 이뤄지면서 static 변수, 함수 또한 메모리 할당됨.

## static object 저장하는 공간

### method area

- Klass(java 클래스 메타 정보 참조) 구조, constant pool, annotations를 관리
- 모든 스레드가 해당 영역을 공유함
- 인스턴스 생성에 필요한 정보들을 관리

### jDK 8 이전

- perm generation 영역에 저장되며, 해당영역은 별개의 heap영역에 있고, method area에 해당 됨.
- perm generation 영역 크기가 정해져있으며, out of memory가 발생하기 쉽상.
- `-XX:+CMSClassUnloadingEnabled -XX:+CMSPermGenSweepingEnabled` 를 설정하면 GC의 대상이 될 수 있음. 그러나 이 것은 OOM을 잠시 연기하는 정도가 될 수 있으며, 속도 저하를 불러일으킴.
- perm generation은 기본적으로 설정된 사이즈가 작음

### JDK 8 이후

- heap 영역에 저장되며, method area에 해당 됨.
- 기본적으로 gc 대상임 (참조를 잃은 static object는 GC의 대상이 될 수 있다.)

## static 메서드

- 인스턴스 생성하지 않고도 메모리에 할당시키며, 호출이 가능함.
- 인스턴스를 생성하지 않았음에도 메모리에 할당되므로, static 변수가 아닌 인스턴스 변수를 사용할 수 없음
- 생성자 호출이 필요없기 때문에, 속도는 빠를 수 있음
    
    ```java
    public class Hello {
    	private static Integer number;
    	private String name;
    	public static String introduce() {
    		System.out.println(name); // 에러 -> 호출 시, 인스턴스변수가 아닌, 클래스로 직접 참조함
    															// static 메서드는 메모리에 올라왔으나,
    															// name은 인스턴스 별로 다른 레퍼런스를 갖는 객체로써
     															// 인스턴스가 초기화됨과 동시에 메모리에 할당될 수 있는데,
    															// 메모리에 할당되지 않아 에러
    	}
    }
    
    public class HelloBot {
    	public void print() {
    		Hello hello = new Hello();
    		System.out.println(hello.number); // 가능
    		System.out.println(Hello.number); // 가능
    	}
    }
    ```
    

## static 변수

- 클래스 이름으로 직접 참조

## static 지양하는 이유?

### 메모리 측면

- java 8 이후 버전 부터 perm gen 영역이 사라지고, static object가 힙 영역에 속하여서 gc의 대상이 되어, 참조를 잃은 static 변수에 경우, gc의 대상이 된다.
- 유효 범위 내에 있지 않으면 gc의 대상이 될 수있는데, static object는 프로그램이 끝날 때 유효 범위를 벗어나기 때문에 메모리 측면에서 static 객체가 마구 늘어나는 것은 메모리 효율이 떨어질 수 있다.

### 객체 지향 측면

**캡슐화**

- static은 **전역 변수**를 사용할 때 유용하다.
- 그러나 객체 지향 프로그래밍 원칙 중 캡슐화는, 객체의 결합도를 낮추고, 객체의 응집도를 높일 수 있는 수단인데, 이 원칙을 위반하여 다른 곳에 사이드 이펙트를 야기할 수 있다.

**다형성**

- static 메서드는 인터페이스를 구현하는데 사용될 수 없다.
- 따라서 추상화 상속을 방해하여, 확장성을 낮출 수 있다.

## main 메서드가 static으로 선언된 이유?

### static으로 선언될 경우

- java 프로그램이 실행되면서, 클래스가 로드되고 링크되고 초기화 될 때, 인스턴스 생성과 관계없이, static으로 선언된 메소드의 경우 jvm의 메소드 영역에 메모리에 할당된다.
- 속도가 비교적 빠르다.

### static으로 선언되지 않을 경우

- 인스턴스가 생성되고, 인스턴스가 초기화되고 main 메서드를 호출해야한다.
- 그렇다면 jvm은 main함수를 수행하기 위해 인스턴스 생성 및 초기화 작업을 거친 후, main함수를 실행해야할 것인데, 클래스가 로드되면서 static 메소드 영역이 메모리에 할당되는 것이 훨씬 효율적일 것.
