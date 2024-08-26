```MySQL
SELECT * From tableName;
```

##### SELECT =>  명령의 종류
##### * = 모든 열

#### `DESC tableName`
##### table 에 어떤 Column이 정의되어 있는지 확인

#### 자료형
+ INTEGER
+ CHAR() - 문자열
+ VARCHAR() - 가변 길이 문자열
+ DATE
+ TIME

### 검색 조건 지정
```sql
SELECT column1, column2 FROM tableName WHERE 조건식
```

##### select 문에서 열 지정, WHERE 구에서 조건식을 통한 행 지정

```sql
SELECT column1 FROM tableName WHERE id=2
```


