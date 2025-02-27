# 스프링 부트 3와 테스트

## 테스트 코드 개념 익히기

### 테스트 코드란
#### given-when-then 패턴
테스트 코드를 세 단계로 구분하여 작성
1. given: 테스트 실행을 준비하는 단계
2. when: 테스트를 진행하는 단계
3. then: 테스트 결과를 검증하는 단계

###### 예제코드
```java
@DisplayName("새로운 메뉴를 저장")
@Test
public void saveMenuTest() {
	// given: 메뉴를 저장하기 위한 준비 과정
	final String name = "아메리카노";
	final int priceWon = 2000;
	final Menu americano = new Menu(name,priceWon);

	// when: 실제로 메뉴를 저장
	final long savedId = menuService.save(americano);

	// then: 메뉴가 잘 추가되었는지 검증
	final Menu savedMenu = menuService.findById(savedId).get();
	assertThat(savedMenu.getName()).isEqualTo(Name);
	assertThat(savedMenu.getPrice()).isEqualTo(price);
}
```


## 스프링 부트 3와 테스트
`spring-boot-starter-test`스타터에 테스트를 위한 도구 있음
**JUnit**과 **AssertJ**를 가장 많이 사용

| 테스트 도구                         | 설명                            |
| ------------------------------ | ----------------------------- |
| JUnit                          | 자바용 단위 테스트 프레임워크              |
| Sprint Test & Spring Boot Test | 스프링 부트 앱을 위한 통합 테스트           |
| AssertJ                        | 어설션(검증문) 작성 라이브러리             |
| Hamcrest                       | Matcher 라이브러리 (표현식 이해 쉽게 만드는) |
| Mockito                        | 테스트용 가짜 목 객체 관리 프레임워크         |
| JSONassert                     | JSON용 어설션 라이브러리               |
| JsonPath                       | JSON 데이터에서 특정 데이터 선택, 검색      |

### JUnit
단위 테스트 프레임 워크: 테스트 할 때 작은 단위 (메서드)로 검증

```java
// src/test/main/JUnitTest.java

public class JUnitTest {  
    @DisplayName("1+1 = 3")  
    @Test  
    public void junitTest() {  
        int a = 1;  
        int b = 2;  
        int sum = 3;  
  
        Assertions.assertEquals(sum, a+b);  
    }  
}
```

#### JUnit의 어노테이션

테스트는 어노테이션에 따라 실행 순서가 정해진다.

- `@BeforeAll`: 시작하기 전에 한번만 실행
- `@BeforeEach`: 각 테스트 케이스를 시작하기 전마다 실행
- `@AfterAll`: 전체 테스트 종료하기 전에 한번만 실행
- `@AfterEach`: 각 테스트 케이스 종료하기 전마다 실행


##### 예제
```java
public class JUnitCycleTest {  
    @BeforeAll // 전체 테스트를 시작하기 전에 1회 실행 => static으로 선언  
    static void beforeAll() {  
        System.out.println("@BeforeAll");  
    }  
  
    @BeforeEach // 테스트 케이스를 시작하기 전마다 실행  
    public void beforeEach() {  
        System.out.println("@BeforeEach");  
    }  
  
    @Test  
    public void test1() {  
        System.out.println("test1");  
    }  
  
    @Test  
    public void test2() {  
        System.out.println("test2");  
    }  
  
    @AfterAll // 전체 테스트를 마치고 종료하기 전에 1회 실행 => static 
    static void afterAll() {  
        System.out.println("@AfterAll");  
    }  
  
    @AfterEach // 테스트 케이스를 종료하기 전마다 실행  
    public void afterEach() {  
        System.out.println("@AfterEach");  
    }  
}
```

##### 실행 결과
```cmd
@BeforeAll
@BeforeEach
test1
@AfterEach
@BeforeEach
test2
@AfterEach
@AfterAll
```


### AssertJ
#### AssertJ로 검증문 가독성 높이기

```java
Assertions.assertEquals(sum, a+b);

// AssertJ 예시
assertThat(a+b).isEqualTo(sum);
// a+b의 값이 sum과 같아야 한다.
```

#### AssertJ 자주 사용하는 메서드
| 메서드 이름               | 설명           |
| -------------------- | ------------ |
| `isEqualTo(A)`       | A값과 같은지 검증   |
| `isNotEqualTo(A)`    |              |
| `contains(A)`        | A값을 포함하는지 검증 |
| `doesNotContaint(A)` |              |
| `startsWith(A)`      | 접두사가 A인지 검증  |
| `endsWith(A)`        | 접미사가 A인지 검증  |
| `isEmpty()`          | 비어있는 값인지 검증  |
| `isNotEmpty()`       |              |
| `isPositive()`       | 양수인지 검증      |
| `isNegative()`       |              |
| `isGreaterThan(1)`   | 1보다 큰 값인지 검증 |
| `isLessThan(1)`      |              |

## 제대로 테스트 코드 작성해보기 
```java
// TestontrollerTest.java

@SpringBootTest // 테스트용 앱 컨텍스트 생성  
@AutoConfigureMockMvc // MockMvc 생성 및 자동 구성  
class TestControllerTest {  
  
    @Autowired  
    protected MockMvc mockMvc;  
  
    @Autowired  
    private WebApplicationContext context;  
  
    @Autowired  
    private MemberRepository memberRepository;  
  
    @BeforeEach  
    public void mockMvcSetUp() {  
        this.mockMvc = MockMvcBuilders.webAppContextSetup(context).build();  
    }  
  
    @AfterEach // 테스트 후 실행  
    public void cleanUp() {  
        memberRepository.deleteAll();  
    }  
}
```

##### `@SpringBootTest`

메인 앱 클래스에 추가하는 `@SpringBootApplication`이 있는 클래스를 찾고 그 클래스에 포함되어 있는 빈을 찾고 **테스트용 애플리케이션 컨텍스트**를 만듬

##### `@AutoConfigureMockMvc`

MockMvc를 생성하고 자동으로 구성
앱을 서버에 배포하지 않고도 테스트용 MVC 환경을 만들어 테스트

**컨트롤러를 테스트**할 때 사용

##### `@BeforeEach`
테스트를 실행하기 전에 실행하는 메서드에 적용

`MockMvcSetUp()` 메서드를 실행해 MockMvc를 설정

##### `@AfterEach`
테스트 실행한 이후에 실행하는 메서드에 적용

`cleanUp()`메서드를 실행해 member 테이블의 데이터를 모두 삭제


```java
// TestControllerTest.java

@DisplayName("getAllMembers: 아티클 조회에 성공한다.")  
@Test  
public void getAllMembers() throws Exception {  
    // given - 멤버를 저장  
    final String url = "/test";  
    Member savedMember = memberRepository.save(new Member(1L, "홍길동"));  
  
    // when - 멤버 리스트를 조회하는 API 호출  
    final ResultActions result = mockMvc.perform(get(url) // 1  
            .accept(MediaType.APPLICATION_JSON)); // 2  
  
    // then - 응답코드가 200ok고 반환받은 값 중에 0번째 요소의 id와 name이 저장된 값과 같은지 확인    result.andExpect(status().isOk())  
            .andExpect(jsonPath("$[0].id").value(savedMember.getId()))  
            .andExpect(jsonPath("$[0].name").value(savedMember.getName()));  
}
```

1. `perform()`메서드는 요청을 전송하는 역할, 결과로 `ResultActions`객체를 받고, `ResultActions`객체는 반환값을 검증하는 `andExpect()`메서드를 제공
2. `accept()`는 요청을 보낼 때 무슨 타입으로 받을지 결정하는 메서드. JSON 타입으로 받는다고 명시
3. `andExpect()`는 응답을 검증. `TestController`에서 만든 API는 OK(200)을 반환하므로 `isOk`로 응답 코드가 OK인지 확인
4. `jsonPath("$[0].${필드명}")`은 JSON 응답값의 값을 가져옴