## 목차
1. [[#Network Limits and Compression]]
2. [[#The Human Visual System]]




## Network Limits and Compression

- ISDN (Integrated Services Digital Network)
- POTS (Plain Old Telephone Service)

![[Screenshot 2025-08-01 at 20.47.55.png]]

ITU-R Recommendations `BT.601`, `BT.709`, `BT.2020`

## The Human Visual System

인간의 시각 시스템 작동의 이해 중요성

- 시청자가 보는 것을 얼마나 인지하는지 설명
- 다양한 속성으로 정보를 표현
- HVS의 불완전성을 활용하여 subsampling


### HSV 특징

- 움직임과 구조에 더 민감하지만 색상 정보 손실에는 덜 민감
- 움직임 민감도는 질감 민감도보다 강함
- 질감 민감도는 시차(disparity)보다 강함
- 인지되는 휘도 (물체의 밝기)는 주변 휘도에 따라 달라짐
![[Pasted image 20250801210435.png]]


## The HVS Models

### The First Approximation Model

HVS가 linear, isotropic, time- and space-invariant (선형적, 등방성, 시공간 불변적) 이라고 가정

- 선형성: 물체에서 빛이 방출되는 강도가 증가하면 HVS 반응 크기도 비례적으로 증가
- 등방성: 방향에 대한 불변성. 실제 HVS는 anisotropic(이방성)이며 회전된 contrast grating(대비 격자)에 대한 반응이 주파수와 방향각도에 따라 달라지지만 이 모델에선 무시
- 시간적 반응은 간단한 모델에서는 고려x

First Approximation model에서 공간 주파수의 함수로서의 대비 민감도는 HVS의 OTF(Optical Transfer function)을 나타낸다. OTF의 크기는 MTF(Modulation transfer function) 이라고 불린다.

![[Screenshot 2025-08-01 at 21.33.35.png]]

HVS는 **중간 주파수에 가장 민감하고 높은 주파수에는 덜 민감하다** (band-pass 특성)

### Refined Model Including Nonlinearity

선형 모델은 비선형성을 무시하기 때문에 HVS에는 불충분

- 눈의 렌즈는 빛이 망막에 도달하기 전에 약간 흐릿하게 만든다 like 저역 통과 필터
- 우리 눈은 빛의 강도를 선형적으로 인식하지 않음 (로그 함수 관계) 어두울때는 작은 밝기 변화에도 민감 but 밝을 때는 훨씬 큰 변화 있어야 알아챔
- 망막에 돋라한 빛 신호는 주변의 빛 신호가 중앙의 신호를 억제 -> 밝기 변화가 급격한 경계선을 더 강조 (고역 통과 필터)

![[Screenshot 2025-08-01 at 22.39.39.png]]

### The Model Implications

고주파 세부사항을 흐릿하게 만든다 (양자화?)

### The Model Applications

![[Screenshot 2025-08-01 at 23.00.29.png]]

- Spatial Low-pass Filter
	- 눈 특성으로 인한 주파수 제한 (저역)
- Logarithmic Function
	- 빛의 강도를 비선형적으로 인지 (로그 곡선)
- High-pass filter
	- 이미지 경계를 강조
- Directional Filter Bank
	- 방향성 필터 뱅크
- Filter Bank for Stimulus Intensity


### Exploiting the HVS (HVS 활용)

인간 시각의 특성을 활용해서 손상과 데이터 압축 사이에서 tradeoff를 찾는

- **나이퀴스트 표본화 활용:** 대역폭을 제한함으로써, 시각 신호는 시각적 품질 손실 없이 대역폭의 두 배에 해당하는 주파수로 공간적 또는 시간적 차원에서 샘플링될 수 있으며, 이는 나이퀴스트 샘플링 기준을 충족합니다.
- **시간적 마스킹(Temporal Masking) 이용:** 빠른 대규모 장면 전환과 물체의 강렬한 움직임 동안 HVS의 민감도는 감소하는데, 이를 시간적 또는 **모션 마스킹(motion masking)**이라고 합니다. 이러한 경우, 강도의 시간적 불연속성으로 인해 가시성 역치가 높아집니다. 이를 활용하여 눈에 띄는 인공물(artifacts)을 생성하지 않으면서도 더 효율적인 압축을 달성할 수 있습니다.
- **질감 정보와 움직임 정보의 차별적 압축:** 질감 정보는 움직임 정보보다 시각적 품질 손실이 무시할 수 있을 정도로 더 많이 압축될 수 있습니다. 이 챕터의 후반부에서 논의되겠지만, 여러 손실 압축 알고리즘은 질감 정보의 양자화와 그로 인한 품질 손실을 허용하는 반면, 움직임 정보는 무손실로 인코딩합니다.
- **크로마 서브샘플링(Chroma Subsampling) 적용:** HVS가 색상 정보 손실에 대한 민감도가 낮기 때문에, **크로마 서브샘플링**은 시각적 품질에 큰 영향을 미치지 않고 데이터 전송률을 줄일 수 있는 실행 가능한 기술입니다.
- **고주파 정보 제거:** 밝기와 대비 정보의 압축은 고주파 정보를 버림으로써 달성될 수 있습니다. 이는 시각적 품질을 손상시키고 인공물을 도입할 수 있지만, 손실의 양에 대한 매개변수는 제어할 수 있습니다.
- **구조적 왜곡 측정:** HVS는 구조적 왜곡에 민감합니다. 따라서, 특히 이미지나 비디오와 같은 고도로 구조화된 데이터에 대해 이러한 왜곡을 측정하는 것은 왜곡의 양이 인간 시청자에게 허용 가능한지 평가하는 기준을 제공할 것입니다. 허용 가능성(acceptability)은 주관적이고 보편적이지 않지만, 구조적 왜곡 지표는 객관적인 평가 기준으로 사용될 수 있습니다.
- **관심 영역에 대한 차별적 압축:** HVS는 사람들이 복잡한 이미지의 흥미로운 부분에는 더 많은 주의를 기울이고 다른 부분에는 덜 주의를 기울이게 합니다. 따라서 이미지의 다른 부분에 서로 다른 양의 압축을 적용하여 전체적인 압축률을 높이는 것이 가능합니다. 예를 들어, 배경에 비해 전경 물체에 더 많은 비트를 할당함으로써 상당한 품질 저하 없이 압축을 높일 수 있습니다.


## An Overview of Compression Techniques

### Data Structures and Concepts

- 비트맵, 이미지, 프레임
	- 메모리에서의 시작 주소
	- 줄 당 픽셀 수
	- 피치 값
	- 프레임당 줄 수
	- 픽셀당 비트 수

#### 신호 및 샘플링

![[Screenshot 2025-08-02 at 00.04.54.png]]

연속된 신호를 일정한 간격으로 측정해 디지털화 하는 것을 **샘플링** 이라고 함

**푸리에 변환**으로 시간 신호를 주파수로 변환
![[Screenshot 2025-08-02 at 13.16.15.png]]


샘플링 할 때 샘플을 너무 띄엄띄엄 찍으면 원래 신호의 모습이 왜곡 (**앨리어싱**)

**나이퀴스트 샘플링 정의**- 신호의 가장 높은 주파수를 `f_max`라고 할 때 샘플링 주파수가 `2 * f_max`이상이어야 앨리어싱이 발생하지 않음

### Comment Terms and Notions

#### Aspect ratio (종횡비)
- DAR (Display): 4:3, 16:9 등
- PAR (Pixel): 보통 정사각형
- SAR (Storage): `SAR * PAR = DAR`

#### 색상
- RGB: 다른 색상을 1차 색상의 선형 조합으로 얻음
- CMYK: Cyan Magenta Yellow Key
- Color Gamut (색 재현율): 주어진 색상 공간에서 색상의 완전 부분집합

#### 루마와 크로마

- Luma: 이미지의 흑백 정보 (밝기)- RGB 신호로부터 계산
- Luminance(휘도): 어떤 방향으로 얼마나 밝은 빛이 나오는지 나타내는 물리량

사람이 밝기에 더 민감하다는 점을 이용해 **크로마(색차)** 를 서브 샘플링함

비디오에서는 RGB를 직접 보내는 대신 루마와 두 개의 색차 신호 Cb, Cr을 전송 

#### 색도(Chromaticity)와 ITU-R 권고안

- 색도
	- 색조(hue)- 색상 신호의 붉은 정도, 푸른 정도
	- 채도(saturation): 색상 신호가 회색으로부터 얼마나 다른지 정도

![[Screenshot 2025-08-02 at 14.15.02.png]]

