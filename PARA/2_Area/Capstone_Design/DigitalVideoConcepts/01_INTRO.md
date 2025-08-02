## 목차

1. 비디오 데이터 압축 (2장)
2. 데이터 압축 알고리즘과 표준 (3장)
3. 노이즈 (4장)
4. Visual Quality (4장)
5. Performance (5장)

## Video Compression Considerations 고려사항들

압축하지 않은 30fps의 FHD `1920x1080` 비디오는 초당 15억 비트정도의 데이터 전송률을 필요로 하는데, 이를 실제 인터넷 환경에서는 처리 불가능 -> 300:1정도로 압축 필요

한정된 데이터 전송률에서 가능한 한 최고의 visual quality를 제공하는 것을 목표로 함

- Varying Uses
	- 온라인 방식 캡쳐
	- 오프라인 방식 녹화
	- 비디오 신호 처리가 가능한한 빠르게 이루어지면 이점
- Conflicting Requirements
	- 압축으로 인한 손실 발생
	- 모바일 디바이스의 에너지 절약 문제
	- 상충하는 requirements에 대한 균형 (비디오 코딩 parameters) 필요
- Hardware vs. Software Implementations
	- ASIC (application-specific integrated circuits)
		- 특정 용도에 맞게 맞춤 제작
	- FPGA (field-programmable gate arrays)
		- 프로그래밍 가능한 logic blocks
	- GPT-based hardware acceleration
	- purely CPU-based software


## Tradeoff Analysis

- Benchmarks and Standards
- Challenges and Opportunities
	- 압축 디지털 비디오 수요 증가
	- 국제 비디오 코딩 표준의 다양화
	- 모바일 환경 제약
	- 처리 속도 향상에 대한 요구
	- 시각적 품질 달성
	- 상충관계의 체계적 분석 필요성
	- 비디오 코딩 솔루션의 평가

