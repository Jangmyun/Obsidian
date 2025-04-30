# CPU Scheduling

## Basic Concepts

### Motivation
멀티프로그래밍, 멀티태스킹 상황에서 **CPU utilization**을 극대화

![[Screenshot 2025-04-29 at 13.50.36.png]]

## CPU-I/O Burst Cycle

프로세스 실행은 CPU실행과 I/O 대기의 사이클

> 처음과 마지막 burst는 CPU burst이다.

![[Screenshot 2025-04-29 at 13.58.35.png]]
### 프로세스 타입
- I/O-bound process
	- CPU bursts가 짧게 많이
- CPU-bound process
	- CPU bursts가 길게 조금

![[Screenshot 2025-04-29 at 13.57.19.png]]

## CPU Sheduler

### CPU scheduler (= short term scheduler)
Ready queue로부터 실행할 프로세스를 선택하여 CPU에 할당

### Ready queue의 구현

FIFO, **Priority queue**, tree, unordered linked list

각 프로세스는 PCB로 표현


## Preemptive Scheduling 선점형 스케줄링

프로세스가 실행중인동안 스케줄링 발생 가능
(강제로 프로세스의 CPU 사용을 중단시키고 다른 프로세스에 CPU 할당)

ex) 인터럽트나 더 높은 우선순위의 프로세스

## 스케줄링의 발생시점

1. 프로세스가 running 상태에서 waiting 상태로 전환될 때
2. 프로세스가 running 상태에서 ready 상태로 전환될 때
3. 프로세스가 waiting 상태에서 ready 상태로 전환될 때
4. 프로세스가 종료될 때

![[Screenshot 2025-04-29 at 14.09.41.png]]

1번과 4번은 필수
2번과 3번은 스케줄링 방식에 따라 선택적이다.

non-preemptive의 경우 2번 3번 경우에 스케줄링이 발생하지 않고 프로세스가 스스로 CPU를 반납할 때까지 기다린다.

## Preemptive Scheduling

### Non-preemptive scheduling
위 스케줄링 케이스 1,4번에서만 스케줄링 발생
실행중인 프로세스는 interrupted 되지 않는다.

### Preemptive scheduling
1,2,3,4번 케이스에서 모두 스케줄링 발생
프로세스 실행중에 스케줄링이 발생할 수 있음
하드웨어 지원과 shared data handling 필요

**Preemptive scheduling**은 프로세스간 데이터를 공유할 때 **race condition**을 야기할 수 있다.

## Preemption of OS Kernel

### Preemptive kernel
커널이 시스템 콜 중 preemption 허용

-> real-time (반응성이 중요한 프로그램) support할 때 좋음

근데 대부분의 OS에서 시스템은 non-preemptive임

## Dispatcher

### Dispatcher
short-term 스케줄러에 의해 선택된 프로세스에세 CPU 제어권을 넘겨주는 모듈

- Switching context
- Switching to user mode
- Jumping to proper location in user program


### Dispatch latency
프로세스 중단부터 다른 프로세스 시작까지의 시간

![[Screenshot 2025-04-29 at 14.44.02.png]]


## Scheduling Criteria

- CPU utilization: CPU를 최대한 바쁘게
- Throughput: 시간 단위 당 완료된 프로세스의 수
- Turnaroundtime: 프로세스 제출부터 완료시점까지의 시간
- Waiting time: Ready Q에서 대기한 시간의 함
- Response time: 요청 제출 시점부터 첫 번째 응답이 나올 때까지의 시간

---
# Scheduling Algorithms

## First-come, first-served (FCFS) scheduling

> CPU에 먼저 요청한 놈이 먼저 served

Non-preemptive scheduling
가장 단순한 스케줄링 방법

근데 비효율을 곁들인

![[Screenshot 2025-04-30 at 11.38.40.png]]

## Shortest-Job-First (SJF) scheduling

> Burst time이 가장 짧은 프로세스를 선택

SJF는 waiting time의 관점에서 최적이다.

문제는 다음 CPU burst의 길이를 알기 어렵다는 것
-> 그동안 쌓인 burst time 기록을 기반으로 비슷하게 구현할 수 있음

![[Screenshot 2025-04-30 at 11.41.27.png]]

### Exponential averaging

![[Screenshot 2025-04-30 at 11.57.28.png]]

- `t_n`: n번째 CPU burst의 실제 길이
- `τ_n`: n번째 CPU burst의 예측값
- `α`: 0과 1사이의 값
	- `α = 0`: n번째 CPU burst의 실제 길이를 무시
	- `α = 1`: n번째의 예측값만으로 n+1을 예측
	- 보통 `α = 0.5` 사용

![[Screenshot 2025-04-30 at 12.01.23.png]]

### Preemptive version of SJF scheduling

![[Pasted image 20250430120335.png]]

## Priority Scheduling

> Priority가 가장 높은 프로세스에 CPU 할당

- priority가 같은 프로세스들 끼리의 priority scheduling은 FCFS와 같다

![[Screenshot 2025-04-30 at 12.13.16.png]]

### Priority can be assigned **internally** and **externally**

#### Internally
determined by measurable quantity or qualities

커널이 봤을 때 CPU burst가 짧게 자주 쓰이는 I/O-bound 프로세스 (real time이 중요한 프로세스) 를 위주로 선택

ex) time limit, memory requirement, # of open files, ratio of I/O burst and CPU burst, ...
#### Externally
importance, political factors


> Priority scheduling은 preemptive, non-preemptive 둘 다 가능하다

### Major problems

#### Indefinite blocking (starvation)
priority가 낮은 프로세스는 CPU 자원을 할당받지 못하는 문제

**aging**을 통해 오랫동안 waiting 상태인 프로세스의 priority를 점점 증가시킨다.

## Round-Robin (RR) Scheduling

> FCFS랑 비슷한데, 이제 preemptive 인..

- time-sharing system을 위함
- CPU time을 **time quantum (time slice)**로 나눔
- Ready queue를 **circular queue**로 취급
- CPU scheduler는 1 ready queue를 돌면서 time quantum만큼의 시간을 부여

![[Screenshot 2025-04-30 at 12.50.09.png]]

RR 스케줄링의 성능은 time quantum size에 의존적
- time quantum이 개짧음 = processor sharing
- time quantum이 개길음 = FCFS

