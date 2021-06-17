# Java
![image](https://user-images.githubusercontent.com/25525648/116489446-efcfc600-a8cf-11eb-80cf-95df2cb47cd3.png)
## 자바 프로그램 실행 과정
1. 프로그램이 실행되면 JVM은 OS로부터 이 프로그램이 필요로 하는 메모리를 할당받는다. JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 읽어들여 자바 바이트코드(.class)로 변환 시킨다.
3. Class Loader를 통해 class파일들을 JVM으로 로딩한다.
4. 로딩된 class파일들은 Execution engine을 통해 해석된다.
5. 해석된 바이트코드는 Runtime Data Areas 에 배치되어 수행이 이루어지게 된다.
이러한 실행과정 속에서 JVM은 필요에 따라 Thread Synchronization과 GC같은 관리작업을 수행한다.

## JVM 구조
1. Class loader: JVM 내로 클래스파일을 로드하고 링크하는 작업을 수행하는 모듈이다.
2. Execution engine: 클래스를 실행시키는 역할을 한다.자바 바이트 코드를 실제로 JVM 내
부에서 기계가 실행할 수 있는 형태로 변경한다
3. JIT(Just In Time): 인터프리터 방식의 단점을 보완하기 위해 도입된 컴파일러다. 인터프리
터 방식으로 실행하다가 적절한 시점에 바이트코드 전체를 컴파일하여 네이티브 코드로 변 경하고, 이후에는 더 이상 인터프리팅 하지 않고 네이티브 코드로 직접 실행하는 방식이다. 네 이티브 코드는 캐시에 보관하기 때문에 한 번 컴파일된 코드는 빠르게 수행하게 된다. JIT 컴파일러를 사용하는 JVM들은 내부적으로 해당 메서드가 얼마나 자주 수행되는지 체크하 고, 일정 정도를 넘을 때에만 컴파일을 수행한다.
4. Garbage collector: GC를 수행하는 모듈이 있다.
5. Runtime Data Area
1. PC Register: Thread가 어떤 부분을 어떤 명령으로 실행해야할 지에 대한 기록을 하 는 부분으로 현재 수행 중인 JVM 명령의 주소를 갖는다.
2. JVM 스택 영역: 프로그램 실행과정에서 임시로 할당되었다가 메소드를 빠져나가면 바 로 소멸되는 특성의 데이터를 저장하기 위한 영역이다. 메서드 호출시마다 각각의 스택 프레임(그 메서드만을 위한 공간)이 생성된다. 메서드 수행이 끝나면 프레임 별로 삭제를 한다.
3. Native method Stack: 자바 프로그램이 컴파일되어 생성되는 바이트 코드가 아닌 실 제 실행할 수 있는 기계어로 작성된 프로그램을 실행시키는 영역이다. JAVA가 아닌 다 른 언어로 작성된 코드를 위한 공간이다.
4. Method Area: 클래스 정보를 처음 메모리 공간에 올릴 때 초기화 되는 대상을 저장하 기 위한 메모리 공간이다. 올라가는 정보의 종류로는 멤버변수의 이름, 데이터 타입, 접 근 제어자에 대한 정보 등이 있다.
5. 힙영역: 객체를 저장하는 가상 메모리 공간이다. New 연산자로 생성된 객체와 배열을 저장한다.
## Garbage Collector

### 가비지 컬렉션은 어떤 원리로 소멸시킬 대상을 선정하는가?
- 가비지 컬렉터는 힙 내의 객체 중에서 가비지(참조되고 있지 않은 객체)를 찾아내고 찾아낸 가비지를 처리해서 힙의 메모리를 회수한다.
- 객체가 가비지인지 아닌지를 판단하기 위해 reachability라는 개념을 사용한다.
    - 어떤 힙 영역에 할단된 객체가 유효한 참조가 있으면 reachability 없다면 unreachability로 판단한다.
- 하나의 객체는 다른 객체를 참조하고, 다른 객체는 또 다른 객체를 참조할 수 있기 때문에 참조 사슬이 형성되는데, 이 참조 사슬 중 최초에 참조한 것을 root set이라 칭한다.
- 힙 영역에 있는 객체들은 총 4가지 경우에 대한 참조를 하게 된다.
1. 힙 내에 다른 객체에 의한 참조
2. java 스택, 즉 java 메서드 실행시에 사용하는 지역변수와 파라미터들에 의한 참조
3. 네이티브 스택에 의해 생성된 객체에 대한 참조
4. 메서드 영역의 정적 변수에 의한 참조

**2,3,4는 root set이다.**

### GC과정

![](https://i.imgur.com/I5j0JPu.png)

#### Minor GC
- 새로 생성된 대부분의 객체는 Eden 영역에 위치한다. Eden 영역에서 GC가 한번 발생 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
- 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 일정시간 참조되고 있다는 뜻이므로 old 영역으로 이동한다.

#### Major GC
- old 영역에 있는 모든 객체들을 검사하여 참조되지 않은 객체들을 한꺼번에 삭제한다.
- 시간이 오래 걸리고 실행 중 프로세스가 정지된다.(stop the world)
- major gc가 발생하면 gc를 실행하는 스레드를 제외한 나머지 스레드는 모두 작업을 멈춘다.
- GC 작업을 완료한 이후에야 중단했던 작업을 다시 시작한다.


## G1 GC & Parallel GC
- 라이브 데이터가 많은 경우 G1 GC가 적합

## 오버라이딩 vs 오버로딩
오버 라이딩이란 상위 클래스에 존재하는 메소드를 하위 클래스에서 필요에 맞게 재정의 하는 것을 의미한다.

오버 로딩은 상위 클래스의 메소드와 이름, return type은 동일하지만, 매개변수만 다른 메소드를 만드는 것을 의미한다. 다양한 상황에서 메소드가 호출될 수 있도록 한다.