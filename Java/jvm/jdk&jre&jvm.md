<aside>
💡 JDK ⊃ JRE ⊃ JVM

</aside>

### JRE(java runtime environment)

- 자바 클래스 라이브러리(Java class libraries), 자바 가상 머신(JVM), 자바 클래스 로더(Java class loader)를 포함
- **운영체제 위**에서 실행되며, 거의 모든 운영체제에서 코드 수정 없이 실행될 수 있도록 함.
- JRE가 자바 애플리케이션을 실행할 수 있도록 해주므로 보통 자바로 개발된 프로그램들은 JRE를 포함하여 배포함.

### JDK(java development kit)

- 자바 개발 도구JRE + 개발을 위해 필요한 도구(javac, java 등)을 포함.

javac

java 파일을 **bytecode**로 컴파일.

컴파일 된 파일은 .class 파일로 저장됨.

java

java 명령어로는 javac 명령어로 컴파일한 **.class 파일을 실행**시킬 수 있음.

### JVM

- 자바로 만들어진 애플리케이션이 디바이스 혹은 클라우드 환경에서 실행되는 데 필요한 **리소스를 확보**하도록 보장하는 역할.
- 플랫폼마다 다른 JVM을 설치해야 함.
