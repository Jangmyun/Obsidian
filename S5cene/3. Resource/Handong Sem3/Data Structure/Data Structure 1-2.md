# DS 1-2

## Algorithm efficiency
### 적용 데이터의 규모가 n일 때 n이 어떤 형태의 함수만큼 증가하는가
### 큰 값의 n을 고려

### Time Complexity 시간복잡도 : 어떤 알고리즘의 실행시간에 대한 효율성을 평가하는 척도
```cpp
statement-a
for(i=0 i<n; i++){
	for(k=0; k<n; k++){
		statement-b
	}
}
for(i=0; i<n; i++){
	statement-c
}
```
#### 수행시간 = f(n) = O(n^2) 
##### 최고 차항의 지수만 표기

### Big-O notation
###### ex) O(n) O(n^2) ...

#### O(1) - n값 상관없이 항상 일정 시간에 완료하는 경우
#### O(log(n)) - log함수에 비례하여 증가
```cpp
for (k=1; k<n; k*=2){
	state
}// log n
```
```cpp
for(i=0; i<n; i+=2){
	// n/2 == O(n)
	for(j=n; j>1; j=j/2 ){
		// log n
	}
} // n*log n

```

#### O(1) > O(log(n)) > O(n) > O(nlog(n)) > O(n^2) > .... > O(2^n)


## Stacks
### Last In First Out (LIFO) 특성을 갖는 linear list
#### 꺼낼 때 마지막에 넣은거 꺼냄

### stack에 적용하는 2가지 연산
#### Push: stack에 한개 원소 저장
#### Pop: stack으로부터 한개 원소를 취함

### 구현 형식:
#### linear list 의 한쪽 끝 (Top)에서 push와 pop 이루어짐

### stack 관련 연산
#### Create-stack: stack 생성
#### Push: stack에 원소 추가
#### Pop: stack으로부터 원소한개 가져옴
#### Full_check: 스택 꽉찼는지 체크
#### Empty_check: 스택 비었는지 체크

### stack의 쓰임 
#### backtracking  미로찾기

## ADT (Abstract Data Type)
### Data type 
#### Data type : 데이터가 어떤 형태인가, 데이터에 적용되는 연산의 종류
### Data structure
#### 데이터를 어떤 규칙에 의해 모아 놓은 것 
### Abstraction (추상화)
#### Data type의 사용법 명시하고 내부적인 구현 내용은 숨기는 개념 (알필요 없는거 숨기기)