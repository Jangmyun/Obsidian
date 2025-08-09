# H.264 비트 스트림 구조

## NAL (Network Abstraction Layer)

네트워크 친화적인 비디오 표현

**VCL (Video Coding Layer)** 의 비디오 표현을 여러 transport layer나 저장 매체에 적합한 방식으로 포맷하고 헤더 정보를 제공하도록 설계

### NAL Unit

코딩된 비디오 데이터는 NAL 유닛으로 구성

![[Pasted image 20250806151126.png]]

각각의 NAL Unit은 **Header**와 **Payload**로 구성

- **Header**: NAL 타입 (프레임, 파라미터 정보 등) 구분
- **Payload**: 실제 비디오 데이터 (slice, SPS, PPS) 등

각 Unit은 `0x000001` 시작 부호로 구분

전송 방식에 따라 시작부호는 생략될 수 있다 (byte stream foramt이 아닌 length-prefixed, packet-based)

##### NAL Unit 종류

- **SPS (Sequence Parameter Set)**
	- 해상도, 프로파일 등 시퀀스 전체 정보
- **PPS (Picture Parameter Set)**
	- 각 영상 프레임의 인코딩 방식 정보
- **IDR Slice**
	- 완전한 I 프레임
- **Non-IDR Slice**
	- P,B 인터 프레임
- 기타 보조정보 (RBSP)

![[Screenshot 2025-08-06 at 21.01.51.png]]

##### SODB (String Of Data Bits)

비트 단위의 데이터 시퀀스
비트 단위로 끊어지므로 **Byte-alginment** 보장 X

##### RBSP (Raw Byte Sequence Payload)

**SODB**가 Byte-alignment를 보장하지 않기 때문에 뒤에 stop bit `1` 과 필요한 만큼의 `0` padding으로 **Byte-alignment**를 맞춘다

##### EBSP (Encapsulated Byte Sequence Payload)

**Emulation Prevention Byte Sequence Payload** 라고도 불림

**RBSP**에서 `0x000000`이나 `0x000001` 같은 **NAL start code**와 혼동하지 않기 위해 특정 바이트 패턴이 나올 경우 `0x03`을 삽입해서 디코더의 혼동을 방지한다

이 과정을 **Emulation Prevention** 이라고 부른다.

정리하면 

> **EBSP** = **RBSP** + **Emulation prevention**
> **RBSP** = **SODB** + stop bit **1** + **0** paddings

![[Screenshot 2025-08-06 at 20.46.57.png]]

### Slice

H.264에서 하나의 이미지를 여러 서브 프레임으로 나눈 단위

![[Screenshot 2025-08-06 at 22.30.12.png]]

| Type | Slice    | Description                                  |
| ---- | -------- | -------------------------------------------- |
| 0,5  | P-slice  | P 매크로블록 (과거 ref 프레임 하나로 예측)<br>I-block 포함 가능 |
| 1,6  | B-slice  | B 매크로블록 (과거 미래 참조)<br>I-block 포함 가능          |
| 2,7  | I-slice  | 전부 I 매크로블록 (프레임 내부 예측)                       |
| 3,8  | SP-slice | P/I 블록 혼합, 스트림 스위치용                          |
| 4,9  | SI-slice | SI 매크로 블록 (스위치 전용), <br>정확한 스트림 전환 지원        |

타입 5-9의 경우 현재 프레임의 나머지 모든 슬라이스도 이 타입임을 의미한다.

#### 슬라이스 구조

- **Header**
	- 슬라이스의 타입 (I/P/B)
	- 어떤 매크로블록이 포함되는지
	- 슬라이스 번호 (프레임 내 위치)
	- 참조 프레임 정보, QP (quantification param)
- **Data**
	- 다수의 매크로블록의 실제 압축 데이터

### Macroblock

이미지 데이터 압축의 가장 작은 단위

H.264에서는 `16x16` + chroma 샘플로 구성

![[Screenshot 2025-08-06 at 22.32.04.png]]

#### 매크로블록 구조

- type (Intra, Inter)
- Prediction type
- **CPB** (Coding Block Pattern)
	- 어떤 블록이 실제 데이터 갖는지 마스킹
- **QP** (Quantification Parameter)
- DATA
	- Y, Cb, Cr 실제 계수값


디코딩 시 비트스트림에서 슬라이스-> 매크로블록 단위로 정보를 읽고

각 매크로블록의 YCbCr 데이터를 복원해 픽셀 컬러를 reconstruction



