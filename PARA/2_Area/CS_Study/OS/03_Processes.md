- Overview
- Process scheduling
- Operations on processes
- Inter-process communication
- Example of IPC system
- Communication in client-server systems

---
# Overview

## Process

> 프로세스 = 실행중인 프로그램 + 리소스 
> 메모리에 로드되어 CPU가 실행하고 있는 상태를 말함

- CPU Registers (**program counter (PC)**)
- Program Code (instruction의 나열)
- Stack
	- local var
	- functions
	- parameter
- Heap
	- dynamically allocated during program run
time
- Data
	- Global var

## Process State

- New: Being created
- **Ready**: 프로세서에 할당되기를 기다림
- **Running**: 실행중
	- 하나의 프로세서에서는 오직 하나의 프로세스만 실행가능
	- **time-sharing**으로 짧은 시간동안 CPU를 번갈아가며 사용
- **Waiting**: 이벤트가 발생하기를 기다림
- Terminated

![[Screenshot 2025-04-12 at 13.52.21.png]]

## Ready / Running State

![[Screenshot 2025-04-12 at 13.59.13.png]]

running중인 프로세스는 time-slice 이 만료됐을 때 다시 Ready상태로 돌아가고, **Ready Queue**에 있는 높은 우선순위의 프로세스가 Running 상태로 바뀜

### Ready Queue

CPU를 할당받아 실행될 준비가 된 프로세스들의 목록

## PCB (Process Control Block)

> OS는 PCB를 사용하여 프로세스들을 관리한다.

### PCB
프로세스에 대한 모든 정보를 담는 repository (저장소)

| **Contents**           | **Examples**                                                   |
| ---------------------- | -------------------------------------------------------------- |
| Process state          | new, ready, running, waiting, terminated                       |
| Process number         | **pid** (Process ID)                                           |
| CPU Registers          | **pc**(의 값) , 범용 registerah, **stack pointer**                 |
| CPU Scheduling info    | priority, pointer to queue                                     |
| Memory-management info | base and limit registers, page/segment table                   |
| Accounting info        | 사용된 CPU-time, time limits, account numbers, job/process number |
| I/O status info        | process에 할당된 I/O device 리스트와 open file 리스트                     |

### PCB in Linux

C Structure `task_struct`
```c
struct task_struct {
	pid_t pid;
	long state;
	unsigned int time_slice
	struct task_struct *parent
	struct list_head children;
	struct files_struct *files
	struct mm_struct *mm
};
```
- `pid`: process id
- `state`: state of the process
- `time_slice`: scheduling information
- `task_struct *parent`: 자기 프로세스를 생성한 부모 프로세스의 task_structure 구조체 포인터 (유일한 값)
- `list_head children`: 자식 프로세스 목록 (linked-list의 헤드값 보유)
- `mm_struct *mm`: 프로세스의 주소공간 (`mm == memory map`), 가상 메모리 주소 공간에 대한 정보-> 메모리 영역 관리에 사용


---
# Process Scheduling

## Process Sceduling

### Scheduling

> 작업들을 자원에 할당하는 것

### **Process scheduling**

> CPU에서 실행할 프로세스를 선택하는 것

- 한 프로세서에서는 오직 하나의 프로세스만 실행할 수 있다
- 다른 프로세스는 기다려야 함

### Scheduling의 목적

- CPU utilization 극대화
- 사용자들이 각 프로그램과 상호작용

## Scheduling Queue

> Waiting List of processes for CPU time or other resources
> 
> CPU 시간 혹은 다른 자원을 사용하기 위해 대기하는 프로세스들의 리스트

- Ready queue
- Job queue <-> Job pool
- Device queue

> 각 Queue는 PCB의 linked-list로 표현된다.

- queue는 head와 tail을 가짐 (tail은 insertion의 편리성을 위함)

## Queueing Diagram

![[Screenshot 2025-04-12 at 15.00.58.png]]

- **Ready queue**
	- 프로세스가 CPU 할당받을 준비가 완료된 상태로 대기
	- I/O 작업이 완료되거나 time slice가 만료된 후 다시 ready queue
- CPU (Running)
	- I/O req
	- time slice expired
	- fork a child  - > child executes
	- wait for an interrupt -> interrupt occurs

## Scheduler

> 스케줄러는 특정 방법으로 queue에서 프로세스를 선택한다 

- Long-term scheduler (**Job scheduler**)
- Short-term scheduler (**CPU scheduler**)

![[Screenshot 2025-04-12 at 15.07.04.png]]
### Short-term scheduler (**CPU scheduler**)

Ready queue에 있는 프로세스 중 다음에 CPU를 할당할 프로세스를 선택 (메모리 영역에서)

![[Screenshot 2025-04-12 at 15.07.36.png]]

- `10-100ms`마다 자주 실행됨
- 스케줄링 시간은 매우 짧음

Completely fair scheduler ?

### Long-term scheduler (**Job scheduler**)

시스템에 제출된 작업들이 대기 (디스크 영역에서)
Job queue에서 작업을 선택해서 Ready queue로 이동시키는 역할

- **degree of multiprogramming**을 조절 (ready queue에 적절한 수의 프로세스를 유지)
	- 안정된 상태에서 `평균 프로세스 생성률 == 프로세스 종료율`
- 실행 빈도가 낮다
	- 프로세스가 종료되어 메모리 자원이 확보됐을 때 실행
- Long-term 스케줄러는 **I/O-bound**프로세스와 **CPU-bound**프로세스를 적절히 섞어서 선택해야 한다.

#### I/O-bound process
- I/O 작업에 많은 시간을 소비하는 프로세스
ex) vi ...

#### CPU-bound process
- CPU 연산에 많은 시간을 소비하는 프로세스
ex) compiler

Long-term scheduler가 I/O bound와 CPU bound 프로세스를 잘 섞으면, CPU와 I/O 장치가 동시에 활발히 사용되어서 효율적

## Scheduler

- UNIX, Windows같은 시스템에서는 long-term 스케줄러가 없거나 역할이 적을 수 있음.
	- short-term 스케줄러가 전체 queue를 책임지는 구조
	- self-adjusting nature of human (user가 프로세스를 알아서 관리)

### medium-term scheduler
몇몇 time-sharing 시스템에서는 medium-term scheduler가 있다.

메모리에서 프로세스를 제거해서 **degree of multiprogramming**을 줄인다.

![[Screenshot 2025-04-12 at 15.41.27.png]]

- `swap out`: 메모리에 있는 프로세스 일부를 일시적으로 디스크(보조기억장치)로 보낸다.
- `swap in`: swap out된 프로세스를 다시 메모리로 로드한다.

## Context Switch

실행중인 프로세스를 switch하려면 **context switch**가 필요하다
- 현재 프로세스의 context를 저장 (**PCB**)
- 다음 프로세스의 context를 복원
![[Screenshot 2025-04-12 at 15.53.12.png]]

### Context switch

> 여러 프로세스가 하나의 CPU 자원을 공유할 수 있도록 CPU의 context를 저장하고 복원하는 컴퓨팅 과정

#### Context
- Register contents
	- PC program counter
	- SP stack pointer
	- etc (gp, state)
- OS specific data
	- 고금 메모리 관리 기술에 필요한 추가 데이터 ex_ page table, segment table
	- 프로세스별로 추가 데이터를 가지며, switch 시 같이 전환

### 언제 Context switch함?

- Multitasking
- Interrupt handling (user mode -> ISR (현재 진행 중인 프로세스 저장하고 인터럽트 핸들러 실행) -> kernel mode)

### context switching 은 상당한 오버헤드를 필요로 한다.
### context switching을 위한 하드웨어 지원
- H/W switching - 모든 레지스터의 내용을 한번에 저장하는 명령어
- Multiple set of register for fast switching
	- 고급 CPU 아키텍쳐는 여러개의 레지스터 세트를 내장하고, 레지스터 내용을 저장하고 로드하는 대신 다른 레지스터 세트로 전환

![[Screenshot 2025-04-12 at 16.09.21.png]]



# Operations on Processes

## Process creation


