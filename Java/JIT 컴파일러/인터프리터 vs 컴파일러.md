### 컴파일러(compiler)

- 전체 파일을 스캔하여 한꺼번에 번역한다.
- 초기 스캔을 마치면 실행파일을 만들어 놓고 다음에 실행할 때, 이전에 만들어 놓은 실행파일을 실행하므로 빠르다.
- 컴파일러는 고급 언어로 작성된 소스를 기계어로 번역 후, 이 과정에서 **오브젝트 코드라는 파일**을 만드는데 이 오브젝트 코드를 묶어서 하나의 실행 파일로 다시 만드는 **링킹**이라는 작업을 해야 한다.
- 따라서 기계어 번역과정에서 인터프리터 보다 더 많은 메모리를 사용한다.
- 전체 코드를 스캔하는 과정에서 ****모든 오류를 한꺼번에 출력해주기 때문에 **실행 전에 오류를 알 수 있다.**
- 대표적인 언어로 C, C++, JAVA 등이 있다.

### 인터프리터(interpreter)

- 프로그램 실행시 한 번에 한 문장씩 번역한다.
- 한번에 한문장씩 번역후 실행 시키기 때문에 실행 시간이 느리다.
- 컴파일러와 같은 **오브젝트 코드 생성과정이 없기 때문에** 메모리 효율이 좋다.
    - 오브젝트 코드를 만들지도 않고, 링킹 과정도 거치지 않기 때문이다.
- 프로그램을 실행시키고 나서 오류를 발견하면 바로 실행을 중지 시킨다. **실행 후에 오류를 알 수 있다.**
- 대표적인 언어로 Python, Ruby, Javascript 등이 있다.
