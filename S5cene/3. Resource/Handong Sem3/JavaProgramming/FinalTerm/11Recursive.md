# 11. Recursive
### Recursion in algorithms
circular 처럼 보이지만 사실은 그렇지 않다.
결국 terminate 되어야 한다. 
재귀 알고리즘은 자기 자신과 또다른 subcases를 사용해서 문제를 해결한다.

### Recursive in Java
`recursive method`: 자기자신을 호출하는 메서드

##### "recursive 메서드는 적어도 하나의 base stopping case를 가지고 있어야 한다."

각각의 연속적인 호출은 자기자신의 smaller version이어야 한다. 
마지막에 base case에 도달해야한다.

example : phone book search
example : countdown
```java
public static void countDown(int num){
	if(num <= 0) System.out.println();
	else {
		System.out.print(num);
		coutDown(num-1);
	}
}
```

Display As Word 예제 이해하기

### Key to Successful Recursion

메서드 정의는 `if-else` 나 branching statement가 있어야 한다.
한 개 이상의 branches는 자기자신 메서드에 대한 재귀적 호출이 있어야 한다.
한 개 이상의 branches는 재귀적 호출이 없어야 한다. 해당 브랜치는 stopping case(base case)가 되어야 한다.

### infinite recursion
stopping case (base case)가 없는 재귀함수의 경우
stack overflow 발생하여 결국 crash된다.

### Recrusive vs. Iterative methods
"모든 재귀 알고리즘(메서드)는 recursion없이 구현할 수 있다."
재귀 없이 loop를 통해 해결하는 함수 : iterative methods

recursive 보다 iterative method가 더 빠르고 메모리 공간을 덜 사용한다.

##### recursion 써야 할 때
효율성이 중요하지 않은 경우/ 
코드를 이해하기 쉽게 만들 기 위해 

재귀호출하는 함수는 모두 같은 동작을 하는 메서드를 재귀 없이 rewritten 가능함
Non recursive 알고리즘은 iteration을 사용한다.

### recursive methods that `return a value`
한 개 이상의 branch 는 return value 를 하는 recursive invocation을 포함해야 한다. 


# Binary Search Algorithm

##### sequential search:
비효율적이지만 이해하기 쉽고 프로그래밍하기 쉽다.

##### Binary Search
sequential 보다 효율적이지만 리스트가 먼저 정렬된 상태여야 작동한다.

왜 Binary search?
- 매 시간 리스트를 읽어서 target value가 아니면 list절반을 날리고 다음 과정을 반복한다.
- 매 반복마다 리스트의 1/2 씩 줄어든다.

### Binary Search Code
```java
private int search (int target, int first, int last){
	int result = -1;
	int mid;
	if(first > last){
		result = -1;
	}else {
		mid = (first + last)/2

		if(target == a[mid]){
			result = mid;
		}else if(target< a[mid]){
			result = search(target, first, mid - 1);
		}else if(target > a[mid]){
			result = search(target, mid+1, last);
		}
	}
}
```

# Merge Sort
배열 원소가 하나라면 아무것도 하지 않는다. (base case)
배열원소가 두개 이상이면 다음을 반복한다
+ 첫 절반 원소를 `front`배열에 복사한다.
+ 남은 절반 원소를 `tail` 배열에 복사한다.
+ front와 tail을 recursive call로 정렬한다.
+ front와 tail을 병합시킨다.
![[Screenshot 2024-06-12 at 15.25.14.png]]
```java
public static void sort(int[] a){
	if(a.length >=2){
		int halfLength = a.length /2;
		int[] front = new int[halfLength];
		int[] tail = new int[a.length - halfLangth];
		divide(a, front, tail);
		sort(front);
		sort(tail);
		merge(a, front, tail);
	}
}

private static void divide(int[] a, int[] fristHalft, int[] lastHalf){
	for(int i=0; i< firstHalf.length; i++){
		firstHalf[i] = a[i];
	}
	for(int i=0; i< lastHalf.length; i++){
		lastHalf[i] = a[firstHalf.length + i];
	}
}

private static void merge(int[] a, int[] firstHalf, int[] lastHalf){
	int firstHalfIndex=0, lastHalfIndex=0, aIndex = 0;

	while((firstHalfIndex<firstHalf.length)&&(lastHalfIndex< lastHalf.length)){
		if(firstHalf[firstHalfIndex] < lastHalf[lastHalfIndex]){
			a[aIndex] = firstHalf[firstHalfIndex];
			firstHalfIndex++;
		}else {
			a[aIndex] = lastHalf[lastHalfIndex];
			lastHalfIndex++;
		}
		aIndex++;
	}

	while(firstHalfIndex < firstHalf.length){
		a[aIndex] = firstHalf[firstHalfIndex];
		aIndex++;
		firstHalfIndex++;
	}
	while(lastHalfIndex < lastHalf.length){
		a[aIndex] = lastHalf[lastHalfIndex];
		aIndex++;
		lastHalfIndex++;
	}
}
```