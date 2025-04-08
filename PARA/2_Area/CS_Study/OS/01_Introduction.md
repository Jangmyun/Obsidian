OS는 #Kernel 과 additional program 모음
## Components of Computer System
![[Screenshot 2025-04-06 at 18.13.20.png]]
## OS가 하는 일
1. OS는 환경을 제공한다.
	1. os자체로는 의미있는 기능을 하지 않지만
	2. os가 제공하는 환경 내에서 작업을 용이하게 한다.
2. OS는 system resource를 관리한다.
	1. 유저와 프로그램이 system resource를 공유하도록 한다.

## Computer System Operation

컴퓨터를 켜면

1. Bootstrap program(firmware)이 실행됨 (ex. BIOS, UEFI)
2. OS kernel
	1. Boots (init)
	2. waits for some event
	3. Handle event
![[Screenshot 2025-04-06 at 18.14.30.png]]
Modern operating systems are **interrupt driven programs** 

## Modern Computer System
### Common bus
하나의 CPU와 여러개의 device controller가 메모리를 공유하며 통신할 수 있도록 연결하는 회로
![[Screenshot 2025-04-06 at 18.19.02.png]]
- 각 device controller는 local buffer를 가지고 있다.
- I/O 작업은 device에서 controller의 local buffer로 이루어짐
- device controller는 CPU에게 inturrupt를 발생시켜서 작업 완료 됐음을 알린다.

# Interrupt
## #Interrupt 란
- asynchronous signal from hardware or software indicating the need for attention
	- 각 interrupt마다 **IRQ(Interrupt ReQuest) number** 가 연결되어 있다
	- **interrupt handler (Interrupt Service Routine)** 가 다룸
- 각 interrupt type마다 어떤 동작을 하는지 결정하는 코드가 존재함
	- **interrupt vector** : table of interrupt handler

### Interrupt handler 종류
1. H/W interrupt
	1. CPU에 신호 전달
2. S/W interrupt
	1. System call (monitor call)- program으로부터의 request
	2. Exception - Divide by zero, invalid memory access, I/O exception 등

## Interrupt Mechanism

### Interrupt Handling
1. CPU가 현재 작업을 멈추고 interrupt handler를 실행
	1. #Interrupt_vector 는 시스템에서 발생하는 다양한 종류의 인터럽트 각각에 대응하는 interrupt handler의 시작 주소를 저장
2. Interrupt가 **ISR (Interrupt Service Routine)** 에 의해 처리
3. interrupt가 발생했을 때의 프로그램으로 다시 돌아옴
	1. interrupt handler 실행 전에 기존 프로그램의 주소와 상태를 저장해야 함

### Hardware Process
```pseudo
InterruptRequest = false;

while(haltFlag not set during execution) {
	IR = memory[PC];
	PC++;
	execute(IR);
	if(InterruptRequest) {
		save_PC();
		restore_PC(IRQ);
	}
}
```

## Interrupt-based I/O
1. CPU는 request를 보내고 current process를 계속하거나 다른 작업을 한다.
2. data 전송이 끝나면 I/O device는 interrupt한다.
![[Screenshot 2025-04-06 at 22.03.37.png]]

## Instruction-Execution Cycle

- 메모리로부터 instruction을 받아 instruction register에 저장
- instruction이 decode되어 operand를 메모리로부터 가져오고 register에 값을 저장한다.
- instruction에 있는 operand가 실행되고, 결과가 다시 메모리에 저장된다.

## Main [[Memory]]
- Array of cells to store to store data or instruction
- 각 cell은 address로 구별
- fast, volatile
## Storage Structure

![[Screenshot 2025-04-06 at 22.13.38.png]]

| Level            | 1        | 2      | 3           | 4                | 5             |
| ---------------- | -------- | ------ | ----------- | ---------------- | ------------- |
| Name             | register | cache  | main memory | solid-state disk | magnetic disk |
| Typical size     | < 1KB    | <16MB  | ~64GB       | ~1TB             | ~10TB         |
| Access time (ns) | 0.25-0.5 | 0.5-25 | 80-250      | 25000-50000      | 5,000,000     |

## I/O Device Access

- Old system
	- Busy waiting - CPU checks devices status periodically
![[Screenshot 2025-04-06 at 22.19.58.png]]

- Modern systems
	- **Interrupt-driven** I/O
	- **DMA (Direct Memory Access)** for large bulk of data
![[Screenshot 2025-04-06 at 22.17.46.png]]

#### Interrupt-driven I/O Cycle
![[Screenshot 2025-04-06 at 22.22.34.png]]

#### Direct Memory Access
- Device controller가 direct로 많은 양의 데이터를 메인 메모리로 전달
![[Screenshot 2025-04-06 at 22.23.25.png]]

# Computer System Architecture
## Single-Processor system
- Single general-purpose main CPU (single core)

## Multi-Processor system
- 두 개 이상의 프로세서가 긴밀한 의사소통 **tightly-coupled (강결합) system**
- 주로 multi-core system을 포함
	- **Core** - 명령어 실행과 데이터 저장을 위한 레지스터를 가진 component

#### Symmetric multiprocessing (SMP)

시스템의 모든 프로세서가 동등한 능력과 역할
![[Screenshot 2025-04-06 at 22.49.51.png]]
#### Asymmetric multiprocessing

Master-slave relation
Master가 slave를 schedule하거나 allocate함
![[Screenshot 2025-04-06 at 22.49.58.png]]

## NUMA System

##### **NUMA (Non-uniform memory access)**
> 각각 작고 빠른 로컬 BUS를 통해 자신만의 로컬 메모리에 접근하는 CPU그룹

자신의 로컬 메모리에 접근할 때는 빠르지만, **interconnect**된 다른 노드의 CPU가 접근할 때는 느리다.

## Multi-Processor System

- 장점
	- single-processor system에 비해 increased throughput (처리량 증가)
		- 병렬 처리로 성능 향상
	- clustered system에 비해 하드웨어 리소스 공유 측면에서 economy of scale

## Clustered System

> Multiple System이 같이 동작

- **Loosely coupled system** 느슨하게 결합된 시스템
- **SAN[^SAN] (Storage-Area Network)**를 통해 저장공간을 공유
- **DLM (Distributed Lock Manager)**를 사용하여 SAN에 여러 컴퓨터가 접속할 때 동시에 같은 파일 수정이 불가능하도록 함 (avoid conflicting operations)

#### Purpose of Clustered System
- High-availability 서비스를 제공 (안정성)
	- Asymmetric clustering은 하나의 기기가 **hot-stanby mode**로 대기 (두대 중에 한대는 비상 시 대기)
	- Symmetric clustring은 여러 노드가 서로를 주기적으로 모니터링 하면서 어플리케이션 실행 
- Increased Reliability
	- **Graceful degradation**: 클러스터의 일부 노드에 장애가 발생해도 서비스가 정상적으로 작동을 계속함.
	- **Fault tolerant**: 일부 장애가 발생하더라도 시스템을 계속 작동하는 속성

클러스터 시스템은 high-availability 뿐만 아니라 **HPC (High-Performance Computing)**을 위해서도 사용함 (병렬화 프로그래밍 기법)

# Operating System structure and operation

## Multiprogramming

##### Motivation
Single User는 CPU와 I/O 장치 각 작업 동안 반대쪽은 놀고 있음 -> 비효율적
![[Screenshot 2025-04-06 at 23.21.56.png]]
##### Multiprogramming
- CPU가 대기하는 상태를 최소화
- 시스템 내 전체 작업의 일부가 메모리에 유지
- #scheduling 을 통해 하나의 작업을 선택하여 실행
- 작업이 waiting 상태여야 할 경우 작업 switch

## Multitasking

#### Timesharing (multitasking)
- CPU가 작업을 빠르게 switch해서 유저가 각 작업 실행 동안 interact하게 만듬
- 각 사용자는 메모리에 최소 한개의 process를 가지고 있다.
- **CPU scheduler**가 실행할 준비된 작업을 선택
![[Screenshot 2025-04-06 at 23.33.30.png]]

## Virtual Memory

- 메인 메모리는 모든 프로세스를 담기에 충분하지 않다
	- **Swapping**: 실행을 위해 메모리 content를 들여오고 내보낸다.
	- **Virtual memory**: 프로세스의 일부만 메모리에 있어도 실행 허용

## #virtual_memory

> 가상 메모리의 핵심은 **추상화**이다. 디스크 공간을 사용해서 프로그램에게 contiguous(연속적인) 메모리 공간이 존재하는 것처럼 보이게 한다.

![[Screenshot 2025-04-07 at 00.04.39.png]]

## Operating System Operation

> Modern OS는 **interrupt-driven** program이다.

- Event는 interrupt에 의해 신호가 보내지고, **interrupt handler (ISR)** 에 의해 처리된다.
- `H/W와 S/W 리소스는 공유된다.` -> 하나의 프로세스에서 발생한 오류가 전체 시스템을 손상시킬 수 있다

> Multi-user OS는 한 프로그램이 다른 프로그램에 영향을 미치지 않아야 한다.

- 시스템을 건드리는 명령어는 #dual_mode 방식을 통해 커널 모드에서만 실행할 수 있도록 제한
- CPU에 대한 제어를 유지해야 한다.
	- #timer 를 통해 특정 프로세스가 CPU를 오랫동안 점유하는 것을 막아야 한다. (일정 간격으로 인터럽트를 발생시켜 프로세스 전환을 꾀함)

### Dual-mode operation

> User mode와 Kernel mode의 가장 큰 차이점은 **Privileged instruction**의 실행 가능 여부이다.

- User mode
	- Privileged instruction 같은 시스템을 손상시킬 수 있는 명령어의 실행이 금지된다
		- ex I/O, timer instruction
- Kernel mode (supervisor, system, privileged mode)
	- OS code
	- privileged instruction 허용

`Privileged instruction은 오직 OS의 System Call로만 실행할 수 있다.`

flag 값에 의해 mode가 제어된다.
mode bit가 1이면 user-mode, 0이면 kernel-mode

![[Screenshot 2025-04-07 at 00.16.42.png]]

## Timer #timer 

> 운영체제가 CPU에 대한 제어권을 유지하도록 보장해야 한다.

- **Timer**를 통해 제어 특정 시간이 지난 후에 interrupt를 발생시키도록 설정할 수 있다.
	- 타이머는 고정 값을 가질 수도, 가변적일 수도 있다.
	- variable timer는 **fixed-rate clock** 과 **counter**로 구현할 수 있다.
		- 운영체제가 카운터 설정
		- 클럭 틱마다 카운터 감소
		- 카운터가 0 되면 인터럽트 발생

당연히 timer는 privileged instruction으로만 수정할 수 있다

![[Screenshot 2025-04-07 at 00.46.40.png]]

## Multi-Mode Operation

- 인텔의 프로세서는 4개의 protection rings를 가지고 있다.
	- Ring 0: kernel mode
	- Ring 1 and 2: 다양한 운영 체제 서비스, 근데 거의 안씀
	- Ring 3: user mode
- ARMv8 시스템은 7개 모드를 가진다.
- virtualization을 지원하는 CPU는 **VMM (Virtual Machine Manager)** 가 시스템을 제어할 때를 나타내는 별도의 모드 존재
	- User process보다는 privileges를 가지고 있지만, kernel보다는 적다

# Core components of OS

## Process Management
### Process란
- 실행중인 프로그램. 즉, 필요한 자원을 할당받은 active program
- 싱글 스레드 프로세스는 **PC (Program Counter)** 를 가지고 있다.
- Unit of work

### Process와 비슷한 개념
- **Thread**: 한 프로그램이 자신을 동시에 실행되는 두개 이상의 작업으로 분리
	- CPU utilization의 기본 unit (process보다 작은)
	- 각 스레드는 ID, PC, register set, stack 등을 가진다.
	- `Major resources는 스레드 간에 공유된다`
- **Task**: 메모리에 로드된 프로그램 명령어들의 실행 흐름
	- 메모리에 로드된 program instruction의 집합
	- Linux같은 관점에서 task는 프로세스 혹은 프로세스와 스레드를 모두 포함하는 개념
### Process Management by OS
- 프로세스 생성과 삭제
- 프로세스 일시 중단과 재개
- 프로세스 동기화
![[Screenshot 2025-04-07 at 12.45.47.png]]
- 프로세스간 통신
- **Deadlock**[^deadlock] handling
![[Screenshot 2025-04-07 at 13.00.13.png]]

## Memory Management
> Main memory는 CPU가 직접 접근하여 데이터를 읽고 쓸 수 있는 유일한 저장장치이다.

### OS에 의한 메모리 관리
- 어떤 곳의 메모리를 누가 점유했는지 트래킹
- 메모리 공간을 할당 및 해제
- 어떤 프로세스와 데이터를 들여오고 내보낼지 결정 (swap-in, swap-out)

## Storage Management

OS는 물리적 저장장치를 논리적 **file**로 변환

### Device Driver
디바이스 드라이버는 **uniform interface**를 제공
![[Screenshot 2025-04-08 at 21.15.16.png]]

🧨`device driver는 소프트웨어이고, device controller는 하드웨어다.`

### File-system management
- 커널이 파일과 디렉토리의 생성과 삭제 관리
- 파일/디렉토리 조작의 기본 기능 지원
- 파일을 보조기억 장치에 매핑

### Mass-Storage management

운영체제는 대용량 저장장치를 관리하면서 **file**이라는 추상적인 개념을 구현

- 빈 공간 관리
- storage allocation
- disk scheduling
	- I/O 요청의 효율성 증가를 위해 디스크 헤드의 움직임을 최적화


## Caching
> 사용된 정보의 임시 복사본을 더 빠른 저장장소인 캐시에 저장하여 다시 사용할 때 빠르게 접근 (참조 지역성[^locality_of_references])

### Cache Coherence (캐시 일관성) 중요!!!!1
멀티 프로세서 환경은 가장 최신의 값을 가지도록 캐시 일관성을 제공해야 한다.

> Cache coherence란 공유 자원의 로컬 캐시에 저장된 데이터의 무결성(integrity of data)

![[Screenshot 2025-04-08 at 21.25.56.png]]
즉, 여러 CPU가 공유하는 자원 (like 메인 메모리)에 대해 각 CPU의 로컬 캐시에 저장된 데이터들이 일관되어야 한다는 것 -> 공유 데이터 자원을 변경하면 다른 CPU 캐시들도 변경사항을 반영

c의 `volatile` 키워드는 무조건 메인 메모리에만 쓰라는 캐시 금지 명령어 느낌스의 키워드

## Protection & Security
#### Protection
주인이 누군지 명시 (Authentification)
모든 리소스에 user_account 권한 관리

#### Security
다른 유저의 access를 허용당하는 것을 방지 (auth 탈취 방지)

![[Screenshot 2025-04-08 at 21.30.26.png]]


[^SAN]: consolidated(통합된), block level data storage를 제공하는 dedicated network 
[^deadlock]:교착상태
[^locality_of_references]: 참조지역성이란 프로세서가 간격에는 동일한 메모리 위치에 반복적으로 액세스 하는 경향이 있음을 뜻한다.










