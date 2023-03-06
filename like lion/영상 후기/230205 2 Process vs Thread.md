# Process vs. Thread

### ***Q***. Thread 가 뭔가요?

- 하나의 Process 내부에서 멀티 Thread 로 여러가지 일을 동시성 으로 처리하는 기능
- 하나의 Process 내부에 존재하기 때문에 Thread 간 공유된 자원으로 통신 비용이 절감된다.

### ***Q***. Multi Process 는 뭔가요?

- 여러개의 독립된 Process 를 생성해 동시성 으로 처리하는 기능
- Thread 와는 다르게 각 Process 들은 독립되어 있기 때문에 Context Switching 비용이 크다.

### ***Q***. 두가지의 차이점이 무엇인가요?

- Process 는 Program 이 실행된 것 이고,
하나의 application 에 대해 2개 이상의 작업을 처리하기 위해 multi - process 혹은 multi - thread 방식을 사용한다.
- Thread 는 하나의 process 내부에서 나뉘어진 하나 이상의 실행 단위 이다.

### ***Q***.동시에라면 CPU 가 한 순간에 여러가지 일을 처리한다는 뜻 인가요?

- 동시와 동시성은 다르다.
동시는 정확이 같은 시간에 2개 이상의 작업을 한번에 처리하는 것을 뜻한다.
    - Multi - Core 의 경우 동시 작업이 가능하다.
- 동시성은 매우 빠른 텀으로 작업을 전환해 동시에 작업하는 것 처럼 보이게 작업하는 것을 뜻한다.
    - 동시에 실행이 되는 것 처럼 보이기 위해서 실행 단위는 시분할로 CPU 를 점유하며 Context Switching 을 한다.

<br>

### 📍 배경지식

- 실행 단위
    - CPU core 에서 실행하는 하나의 단위로 Process 와 Thread 를 포괄하는 개념
- Process
    - 하나의 Thread 만 가지고 있는 단일 Thread 프로세스
- 동시성
    - 한 순간에 여러가지 일이 아니라,
    짧은 전환으로 여러가지 일을 동시에 처리하는 것 처럼 보이는 것

<br>

## ✏️ Process vs. Thread

### 📍 Program vs Process

- ***Program***
    - 실행시키기 전에는 단순한 code 가 구현되어 있는 file 에 불과하다.
    - application 의 레시피 라고 할 수 있음
- ***Process***
    - Program 을 읽고 실행시킬 수 있는 것

<br>

### 📍 Program —> Process

[🔗 가상 memory 와 프로세스 실행 과정](https://github.com/choideakook/TIL/blob/main/like%20lion/영상%20후기/230224%20가상%20메모리.md)

1. Process 가 필요로 하는 제료가 Memory 에 올라와야 한다.
    - code - 실행 명령을 포함하는 코드들
    - data - static 변수 혹은 global 변수
    - heap - 동적 메모리 영역
    - stack - 지역변수, 매개변수, 반환 값 등등 … 일시적인 data
2. Process 에 대한 정보를 담고 있는 PCB 블럭이 Process 생성시 함께 만들어 진다.
3. Process 가 Memory 의 Program 을 읽어 application 이 실행된다.

### ⚠️ 만약 실행해야 할 Program 2개 이상 이라면

CPU 는 한번에 하나의 작업만 처리할 수 있기 때문에 Context Switching 을 통해 동시성 문제를 수행한다.

이 과정에서 모든 code, data, heap, stack 영역을 준비상태 → 실행상태 / 실행상태 → 준비상태 로 매우 빠른 속도로 반복해서 작업하기 때문에 효율이 매우 낮아진다.

<br>

### 📍 Thread (경량화된 Process) 로 문제 해결

- 하나의 process 에 다수의 thread 가 있을 때 code, data, heap 영역을 공통된 자원으로 공유한다.
- Thread 는 stack 영역만 따로 갖고있다.
- Context Switching 이 일어날 때 캐싱 적중률이 올라가 매우 효율적으로 동시성을 수행할 수 있게된다.
    - Switch 할 때 공유되는 영역을 제외한 stack 영역만 Switch 하게 된다.

<br>

## ✏️ Multi - Process vs. Multi -Thread

이 두가지 개념은 모두 application 에 대한 처리 방식의 일종이다.

<br>

### ⚠️ 하나의 application 이 여러가지 일을 처리해야 할 때 의 처리방법

ex) 여러 사용자가 로그인을 요청할 때

### 📍 Multi - Process

1. 한 Process 는 하나의 로그인 처리만 할 수 있기 때문에 동시에 모든 요청을 처리할 수 없다.
2. 부모 Process 는 fork() 를 통해 자식 Process 를 여러개 생성한다.
3. 생성된 자식 Process 를 통해 동시성을 수행한다.
    - 이 때 자식 Process 는 부모와 별개의 Memory 영역을 확보하게 된다.
- ***정리***
    - 각 Process 는 독립적 이기 때문에 IPC 를 통해서 통신을 해야 한다.
    - 개별 Memory 를 차지하기 때문에 자원 소모적 이다.
    - Context Switching 비용이 크다.
    - 동기화 작업이 필요하지 않다.

<br>

### 📍 Multi - Thread

- Multi - Proecess 와는 다르게 하나의 Process 내에서 구분지어진 실행 단위이다.
    - 만약 Process 가 다수의 Thread 로 구분되어 있지 않다면 단일 Thread 하나로 Process 가 실행된다.
    - Process 내부에서 여러개의 Thread 로 나누어저 실행되는 것을 multi - thread 라고 한다.
- ***정리***
    - 하나의 Process 내부에 존재하기 때문에 Thread 간 공유된 자원으로 통신 비용이 절감된다.
    - 공유된 자원으로 Memory 가 효율적이다.
    - Context Switching 비용이 적다.
    - 공유 자원 관리를 해주어야 한다.

<br>

### 📍 Multi - Process 의 사용 빈도가 더 많은 이유

- Multi - Thread 는 Thread 간에 긴밀하게 연결되어있기 때문에 하나의  Thread 에 문제가 생길 경우 전체 Process 에 영향이 미친다.
- multi - Process 는 운영 측면에서 약간 비효율 적이지만 Process 간의 영향을 덜 받게 된다.

<br>

## ✏️ Multi - Core

Multi - Process 와 Multi -Thread 는 처리 방식의 일종이기 때문에 Soft ware 분야에 가깝지만,
Multi - Core 의 경우 hard ware 의 분야에 가깝다.

<br>

### 📍 Single - core (동시성)

- 동시성을 수행하기 위해서 하나 이상의 Process ( 혹은 Thread ) 가 빠른 텀으로 전환이 되는 방식을 사용한다.
    - 짧은 순간의 CPU 의 시간을 분할해 동시에 실행하는 것 처럼 보이게 한다.

<br>

### 📍 Multi - Core (병렬 처리)

- 물리적으로 여러개의 코어를 사용해 실제로 여러 요청을 동시에 처리하는 방식