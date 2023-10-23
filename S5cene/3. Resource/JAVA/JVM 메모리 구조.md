# JVM 메모리 구조
## 메서드영역, 힙(heap), 호출스택(call stack)
 + ### 메서드 영역: 클래스 사용시 해당 클래스 파일 분석후 저장
 + ### 힙(heap): 인스턴스가 생성되는 공간.
 + ### 호출스택: 메서드 작업에 필요한 메모리 공간 제공, 메서드 작업 끝나면 비워짐
```java
class CallStackTest{
	public static void main(String[] args){  // 1. main() 실행
		first();   // 2. main()에서 first() 호출
	}
	static void first(){  
		second();  // 3. first() 에서 second() 호출
	}
	static void second(){
		System.out.println("secondMethod");
	}
}
```

#### 호출스택이 main() 메서드를 실행하고 main()안의 first() 메서드가 실행되면 main()위에 first()가 올라고 main()은 대기상태로 남아있는다.<br>first()가 second()를 실행하면 first()는 대기상태로 남고 second()가 스택에 올라간다. second()는 println()을 실행하고 println()부터 쌓였던 순서대로 스택에서 비워진다.


