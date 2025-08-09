# Video Streaming

인터넷을 통해 동영상 콘텐츠를 별도 다운로드 없이 실시간으로 재생

## 스트리밍 기본 원리

1. 서버에서 영상을 작은 세그먼트로 나누어 전송
2. 클라이언틍에서 수신한 세그먼트를 버퍼에 저장
3. 버퍼에 충분한 데이터가 쌓이면 재생 시작
4. 재생 동안 추가 데이터 계속 수신하여 버퍼에 채워짐

### 버퍼링

영상 데이터를 임시로 저장

네트워크 속도가 불안정할 때 버퍼로 데이터를 미리 받아둬서 갑자기 데이터 전송 속도가 느려지더라도 재생이 끊기지 않도록 함

---


## RTSP (Real-Time Streaming Protocol)

실제 미디어 데이터는 전달하지 않으나 스트림의 **전송, 재생, 일시정지, 중지** 등 명령을 담당

데이터 전송은 **RTP(Real-time Transport Protocol)** 이 담당
**RTCP (Real-time Control Protocol)** 이 품질 및 동기화를 보조

### RTSP 동작 과정

![[Pasted image 20250808004455.png]]

#### 주요 명령

- `OPTIONS`: 지원 명령 목록 받기
- `DESCRIBE`: 스트림 정보(SDP 등) 요청
- `SETUP`: 세션/스트림 설정 및 전송방식 지정
- `PLAY`: 실시간 재생 시작
- `PAUSE`: 일시정지
- `TEARDOWN`: 세션 종료
- `GET_PARAMETER`/`SET_PARAMETER`: 파라미터 조회/변경
- `ANNOUNCE`, `RECORD`, `REDIRECT` 등도 지원

### 특징

- 실시간 저지연 스트리밍
- 양방향 통신 및 세션 관리
	- 클라이언트가 서버에 명령을 보내고, 접속 상태와 스트림 동기화, 속도 조절 등 다양한 동작을 제어
	- 서버와 클라이언트가 세션을 생성,관리 연결 상태를 지속적으로 추적
- 코덱/포맷 자유로움
- 네트워크 프로토콜
	- RTSP (제어채널)는 TCP, RTP (데이터 스트림)은 UDP 주로 사용
- 멀티플랫폼 및 상호운용성

### 장단점
| 장점                     | 단점                                  |
| ---------------------- | ----------------------------------- |
| 저지연·실시간 재생             | 브라우저 등 범용 지원 부족                     |
| 강력한 플레이어 제어(점프, 탐색 등)  | 네트워크 장벽(방화벽, NAT 등)에 취약             |
| 녹화·다중 스트림·동기화/멀티캐스트 지원 | 대규모 확장성·ABR(적응형 전송)에는 HLS/DASH보다 불리 |
| 높은 유연성(포맷, 코덱 자유로움)    | 암호화/복원력(재전송 등) 측면에선 한계가 있음          |
| 라이브 방송/감시/클로즈드 환경 최적화  | 모바일·PC 환경에서 앱 설치 등 추가 컴포넌트 필요       |

---


## RTP (Real-time Transport Protocol)

![[Pasted image 20250808011357.png]]

### Packet Structrue

![[Pasted image 20250808011625.png]]

- Version - RTP 프로토콜 버전
- Padding (P) - 패딩 사용 여부. 마지막에 추가 바이트 있을 경우 1
- Extension (X) - 확장 헤더 존재 여부
- CSRC Count (CC) -CSRC (기여자) 식별 수
- Marker (M) - 특정 이벤트, 프레임의 끝
- Payload Type (PT) - 페이로드(미디어) 타입 지정
- Sequence Number - 패킷 송신시마다 1씩 증가 -> 순서 복구 및 패킷손실 탐지 활용
- Timestamp - 해당 페이로드의 첫 샘플 시간 (동기화 및 재생 타이밍용)
- SSRC - 동기화 소스 식별자. 각 RTP 세션 내 고유
- CSRC List - 혼합된 RTP 페이로드의 기여자 식별

### 특징 

- 동기화 및 순서 보장
- 손실 허용
- 멀티캐스트
- 다양한 페이로드 타입 지원 (코덱 포맷 등)

---

## RTMP (Real-Time Messaging Protocol)

Adobe가 개발한 실시간 인터넷 스트리밍 프로토콜
Flash 기반 스트리밍에 최적화 되어 있으나 flash가 사라지고 HLS 등장하면서 RTMP는 단순 영상을 서버로 전달하는 용도로 사용


### Packet Structure

#### Header

헤더는 데이터 사이즈에 따라 1~3 Byte 유동적으로 결정

![[Pasted image 20250808015649.png]]


- Basic Header
	- Chunk Type - 이어지는 Chunk Message Header의 길이와 의미 결정
	- Stream ID - 동시에 여러 데이터 스트림을 구분하는 역할
- Chunk Message Header
	- Timestamp - 실시간 동기화, 프레임 재생 속도 제어
	- Message length - 전체 메시지 길이
	- Message Type ID - 메시지의 종류
	- Message Stream ID - 실제 메시지 스트림 구분용 ID
- Extended Timestamp - 타임스탬프가 `0xFFFFFF` 넘을 때 추가로 4 Bytes
- Payload - 실제 전송되는 데이터 (기본 128 Bytes)

### 스트리밍 과정

#### 스트리밍 서버 내 변환
- RTMP 인제스트
	- OBS, XSplit 등 인코더가 실시간으로 인코딩한 영상/음성 데이터를 RTMP 프로토콜을 통해 스트리밍 서버로 전송
- 서버 내부 변환 (**Transmuxing**)
	- 스트리밍 서버로 들어오는 RTMP 스트림을 받아 데이터를 HLS, DASH 같은 HTTP 기반의 포맷으로 실시간 변환

##### Transmuxing

미디어 데이터를 다시 인코딩하지 않고 전송 형식 (컨테이너 포맷)만 바꿔주는 과정

RTMP로 입력된 인코딩된 영상데이터를 HLS나 DASH에서 요구하는 MP4, TS 등으로 변환 하는데 코덱 자체는 두고 껍데기만 바꾸기

#### 클라이언트 재생

- 서버에서 변환된 HLS/DASH 파일들은 일반 웹서버 (HTTP) 로 제공
- 특수 플레이어나 전용 앱 없이 가능
- 인터넷 환경에 따라 적응형 스트리밍 전환 가능

---


## HTTP (Hypertext Transfer Protocol)

