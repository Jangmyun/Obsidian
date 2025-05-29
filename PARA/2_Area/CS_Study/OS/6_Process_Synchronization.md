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

