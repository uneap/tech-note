# JVM이란 무엇인가

<aside>
💡 JDK ⊃ JRE ⊃ JVM

</aside>

![Untitled (3)](https://github.com/uneap/tech-note/assets/25525648/7e9227bb-6ea2-4532-91d2-14e315cb700e)



### JDK(java development kit)

- 자바 개발 도구JRE + 개발을 위해 필요한 도구(javac, java 등)을 포함.

javac

java 파일을 bytecode로 컴파일.

컴파일 된 파일은 .class 파일로 저장됨.

java

java 명령어로는 javac 명령어로 컴파일한 **.class 파일을 실행**시킬 수 있음.

### JRE(java runtime environment)

- 자바 클래스 라이브러리(Java class libraries), 자바 가상 머신(JVM), 자바 클래스 로더(Java class loader)를 포함
- 운영체제 위에서 실행되며, 거의 모든 운영체제에서 코드 수정 없이 실행될 수 있도록 함.
- JRE가 자바 애플리케이션을 실행할 수 있도록 해주므로 보통 자바로 개발된 프로그램들은 JRE를 포함하여 배포함.

### JVM

- 자바로 만들어진 애플리케이션이 디바이스 혹은 클라우드 환경에서 실행되는 데 필요한 **리소스를 확보**하도록 보장하는 역할.

# JVM이란?

가상 머신이란 프로그램을 실행하기 위한 물리적 머신과 유사한 머신을 소프트웨어로 구현한 것이다.

JAVA Virtual Machine

### JVM의 역할

- JAVA와 OS 사이에서 **중개자** 역할을 수행한다.
- OS에 구애받지 않고 재사용을 가능하게 해준다.
- **메모리 관리, Garbage collection**을 수행한다.
- **스택 기반**의 가상머신이다.

### JVM이 중요한 이유

속도 저하 현상이나 튕김 현상을 줄이기 위해 한정된 메모리를 효율적으로 사용하여 최고의 성능을 내기 위함이다.

### 프로그램 실행 과정

1. 프로그램이 실행되면 **JVM은 OS로부터 이 프로그램이 필요로 하는 메모리를 할당받는다**. JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 읽어들여 자바 바이트코드(.class)로 변환 시킨다.
3. Class Loader를 통해 class파일들을 JVM으로 로딩한다.
4. 로딩된 class파일들은 Execution engine을 통해 해석된다.
5. 해석된 바이트코드는 Runtime Data Areas 에 배치되어 수행이 이루어지게 된다. 이러한 실행과정 속에서 JVM은 필요에 따라 Thread Synchronization과 GC같은 관리작업을 수행한다.

### JVM 구성

![Untitled (4)](https://github.com/uneap/tech-note/assets/25525648/0016bb71-a83e-4500-a2a0-4cae69703e8e)


- **class Loader**
    - 클래스 로딩 과정을 수행하는 주체.
    - 확장자가 .class인 파일의 위치를 찾아 클래스를 메모리에 로드하고 사용할 수 있게 만들어줌.
    - 링크를 통해 Runtime Data Area에 배치하는 작업을 수행하는 모듈
    - Runtime 시 동적으로 클래스를 로드함.
    - jar 파일 내 저장된 클래스들을 JVM 위에 탑재하고 사용하지 않는 클래스들은 메모리에서 삭제함.

[class loader](https://www.notion.so/class-loader-080747562e5b44bc85430c99945887f8?pvs=21)

- **Execution Engine**
    - 클래스를 실행시키는 역할
    - 실행 엔진은 클래스 로더에 의해 JVM 내 런타임 데이터 영역에 배치된 바이트 코드 실행
    - 자바 바이트 코드는 기계가 바로 수행할 수 있는 언어보다는 비교적 인간이 보기 편한 형태로 기술된다. 그래서 실행 엔진은 이와 같은 바이트 코드를 실제로 JVM 내부에서 기계가 실행할 수 있는 형태로 변경한다.
    - 실행 엔진이 사용하는 두가지 방식
        - **Interpreter**
            
            [인터프리터 vs 컴파일러](https://www.notion.so/vs-6c8feed5550c4cfe895e5753dd4ddc40?pvs=21) 
            
        - **JIT(Just-In-Time)**
            
            [JIT 컴파일러란?](https://www.notion.so/JIT-1ccd79fbb5614325ad38acba9fadbed5?pvs=21) 
            
- Ga**rbage collector**
    - jvm은 GC를 통해 메모리 관리 기능을 자동으로 수행
    - GC를 수행하는 모듈(스레드)가 있다.

![Untitled (5)](https://github.com/uneap/tech-note/assets/25525648/eca4dd73-cf8b-40df-97d7-8f31d4122c80)


> Runtime Data Area
> 

프로그램을 수행하기 위해 **OS에서 할당받은 jvm의 메모리 공간**

class loader에서 준비한 데이터들을 보관하는 저장소

1. **PC Register**
2. **JVM 스택 영역**
3. **Native method stack**
4. **Method Area == Class Area == Static Area**
5. **Heap**

![image](https://github.com/uneap/tech-note/assets/25525648/42e9d3a1-2320-459e-afb4-69b441c0f936)


### **PC Register**

- Thread 가 시작될 때 생성되며 생성될 때마다 생성되는 공간으로 스레드마다 하나씩 존재한다.
- Thread가 어떤 부분을 어떤 명령으로 실행해야할 지에 대한 기록을 하는 부분으로 현재 수행 중인 **JVM 명령의 주소**를 갖는다.
- stack 영역에는 아주 많은 stack frame이 적재되어있음
- 그런데 외부 요인으로 인해 현재 Thread가 점유하여 사용하고 있는 CPU 자원을 인터럽트 당할 수 있음
- 따라서 현재까지 작업하고 있는 히스토리를 스택에 저장하고, 나중에 다시 스레드가 CPU를 할당받았을 때, 어떤 작업부터 다시 수행해야하는지 가리키는 변수는 **PC 레지스터**임.

### **JVM 스택 영역**

- 프로그램 실행과정에서 임시로 할당되었다가 메소드를 빠져나가면 바로 소멸되는 특성의 데이터를 저장하기 위한 영역이다.
- 각종 형태의 변수나 임시 데이터, 스레드나 메소드의 정보를 저장한다.
- 메소드 호출 시마다 각각의 스택 프레임(그 메서드만을 위한 공간)이 생성된다.
- 메서드 수행이 끝나면 프레임 별로 삭제를 한다.
- 메소드 안에서 사용되는 값들(local variable)을 저장한다.
- 또 호출된 메소드의 매개변수, 지역변수, 리턴 값 및 연산 시 일어나는 값들을 임시로 저장한다.

### **Native method stack**

- 자바 프로그램이 컴파일되어 생성되는 바이트 코드가 아닌 실제 실행할 수 있는 기계어로 작성된 프로그램을 실행시키는 영역이다.
- JAVA가 아닌 다른 언어로 작성된 코드를 위한 공간이다.
- JAVA Native Interface를 통해 바이트 코드로 전환하여 저장하게 된다.
- 일반 프로그램처럼 커널이 스택을 잡아 독자적으로 프로그램을 실행시키는 영역이다.
- 이 부분을 통해 C code를 실행시켜 Kernel에 접근할 수 있다.

### **Method Area (= Class area = Static area)**

- 클래스 정보를 처음 메모리 공간에 올릴 때 **초기화되는 대상을 저장하기 위한 메모리 공간**.
- 올라가게 되는 메소드의 바이트 코드는 프로그램의 흐름을 구성하는 바이트 코드이다.
- 자바 프로그램은 main 메소드의 호출에서부터 계속된 메소드의 호출로 흐름을 이어가기 때문이다.
- 대부분 인스턴스의 생성도 메소드 내에서 명령하고 호출한다.
- 사실상 컴파일 된 바이트코드의 대부분이 메소드 바이트코드이기 때문에 거의 모든 바이트코드가 올라간다고 봐도 상관없다.
- 이 공간에는 **Runtime Constant Pool**이라는 별도의 관리 영역도 함께 존재한다.
- 이는 상수 자료형을 저장하여 참조하고 중복을 막는 역할을 수행한다.
    - ***올라가는 정보의 종류***
        
        **1) Field Information**
        
        **멤버변수**의 이름, 데이터 타입, 접근 제어자에 대한 정보
        
        **2) Method Information**
        
        **메소드**의 이름, 리턴타입, 매개변수, 접근제어자에 대한 정보
        
        **3) Type Information**
        
        class인지 interface인지의 여부 저장 /Type의 속성, 전체 이름, super class의 전체 이름(interface 이거나 object인 경우 제외)
        

*Method Area는 클래스 데이터를 위한 공간이라면 Heap영역이 객체를 위한 공간이다.*

*Heap과 마찬가지로 GC의 관리 대상에 포함된다.*

### **Heap( 힙 영역 )**

객체를 저장하는 가상 메모리 공간이다. new연산자로 생성된 객체와 배열을 저장한다. 물론 class area영역에 올라온 클래스들만 객체로 생성할 수 있다. 힙은 세 부분으로 나눌 수 있다.

![image (1)](https://github.com/uneap/tech-note/assets/25525648/9518ba3b-94f3-47c8-8928-4eac92095124)


[GC](https://www.notion.so/GC-4749dee246894245a520f1d2d3ed5e65?pvs=21) 

**Permanent Generation**

생성된 객체들의 정보의 **주소값이 저장된 공간**이다. Class loader에 의해 load되는 Class, Method 등에 대한 Meta 정보가 저장되는 영역이고 JVM에 의해 사용된다. Reflection을 사용하여 동적으로 클래스가 로딩되는 경우에 사용된다. 내부적으로 Reflection 기능을 자주 사용하는 Spring Framework를 이용할 경우 이영역에 대한 고려가 필요하다.

**New/Young 영역**

- **Eden** :
    - **새롭게 생성한 객체** 대부분이 여기에 위치
    - 대부분이 금방 **접근불가능** 상태가 되며, 매우 많은 객체가 Young 영역에 생성되었다가 사라짐
    - 이 영역에서 객체가 사라질때 Minor GC가 발생한다고 말함.
- **Survivor 0 / 1** :
    - Eden영역에서 minor GC가 일어났을 경우, 참조되고 있는 게 확인되었을 때 여기에 위치

**Old 영역**

- 접근 불가능 상태로 되지 않아 age가 계속 쌓여 특정 age만큼 쌓였을 때, young 영역에서 살아남은 객체는 여기로 복사 됨.
- 대부분 young 영역보다 크게 할당함.
- 쉽게 가득차지 않기에 GC가 적게 발생함.
