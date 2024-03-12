# Structure

```c
#include <stdio.h>
#include <string.h>

struct structName {
	int myInt;
	char myChar;
	char myString[30];
};
int main(){
	struct structName s1;
	s1.myInt = 1;
	s1.myChar = 'A';
	strcpy(s1.myString, "String String");
}
```

###### 구조체 선언 후에 구조체 변수에 값을 넣을 때 `String` 의 경우 `strcpy()`를 사용해야함
```c
int main(){
	struct structName s1 = {1, 'A', "String String"};
}
```
###### 구조체 사용할 때 한번에 변수 넣어주면 `strcpy`필요없음   

### Copy Struct
```c
struct myStruct s1 = {1,'A',"String String"};
struct myStruct s2;
s2 = s1;
```

### typedef
```c
#include <stdio.h>

typedef struct _Person { // 관례상 구조체 이름앞에 언더바 
	char name[20];
	int age;
	char address[100];
} Person;

int main(){
	Person p1;   // 구조체 별칭 
}
```