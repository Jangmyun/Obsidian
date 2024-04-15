# Recursion
##### function body에 자기 자신을 call 하는 명령을 포함

#### 예제:
```cpp
// 1에서 n까지 정수 합
int sum (int n){
	if(n==1){
		return 1;
	}
	else {
		return (n + sum(n-1));
	}
}
``` 

```cpp
// Fibonacci
int fibonacci(int n){
	if(n==0){
		return 0;
	}
	if(n==1){
		return 1;
	}
	return (fibonacci(n-1)+ fibonacci(n-2))
}
```

