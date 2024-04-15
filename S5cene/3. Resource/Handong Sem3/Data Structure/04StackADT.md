# Stack :
##### Last In First Out (LIFO) 특성을 갖는 linear list

##### Linear list의 한쪽 끝 위치 (Top)에서 Push Pop 이루어짐

#### create_stack
```cpp
void Stack::create_stack (){
	int my_stack[SIZE];
	int top = 0;
}
```


#### push
```cpp
void push(int a) {
	if (top >= SIZE){
		// error 처리
	}
	my_stack[top] = a;
	top++;
}
```

#### pop
```cpp
int pop(){
	if(top==0){
		//error 처리
	}
	top--;
	return(my_stack[top]);
}
```

