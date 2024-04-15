# `useDelimiter()`  

#### useDelimiter()로 Scanner의 구분자를 space 에서 다른  문자로 변경하는 방법

##### `mm/dd/yyyy`의 형태로 입력을 받고자 한다.

```java
Scanner s = new Scanner(System.in);
s.useDelimiter("/"); // 구분자를 '/'로 설정

String month = s.next();
String day = s.next();
String year = s.next();
```

##### 구분자를 `"/"`로만 명시할 경우 입력이 `mm/dd/yyyy/`로 마지막에 `/`가 한번 더 나와야 입력이 멈추는 현상이 발생함.
### 👉해결법:
##### 구분자를 정규식 (Regular expression)으로 넣어주면 해결됨

##### `s.useDelimiter("[/\n]");`

