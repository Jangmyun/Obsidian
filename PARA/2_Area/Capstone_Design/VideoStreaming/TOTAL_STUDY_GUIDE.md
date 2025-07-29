# 4주 스터디 플랜: 비디오 스트리밍, Computer Vision, PiWall

---

## 📅 Week 1~2: 비디오 스트리밍 & Computer Vision 기초

### ✅ Week 1: 비디오 스트리밍 개념 및 기술

#### 📌 Perplexity

- 비디오 파일 포맷 및 코덱: MP4, AVI, MKV / H.264, H.265, VP9
- 스트리밍 프로토콜: RTMP, HLS, DASH
- 실시간 스트리밍 vs VOD
- 버퍼링, 비트레이트 조절(Adaptive streaming)

#### 📌 Gemini

- 비디오 구조 이해: 픽셀, 해상도, 프레임레이트, 컬러 스페이스(YUV, RGB 등)
- 인코딩/디코딩: FFmpeg 실습 포함
- 스트리밍 프로토콜: RTMP, RTSP, HLS, DASH, WebRTC 개념
- 하드웨어 가속 개념 및 OpenGL/DirectX의 렌더링 역할

#### 📌 ChatGPT

- GStreamer 개요 및 `gst-launch` 실습
- OpenCV로 프레임 처리 및 색 변환 실습
- MJPEG, WebSocket 기반 실시간 스트리밍 예제

---

### ✅ Week 2: 영상 처리 및 디스플레이 기술

#### 📌 Perplexity

- FFmpeg로 영상 인코딩/디코딩
- OpenCV로 프레임 추출/재생
- Raspberry Pi에서 영상 디스플레이
- SDL, VLC, 하드웨어 가속 디코더 활용

#### 📌 Gemini

- OpenCV 설치 및 기본 영상 처리
- 필터 적용: 블러, 엣지 검출(Canny)
- 카메라 입력/프레임 읽기 및 표시
- 비디오 렌더링 파이프라인 이해: Capture → Decode → Process → Display

#### 📌 ChatGPT

- 영상 캡처 → 처리 → 인코딩 → 표시 흐름 정리
- SDL2 기반 디스플레이
- DRM/KMS 개요 및 Raspberry Pi 출력 장치 이해
- 멀티 디스플레이 환경 구성 원리 소개

---

## 📅 Week 3~4: PiWall 구조 분석 및 실습

### ✅ Week 3: PiWall 개념 및 구조 이해

#### 📌 Perplexity

- PiWall 개념: 다수의 Raspberry Pi로 구성한 비디오월
- 주요 컴포넌트: PiCaster(서버), PiSink(클라이언트)
- 소프트웨어 구조 및 네트워크 연동

#### 📌 Gemini

- PiWall 소프트웨어 구성 분석
- `pwomxplayer`, `piwall.conf` 구조 파악
- 화면 분할 원리와 스트리밍 동작 방식
- NTP 기반 시간 동기화 기초

#### 📌 ChatGPT

- PiWall 설치 실습 (Raspberry Pi OS, 패키지 설치)
- 디스플레이 노드 연결, 레이아웃 설정
- 프레임 싱크 개념 및 문제점 해결 방식

---

### ✅ Week 4: PiWall 실습 및 확장 아이디어

#### 📌 Perplexity

- PiWall 구축 실습: 여러 Pi에 비디오 분할 송출
- 동기화 및 품질 테스트
- OpenCV와의 결합 아이디어 탐구
- Video Wall 대체 솔루션과 비교

#### 📌 Gemini

- 실제 설치: 각 Pi에 클라이언트 설정 및 테스트
- 성능 최적화: Pi 4 사용, 해상도 조정 등
- 인터랙티브 비전 연동: 제스처/객체 인식 등 응용 제안

#### 📌 ChatGPT

- 다양한 레이아웃(2x2, 3x1 등) 실습
- 외부 소스 연동 테스트 (로컬 파일, 실시간 스트림)
- 확장 가능성 탐구: OmxPlayer, VLC Mosaic 비교

---

## 📚 참고 자료

- [FFmpeg Docs](https://ffmpeg.org/documentation.html)
- [OpenCV Docs](https://docs.opencv.org/)
- [GStreamer](https://gstreamer.freedesktop.org/)
- [PiWall GitHub](https://github.com/πwall/piwall) (혹은 아카이브: http://www.piwall.co.uk/)
- [Raspberry Pi Display Stack](https://www.raspberrypi.com/documentation/computers/display.html)
- YouTube 검색: "PiWall demo", "Video streaming explained", "OpenCV tutorial"
