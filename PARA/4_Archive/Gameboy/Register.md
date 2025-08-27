레지스터는 두 종류로 나눌 수 있다.
+ general-purpose register
+ special-purpose register

### General-purpose register (범용 레지스터)
범용 레지스터는 연산 결과를 임시로 저장하거나 데이터 주소를 지정하는 등 말그대로 범용적으로 사용되는 레지스터다.

게임보이의 범용 레지스터는 8bit로 , `a`, `b`, `c`, `d`, `e`, `h`, `l`이 있다.
8bit이므로 정수의 경우 0부터 255까지 표현이 가능하다.

`a` 는 **accumulator**(누산기)로 데이터 전송 및 연산 후의 결과값을 잠시 저장한다.

`a`를 제외한 다른 레지스터는 두개가 하나로 묶여서 `bc`, `de`, `hl` 처럼 16비트 레지스터처럼 사용할 수 있다. 




## System V Shared-memory

### shared memory 생성

`int shmget(key_t key, int size, int shmflg);`

ex > `seg_id = shmget(IPC_PRIVATE, size, S_IRUSR|S_IWUSR);`

- `IPC_PRIVATE`: 새로운 고유 공유 메모리 segment 생성
- `size`: byte 단위의 세그먼트 크기
- `shmflg`: 공유 메모리 세그 권한, 플래그
	- `S_IRUSR`: 유저 읽기
	- `S_IWUSR`: 유저 쓰기

### shared memory를 프로세스 주소 공간에 연결

`void* shmat(int shmid, char *shmaddr, int shmflg);`

ex > `shared_mem = (char *) shmat(seg_id, NULL, 0);`

- `shmid`: `shmget()`리턴값
- `*shmaddr`: 공유 메모리 segment연결할 주소공간의 시작주소
	- `NULL`: OS가 자동으로 선택
- `shmflg`: 연결 옵션
	- `0`: 읽기 및 쓰기

### 연결된 주소로 공유 메모리 사용

ex> `spinrtf(shared_mem, "Writing to shared memory");`

### 공유 메모리를 프로세스 주소공간에서 분리

`int shmdt(char *shmaddr);`

ex> `shmdt(shared_mem);`

- `shmaddr`: 분리할 공유 메모리가 연결된 프로세스 주소 공간의 시작주소
- `shm_nattach--`: 공유 메모리에 연결된 프로세스 수 감소
	- 모든 프로세스가 detach 됨녀 공유 메모리 폐기

### 공유 메모리 할당 해제

`int shmctl(int shmid, int cmd, struct shmid_ds *buf);`

ex> `shmctl(shmid, IPC_RMID, NULL)`

- `shmid`: 제어할 공유 메모리 seg 식별자
- `cmd`: 수행할 명령
	- `IPC_RMID`: 공유 메모리 세그먼트 제거 (실제 제거는 shm_nattach가 0됐을 때)
- `*buf`: 공유 메모리 상태 정보 저장, 설정하는 shmid_ds 포인터


## POSIX Shared Memory

### 공유 메모리 생성 혹은 열기

`int shm_open(const char *name, int oflag, mode_t mode);`

ex> `shm_fd = shm_open("OS", O_CREAT | O_RDWR, 0666);`

- `oflag`:  플래그
	- `O_CREAT`: 지정된 이름의 메모리 객체가 없으면 새로 생성
	- `O_RDWR`: 읽기 쓰기 권한
- `mode`: 파일 권한 설정
	- `0666`: 모든 사용자에게 읽기 및 쓰기 권한

### 공유 메모리 객체 크기 설정

`ftruncate(int fd, off_t length);`

파일 디스크립터 `fd`로 참조되는 공유 메모리 크기를 `length`로 설정

ex> `ftruncate(shm_fd, 4096);`

### 공유 메모리 세그먼트를 프로세스 주소 공간에 매핑

`void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset)`

- `addr`: 매핑 시작 주소
- `length`: 매핑할 바이트 수
- `prot`: 메모리 보호 속성
- `flags`: 매핑 유형
	- `MAP_SHARED`: 다른 프로세스와 공유
- `fd`: 공유 메모리 파일 디스크립터
- `offset`: 매핑 시작 위치 오프셋

예시> `shared_memory = mmap(0, 4096, PROT_WIRTE, MAP_SHARED, shm_fd, 0);`

### 공유 메모리 사용
`sprintf(shared_memory, "Writing to shared memory");`

### 공유 메모리 매핑 해제

`int munmap(void *addr, size_t length)`: 공유 메모리 해제

- `addr`: `mmap`이 리턴한 값
- `length`: 해제할 바이트

### 공유 메모리 객체 삭제

`int shm_unlink(const char *name)`

- `name`: `shm_open`에 사용된 이름

마지막 매핑이 해제될 때 실제로 메모리가 해제
모든 프로세스가 `munmap()`호출하면 커널이 자동으로 삭제


## POSIX Message Passing

