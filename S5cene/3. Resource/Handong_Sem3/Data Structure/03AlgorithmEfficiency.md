# Algorithm efficiency
## Time complexity:
##### 어떤 알고리즘의 실행시간에 대한 효율성을 평가하는 척도

```c++
// statement A
for(int i=0; i<n; i++) {
	for(int k=0; k<n; k++){
		// statement B
	}
}
for(int i=0; i<n; i++){
	// statement C
}
```
##### 위 코드에서 수행시간 f(n) = Bn^2 + Cn + A   ==> O(n^2)

### Big O notation:
##### "주어진 데이터의 규모 n에 대하여 어떤 함수의 형태로 실행시간이 결정되는가"
#### 큰 값의 n에 대한 고려이므로 다항식 최고차항의 지수만 표기

#### O(1) < O(log n) < O(n) < O(n log n) < O(n^2) < ... < O(2^n)

### Algorithm efficiency 분석의 필요성
+ ##### 알고리즘의 실용적 유용성 판단
+ ##### 데이터 양이 많아질 때 실행시간 예측
+ ##### 여러 알고리즘을 비교하여 최적의 선택 가능
+ ##### 알고리즘 개선요소 발견

