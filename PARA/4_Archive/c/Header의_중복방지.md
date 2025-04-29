### `#ifndef`사용 패턴
```c
#ifndef EXAMPLE_H
#define EXAMPLE_H

typedef struct {
	int a;
	int b;
	...
}

#endif
```

### 왜 필요한가?

만약 `example.h`를 여러 파일에서 `#include`하고, 그 파일들이 또 서로를 `#include`하면 동일한 코드가 중복 포함되면서 **컴파일 에러**가 발생할 수 있다.

### 작동 흐름
1. 처음 `example.h`가 포함되었을 때:
	1. `EXAMPLE_H`가 정의되지 않았기 때문에 통과
	2. `#define EXAMPLE_H` 실행
2. 다음에 또 `#include "example.h"`하면
	1. `EXAMPLE_H`가 이미 정의돼있으므로 무시

