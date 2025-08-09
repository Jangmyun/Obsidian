# DCT (이산 코사인 변환)

이미지를 주파수 성분으로 바꿔주는 변환

- 영상의 residual 데이터나 픽셀 블록에 적용
- 원래 이미지 데이터를 주파수 coefficient로 변환
- 중요 정보를 몇개의 계수만으로 표현하고, 나머지 정보를 무시 가능해짐

`N*N` 크기의 샘플블록 `X`를 처리하여 `N*N` 크기의 계수 블록 `Y`를 생성
변환 행렬 `A`를 사용하여 DCT와 IDCT의 동작을 설명

$$
\begin{align}
\mathbf{Y} &= \mathbf{A} \mathbf{X} \mathbf{A}^\mathrm{T}  \\
\mathbf{X} &= \mathbf{A}^\mathrm{T} \mathbf{Y} \mathbf{A} 
\end{align}
$$

### 공식

변환 행렬 `A`의 요소 공식은 다음과 같다.

$$
A_{ij} = C_i \cos\left( \frac{(2j + 1)i\pi}{2N} \right) \quad \text{where} \quad 
C_i = 
\begin{cases}
\sqrt{\frac{1}{N}} & i = 0 \\
\sqrt{\frac{2}{N}} & i > 0
\end{cases}
$$

summation form으로 다시 쓰면
$$
Y_{xy} = C_x C_y \sum_{i=0}^{N-1} \sum_{j=0}^{N-1} X_{ij} 
\cos\left( \frac{(2j + 1)y\pi}{2N} \right)
\cos\left( \frac{(2i + 1)x\pi}{2N} \right)
$$
$$
X_{ij} = \sum_{x=0}^{N-1} \sum_{y=0}^{N-1} C_x C_y Y_{xy}
\cos\left( \frac{(2j + 1)y\pi}{2N} \right)
\cos\left( \frac{(2i + 1)x\pi}{2N} \right)
$$

##### 예시

`4x4` DCT의 A 행렬의 값은 다음과 같


![[Screenshot 2025-08-05 at 01.30.43.png]]

위 코사인 식을 풀면 아래와 같다.

![[Screenshot 2025-08-05 at 01.31.23.png]]



`4x4` DCT의 **basis pattern**은 다음과 같다.

![[Screenshot 2025-08-05 at 01.32.48.png]]

### DCT의 연산 순서

1. `Y' = AX`: `X`의 각 열에 대해 1차원 DCT를 계산하는 것과 동일
2. `Y=Y′A_T`: `Y'`의 각 행에 대해 1차원 DCT를 계산하는 것과 동일


### DCT 유용성

원본 이미지 블록을 DCT 계수 블록으로 변환한 후

![[Screenshot 2025-08-05 at 01.39.39.png]]

가장 중요한 계수인 **DC 계수** (0,0) 만 남기고 모든 계수를 0으로 설정한 후 IDCT 수행하면 원본 픽셀들의 평균값이 된다.

5개의 계수를 포함하면 IDCT 결과 블록은 원본과 근접하게 된다

16개의 계수 중 일부만으로도 이미지 블록의 근사한 복사본을 재구성할 수 있다.

![[Screenshot 2025-08-05 at 01.40.13.png]]

**Quantization**이 이 점을 이용해 중요도가 낮은 계수를 0으로 만들어서 데이터 양을 줄인다.

