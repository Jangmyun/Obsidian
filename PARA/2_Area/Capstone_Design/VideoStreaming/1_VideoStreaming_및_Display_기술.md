### Video Container Format

> 비디오, 오디오 자막, 챕터 정보 등의 데이터를 하나의 파일 안에 담는 container

`.mp4`, `.mkv`, `.avi` 등의 확장자

![[Pasted image 20250729204824.png]]

위 사진의 MKV 파일의 비디오는 x264코덱으로, 오디오는 DTS 코덱으로 만들어졌다. 포맷(확장자)는 컨테이너가 무엇인지 알 수 있으나 컨테이너 내부의 비디오와 오디오가 어떤 [[#Codec]]으로 만들어졌는지 알 수 없음

컨테이너에 포함되는 비디오, 오디오를 만드는 과정인 **인코딩**과 재생할 때의 **디코딩**을 [[#Codec]]이 담당

### Codec

> 비디오 데이터를 **Compress (압축)** 하고, **Decompress (해제)** 하는 기술

`H.264 (AVC)`, `H.265 (HEVC)`, `VP9 / AV1` 등

영상, 음성 데이터를 **RAW** 그대로 송출하거나 저장할 때 그 용량 때문에 부피를 줄일 필요성이 있음.

[코덱이란 무엇인가?](https://m.blog.naver.com/kky812/221068406920)
[비디오 코덱 (Video Codec)](https://velog.io/@dhwltnoooh/%EB%B9%84%EB%94%94%EC%98%A4-%EC%BD%94%EB%8D%B1)

---
코덱은 영상을 압축하는 기술, 컨테이너는 압축된 영상을 담는 상자
(MP4 상자에 H.264코덱으로 압축한 비디오와 AAC로 압축한 오디오가 담겨있는 식)


## 원시 비디오 데이터의 압축 및 디코딩 과정

[Video Codecs and Encoding: Everything You Should Know](https://www.wowza.com/blog/video-codecs-encoding)
[MPEG-4 비디오 압축](https://carstart.tistory.com/8)

### 인코딩

영상 데이터에서 중복되거나 중요하지 않은 정보를 제거하여 용량을 줄임

#### 공간적 압축

**Intra Coding**으로도 불리며, 한 프레임 내에서 압축이 진행됨.

1. **Subsampling**
	1. `RGB`를 `YCbCr`로 변환할 때 4:4:4 데이터를 4:2:0 등으로 변환하면서 데이터를 1/2로 압축
	2. 영상에서 색상 정보를 일부 손실 (Lossy)

2. **DCT(이산코사인변환)**
	1. `YCbCr` 데이터를 주파수 영역으로 변환
		1. Y: 밝기(명암) 정보
		2. Cb: 파란색 성분과 밝기 성분 사이의 차이
		3. Cr: 빨간색 성분과 밝기 성분 사이의 차이
		4. RGB에 비해 압축 효율이 높다

3. **Quantization(양자화)**
	1. DCT 과정의 결과에서 고주파 영역을 일정 레벨로 나누어 제거

![[Pasted image 20250730015829.png]]

#### 시간적 압축

**Inter Coding**으로도 불리며, 각 프레임 내에서 압축을 하는 공간적 압축이 아닌, 연속된 프레임 간의 공통점을 찾아내어 유사한 블록에 대해서는 압축을 수행하지 않고 방향 좌표만을 설정

**Motion Estimation**, **Motion Compensation** 개념을 기본으로 이루어짐

- **Motion Compensation** 움직임 보상
	- 연속된 영상의 두 프레임 차이가 물체가 움직인 결과라는 사실을 활용
- **Motion Estimation** 움직임 추정
	- 영상 프레임을 작은 블록으로 분할하여 앞뒤 프레임에서 어떤 변화가 있었는지 추정
	- Motion Compensation을 위해 Motion Vector를 찾는 기술


##### I/P/B-Frame

- **I-Frame** (Intra-coded Frame)
	- **GOP**의 기준이 되는 핵심 프레임으로, 입력 원본 그대로 저장된 프레임
- **P-Frame** (Predictable-coded Frame)
	- 바로 이전의 I-Frame을 기준으로 차이가 나는 부분의 데이터만을 예측하여 저장
	- I-Frame의 33% 용량
- **B-Frame** (Bidirectionally predictive-coded Frame)
	- I와 P프레임 사이에 껴서 양 프레임을 모두 참조
	- P-Frame의 33% 용량

![[Pasted image 20250729222747.png]]

##### I/P/B-Frame의 압축 과정
1. 원본 프레임 데이터를 기준으로 `I-Frame`생성
2. `I-Frame` 기준으로 이웃한 `P-Frame` 압축
3. `I-Frame`과 `P-Frame` 사이의 `B-Frame` 압축

![[Pasted image 20250730184448.png]]

#### GOP (Group of Pictures)

프레임들을 인코딩할 때 나열된 I/P/B-Frames들의 그룹

[GOP란?](https://tell-mia.tistory.com/46)

![[Pasted image 20250730012839.png]]

P,B 프레임은 I 프레임으로부터 생성되기 때문에 I 없이 P/B 부분 영상을 재생할 수 없음

영상 중간으로 이동했을 때 디코더가 I 프레임을 찾지 못하면 어디부터 영상을 복원해야 할지 알 수 없다. -> **GOP** 사용

**하나의 GOP 안에는 반드시 하나의 I-frame이 포함되어야 한다**

GOP 앞에 시쿼스 헤더가 붙어서 메타데이터 제공

### 디코딩

1. **GOP** 식별 및 접근을 위해 가장 가까운 I-Frame 찾기
2. I-Frame 디코딩
	- I-프레임은 다른 프레임을 참조하지 않기 때문에 공간적 압축 해제만으로 완벽한 RAW 데이터 복원
3. P-Frame 디코딩
	- 이전의 I-Frame 혹은 다른 P-Frame을 참조
	- 디코더는 P-Frame에 저장된 **Motion Vector**와 **Residual Data(차이 정보)** 를 사용
4. B-Frame 디코딩
	- 이전의 I/P-Frame과 이후의 I/P-Frame까지 디코딩 된 이후 양쪽에서 Motion Vector를 통해 픽셀 블록을 갖오고, B-Frame에 저장된 Residual Data를 합쳐서 B-Frame 이미지 생성
	- B-Frame 저장 순서와 재생 순서는 다를 수 있다 (참조할 프레임을 먼저 디코딩 해야하기 때문)

## 컬러 스페이스 (RGB, YUV) 및 크로마 서브샘플링

### 컬러 스페이스

색상을 표현하는 방식을 정의

- **RGB**
	- R(red), G(green), B(blue) 빛의 조합으로 모든 색을 표현
	- 화질손상이 적으나 용량이 크다
- **YUV (YCbCr)**
	- Y: 밝기(명암) 정보
	- Cb: 파란색 성분과 밝기 성분 사이의 차이
	- Cr: 빨간색 성분과 밝기 성분 사이의 차이
	- 인간이 생상 변화보다 밝기 변화에 민감하다는 점을 이용하여 색상 정보를 줄임

#### YCbCr <-> RGB 변환 공식
표준 BT.601 계수 사용

```
Y = 0.299R + 0.587G + 0.114B
Cb = 0.564(B - Y)
Cr = 0.713(R - Y)
```

```
R = Y + 1.402Cr
G = Y - 0.344Cb - 0.714Cr
B = Y + 1.772Cb
```

### 크로마 서브샘플링 (Chroma Subsampling)

**YUV** 컬러 스페이스의 장점을 극대화

밝기(Y) 정보는 유지하고 색상 (U, V) 정보의 해상도만 줄여서 데이터 용량 줄이기

#### 샘플링 비율에 따른 방식 (Y:U:V)

- **4:4:4**
	- 밝기 정보, 색상 정보 두개 각각 4개 픽셀 모두 사용
	- 색상 정보 손실 X, 용량 큼 (RGB와 동일한 비율로 저장)
- **4:2:2**
	- 밝기 정보는 유지하되 수평방향으로 색상 정보 절반으로 압축
- **4:2:0**
	- 수직 방향으로도 색상정보 절반으로 줄이기
	- 용량대비 효율 굿

![[Pasted image 20250730182753.png]]

[영상 레코딩 스펙](https://m.blog.naver.com/watneym/223078153133)
[위키피디아-크로마 서브샘플링](https://ko.wikipedia.org/wiki/%ED%81%AC%EB%A1%9C%EB%A7%88_%EC%84%9C%EB%B8%8C%EC%83%98%ED%94%8C%EB%A7%81#3:1:1)


## 프레임 레이트, 해상도, 비트레이트

### 프레임 레이트 (FPS)

1초동안 보여주는 정지 이미지 (Frame)의 개수

### 해상도

픽셀 총 개수 (가로 * 세로)

### 비트레이트

1초동안 처리되는 데이터의 양. `bps` (bits per second) 단위를 사용

베트레이트가 높을 수록 1초에 더 많은 데이터가 전달되므로 더 많은 원본 정보를 보존

#### 가변 비트레이트 (VBR) vs. 고정 비트레이트 (CBR)

- **VBR**: 영상의 복잡도에 따라 비트레이트를 유동적으로 조절
- **CBR**: 비트레이트를 항상 일정하게 유지하는 방식

[ITU-R Recommendation 표준](https://en.wikipedia.org/wiki/Rec._2020)

## 그래픽스 API



## 스트리밍 프로토콜

### RTSP (Real-Time Streaming Protocol)

스트리밍 서버와 클라이언트 간 미디어 스트림을 제어하기 위한 프로토콜

비디오 스트림 자체를 전송하는 게 아닌 스트림의 재생, 일시정지, 되감기, 중지 등을 제어하는 리모컨 역할

실제 데이터는 **RTP**와 **RTCP**를 통해 전송

HTTP대신 TCP/UDP 직접 사용으로 방화벽/네트워크 제약에 약함 (웹 직접 재생 어려움)

### RTMP (Real-Time Messaging Protocol)

어도비에서 개발 (플래시 기반)

실시간 저지연, TCP 기반, 안정적 스트림 전송

### HLS (HTTP Live Streaming)

애플에서 개발한 HTTP 기반 스트리밍 프로토콜

비디오를 세그먼트 (`.ts`)로 나누고, 이 세그먼트들의 목록이 담긴 `M3U8` 플레이리스트 파일을 제공

클라이언트는 이 플레이리스트를 읽어 순서대로 세그먼트를 다운로드하여 재생

**적응형 비트레이트(VBR)**를 지원

상대적으로 고지연

[우리가 스트리밍 영상을 보는 방법 - HLS가 뭐야?](https://brunch.co.kr/@musicman/3)

### DASH (Dynamic Adaptive Streaming over HTTP)

HLS와 유사한 HTTP 기반 적응형 스트리밍

영상을 다양한 비트레이트 세그먼트로 분할하고 MPD (메니페스트)로 관리


### WebRTC (Web Real-Time Communication)

실시간 오디오/비디오 및 데이터 P2P 통신

초저지연, 강력한 암호화

STUN/ICE 등으로 NAT 방화벽 통과

| 프로토콜   | 지연               | 확장성 | 주요 용도           | 장점             | 단점          |
| ------ | ---------------- | --- | --------------- | -------------- | ----------- |
| RTSP   | 2초               | 낮음  | 방송, CCTV 등      | 실시간성, 제어       | 호환성 낮음      |
| RTMP   | 3~5초             | 중간  | 서버 인입, SNS Live | 저지연, 인터랙티브     | 웹 재생 지원 약화  |
| HLS    | 6~30초/3초(LL-HLS) | 높음  | VOD, 라이브 전체     | 범용성, 브라우저 지원   | 높은 지연       |
| DASH   | 3~10초            | 높음  | 범용(OTT, VOD 등)  | 표준성, 적응형 스트림   | Apple 기기 제약 |
| WebRTC | 1초↓              | 낮음  | 화상회의, 라이브챗      | 초저지연, P2P, 암호화 | 대규모 송출 제약   |

[가장 많이 사용하는 스트리밍 프로토콜 6가지](https://dongtrivia.com/entry/%EA%B0%80%EC%9E%A5-%EB%A7%8E%EC%9D%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%8A%A4%ED%8A%B8%EB%A6%AC%EB%B0%8D-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C-6%EA%B0%80%EC%A7%80)


## 멀티캐스트 vs 유니캐스트

### Unicast

1대1 통신으로 하나의 송신자가 하나의 수신자에게 데이터를 전송

- 장점
	- 안정성, 제어 용이
- 단점
	- 네트워크 bandwidth 소모 (100대의 요청에 100번 전송)
	- 서버 부하 증가

### Multicast

1대 다 통신으로 하나의 송신자가 특정 **멀티캐스트 그룹**에 속한 여러 수신자에게 데이터 전송

- 장점
	- 네트워크 bandwidth 절약 (한번 전송으로 여러 클라이언트가 수신)
- 단점
	- 신뢰성 이슈
	- 멀티캐스트 지원하는 네트워크 인프라 (라우터, 스위치) 필요
	- 제어 어려움

## Latency & Bandwidth

### Latency

- 지연시간 최적화
	- 코덱 최적화
	- 스트리밍 프로토콜 선택
	- 버퍼링 최소화
	- 하드웨어 가속
	- 네트워크 환경 최적화
	- 짧은 GOP 길이 설정

### Bandwidth

- 대역폭 요구량 결정 요인
	- 해상도
	- 프레임레이트
	- 비트레이트
	- 코덱의 압축 효율
- 대역폭 최적화
	- 고효율 코덱 사용
	- 적응형 비트레이트 (ABR)
	- 네트워크 인프라 강화
	- [[#QoS (Quality of Service)]] 적용

## QoS (Quality of Service)

네트워크에서 데이터 패킷의 전송 우선순위를 제어하고, 특정 유형의 트래픽에 대해 미리 정의된 성능 수준을 보장하려는 기술&메커니즘

latency, packet loss, bandwidth constraint, jitter^[jitter] 등의 문제를 해결

### 스트리밍 서비스에서의 QoS 적용 방식

- 트래픽 분류 및 마킹
- 대역폭 관리
	- Queuing, Scheduling
	- Shaping, Policing
- 혼잡 회피 (Congestion Avoidance)




---

[^jitter]: 신호의 주기의 불안정성. 여기서는 패킷 도착 시간의 불규칙성

