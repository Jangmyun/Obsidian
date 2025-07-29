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

#### GOP (Group of Pictures)

프레임들을 인코딩할 때 나열된 I/P/B-Frames들의 그룹

[GOP란?](https://tell-mia.tistory.com/46)

![[Pasted image 20250730012839.png]]

P,B 프레임은 I 프레임으로부터 생성되기 때문에 I 없이 P/B 부분 영상을 재생할 수 없음

영상 중간으로 이동했을 때 디코더가 I 프레임을 찾지 못하면 어디부터 영상을 복원해야 할지 알 수 없다. -> **GOP** 사용

**하나의 GOP 안에는 반드시 하나의 I-frame이 포함되어야 한다**

GOP 앞에 시쿼스 헤더가 붙어서 메타데이터 제공

