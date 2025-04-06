OS는 Kernel과 additional program 모음

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

Modern operating systems are **interrupt driven programs** 
