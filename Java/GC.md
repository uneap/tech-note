# GC

> 동적으로 할당한 메모리 영역 중 사용하지 않는 영역을 탐지하여 해제하는 기능
> 

## Stack

- 정적으로 할당한 메모리 영역
- 원시 타입의 데이터가 값과 함께 할당, Heap 영역에 생성된 Object 타입 데이터의 참조 값 할당

## Heap

- 동적으로 할당한 메모리 영역
- 모든 Object 타입의 데이터가 할당, Heap 영역의 Object를 가리키는 참조 변수가 Stack에 할당

## GC 과정

1. GC가 Stack의 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹한다.
2. Reachable Object가 참조하고 있는 객체도 찾아서 마킹한다.
3. 마킹되지 않은 객체를 Heap에서 제거한다.
![Untitled - 2023-06-30T144738 579](https://github.com/uneap/tech-note/assets/25525648/e2ada471-41a4-4a32-a521-23ff3e271d31)


> mark and sweep
> 
![Untitled - 2023-06-30T144740 592](https://github.com/uneap/tech-note/assets/25525648/370dd00c-0120-4bbd-a2c7-a228210812d3)


1. 새로운 객체는 Eden 영역에 할당된다.
2. GC 발생(minor GC) → Eden 영역의 Reacable 객체는 survival 0에 할당된다. unreachable 객체는 메모리에서 해제된다.
    1. survival 0나 survival 1 중 이미 객체가 차있는 영역으로 할당됨. survival 0나 survival 1 둘 중 하나는 항상 비어있는 상태로 됨.
3. survival 0 영역이 가득차게 될 경우 해당 영역에 대해서도 mark and sweep이 일어남 (survival 1 영역의 경우도 마찬가지)
    1.  survival 0 영역에 있던 객체는 survival 1 영역으로 이동, 이동한 객체는 age 값 증가.
4. age 값이 특정 값 이상이 되면 old generation 영역으로 옮겨짐, 이 과정을 promotion이라 함.
5. promotion이 지속되면 old generation 영역에 데이터가 쌓임, GC 발생(major)

## Stop the world

- GC 실행하기 위해 JVM이 애플리케이션 실행을 멈춤
- stop-the-world가 발생하면 GC를 실행하는 스레드를 제외한 모든 스레드가 작업을 멈춤
- GC 작업을 완료한 이후, 중단된 작업을 다시 실행

## Garbage Collector 종류

### Serial GC

- GC를 처리하는 스레드가 1개
- CPU 코어가 1개만 있을 때 사용하는 방식
- mark-compact collection 알고리즘 사용
    - mark and sweep + compact

### Parallel GC

- GC를 처리하는 스레드가 여러개
- serial GC보다 빠른 처리
- Parallel GC는 메모리가 충분하고 코어 개수가 많을 때 사용하면 좋음

### Concurrent Mark Sweep GC(CMS GC)

![Untitled - 2023-06-30T144743 810](https://github.com/uneap/tech-note/assets/25525648/7dcd05b7-c727-4735-8b5f-374905599f7a)


- stop the world를 줄여서 속도가 중요한 애플리케이션에 사용
- 다른 GC 방식보다는 메모리와 CPU를 더 많이 사용함 (다른 GC보다 사이클이 김)
- compaction 단계가 제공되지 않음.

### G1 GC

- 각 영역을 Region 영역으로 나눈다.
- GC가 일어날 때, 전체 영역(Eden, Survival, Old generation)을 탐색하지 않는다.

![Untitled - 2023-06-30T144751 367](https://github.com/uneap/tech-note/assets/25525648/7109f03a-4d06-4b1a-90cc-c131344f823e)


- 바둑판의 각 영역에 객체 할당하고 GC 실행함.
- 그러다가 해당 영역이 꽉 차면 다른 빈 영역에 객체를 할당하고 GC를 실행한다.
- STW 시간이 짧다.
- Compaction을 이용한다.
