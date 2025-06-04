1. Background
2. The critical-section problem
3. H/W Support for Synchronization
4. Mutex and Semaphore
5. Monitors
6. Liveness

### Background

Process Communication 방법
- Message passing
- **Shared memory** (confliction  발생 가능)

Producer-consumer problem
![[Screenshot 2025-05-29 at 02.25.30.png]]

## Concurrent Access of Shared Data
공유 데이터에 동시에 접근했을 때 문제

```c
// 생산자 (Producer)
while(true){
    while(counter == BUFFER_SIZE); // 버퍼가 가득 차면 대기 (공간이 생길 때까지)
    buffer[in] = nextProduced;    // 생산된 아이템을 버퍼에 넣기
    in = (in + 1) % BUFFER_SIZE;  // 'in' 포인터 업데이트
    counter++;                     // 카운터 증가
}

// 소비자 (Consumer)
while(true){
    while(counter == 0);          // 버퍼가 비어 있으면 대기 (아이템이 생길 때까지)
    nextConsumed = buffer[out];   // 버퍼에서 아이템 가져오기
    out = (out + 1) % BUFFER_SIZE; // 'out' 포인터 업데이트
    counter--;                     // 카운터 감소
}
```

`counter`의 증가/감소 연산할 때, 기계어 수준에서 레지스터로 `counter`값을 fetch하고 그 값에 증가/감소 연산을 한 이후 다시 메모리의 `counter`변수에 writing한다.

modification(수정) 작업동안 스케줄러에 의한 context switching이 발생할 수 있다.
이때 **Race Condition**이 발생할  수 있다. (데이터 불일치)

해결을 위해서는 **Synchronization**을 보장 해야한다.

### Race condition
여러 프로세스가 동시에 동일한 데이터에 접근하여 조작하는 상황

Execution의 결과가 공유 데이터에 접근하는 순서에 따라 달라지는 상황

### Synchronization
공유 자원에 접근할 때 실행순서를 제어하여 **race condition**이 발생하지 않도록 함
-> 한번에 하나의 프로세스만 공유 데이터에 접근할 수 있도록 보장


## The Critical-Section Problem

### Critical-Section Problem
프로세스가 cooperate 할 수 있는 프로토콜을 설계

n개의 프로세스가 시스템에서 동시에 실행되고 있을 때
각 프로세스는 다음과 같이 구성된다.

#### Critical-Section
공유 리소스에 접근하거나 변경할 수 있는 코드 세그먼트
**race condition**을 피하려면 **critical section**에는 오직 하나의 프로세스만 진입하도록 보장
#### Remainder section
공유자원을 변경하지 않는 코드 세그먼트

#### Entry section
critical section에 진입 허가를 요청하는 코드 섹션

#### Exit section
critical section을 떠났음을 알리는 코드 섹션

![[Pasted image 20250603232829.png]]

### Requirements of Critical-Section Problem

#### Mutual exclusion
한 프로세스가 크리티컬 섹션에서 실행중이라면 다른 프로세스는 크리티컬 섹션에서 실행될 수 없다. (대기해야 함)

#### Progress
critical section이 비어있으면 무한정 대기 없이 waiting중인 프로세스들 중 하나가 진입해야한다

Critical section에서 실행중인 프로세스가 없을 때, Remainder section에서 실행중이지 않은 프로세스들만이 다음 Critical section에 진입할 프로세스 결정에 참여할 수 있다.

#### Bounded waiting
**starvation**방지를 위해
한 프로세스가 critical section 진입 요청을 한 후부터 그 요청이 허가될 때까지 다른 프로세스들이 자신의 critical section에 진입하는 횟수에 제한이 존재해야한다.

### Kernel is an example of critical section problem

커널은 프로세스와 스레드가 공유하는 핵심 데이터 구조와 리소스를 관리

#### Non-preemptive kernel
프로세스가 커널모드에서 실행중일 경우 context switching 이 발생할 수 없다.

race condition으로부터 자유롭다.

#### Preemptive kernel
critical section 문제를 해결한 preemptive kernel은

real-time programming에 더 적합 (응답성이 좋다.)

## Peterson's Solution



