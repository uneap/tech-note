# JDK8 에서 perm 영역이 삭제된 이유

- JDK 8부터 Permanent Heap 영역이 제거되었다.
    - 대신 Metaspace 영역이 추가되었다.
    - Perm은 JVM에 의해 크기가 강제되던 영역이다.
- Metaspace는 Native memory 영역으로, OS가 자동으로 크기를 조절한다.
    - 옵션으로 Metaspace의 크기를 줄일 수도 있다.
- 그 결과 기존과 비교해 큰 메모리 영역을 사용할 수 있게 되었다.
    - Perm 영역 크기로 인한 `java.lang.OutOfMemoryError`를 더 보기 힘들어진다.

jvm 벤더인 JRockit과 Hotspot을 통일 시키기 위해 permgen 영역을 삭제하고자 한다 함.

Java7 까지의 HotSpot JVM 구조를 보자.

```
<----- Java Heap ----->             <--- Native Memory --->
+------+----+----+-----+-----------+--------+--------------+
| Eden | S0 | S1 | Old | Permanent | C Heap | Thread Stack |
+------+----+----+-----+-----------+--------+--------------+
                        <--------->
                       Permanent Heap
S0: Survivor 0
S1: Survivor 1

```

그리고 Java 8 HotSpot JVM 구조를 보자.

```
<----- Java Heap -----> <--------- Native Memory --------->
+------+----+----+-----+-----------+--------+--------------+
| Eden | S0 | S1 | Old | Metaspace | C Heap | Thread Stack |
+------+----+----+-----+-----------+--------+--------------+
```

native memory는 OS에서 직접 관리함.

|  | Java 7 | Java 8 |
| --- | --- | --- |
| Class 메타 데이터 | 저장 | 저장 |
| Method 메타 데이터 | 저장 | 저장 |
| Static Object 변수, 상수 | 저장 | Heap 영역으로 이동 |
| 메모리 튜닝 | Heap, Perm 영역 튜닝 | Heap 튜닝, Native 영역은 OS가 동적 조정 |
| 메모리 옵션 | -XX:PermSize-XX:MaxPermSize | -XX:MetaspaceSize-XX:MaxMetaspaceSize |

---

https://johngrib.github.io/wiki/java8-why-permgen-removed/
