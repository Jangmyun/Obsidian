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







[^SAN]: consolidated(통합된), block level data storage를 제공하는 dedicated network 











