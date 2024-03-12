# pointer
#### pointer : 다른 변수의 메모리 주소값을 저장하는 변수

```c
int main(){
	int n = 200;
	int* pointer = &n; // 포인터의 타입은 주솟값 저장하는 변수의 타입과 일치
}
```
##### 포인터의 값 (`pointer`) 자체는 포인터가 가리키는 변수의 주소를 담고 있고<br>포인터가 가리키는 주소의 값 은 (`*pointer`)로 찾을 수 있음

### Pointer & Arrays
```c
int numbers[] = {1,2,3,4};
int *ptr = numbers;

printf("%p", &numbers[0]);
printf("%p" , *ptr);
```