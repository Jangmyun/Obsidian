# 데이터베이스 조작이 편해지는 ORM

## 데이터베이스

> 데이터를 효율적으로 보관하고 꺼내기

### DBMS (Database Management System)

> 데이터 베이스를 관리하기 위한 소프트웨어

데이터 베이스는 많은 사람들이 공유할 수 있어야 하기 때문에 **동시 접근**할 수 있어야 함

관리 특징에 따라 관계형, 객체-관계형, 도큐먼트형, 비관계형 등으로 분류

#### 관계형 RDBMS (Relational DBMS)
테이블 형태로 이루어진 데이터 저장소

##### H2, MySQL
+ H2: 스프링 부트가 지원하는 인메모리 RDBMS로 앱을 다시 실행하면 데이터는 초기화 됨. 개발 시 테스트 용도로 사용

#### DB용어
##### 테이블
DB에서 데이터를 구성하기 위한 가장 기본적인 단위
##### 행
테이블의 가로로 배열된 데이터 집합
행은 반드시 기본 키 (Primary Key) 가짐
##### 열
행에 저장되는 유형의 데이터
열은 각 요소에 대한 속성을 나타내고 **무결성을 보장**함
##### 기본 키 (Primary Key)
행을 구분할 수 있는 식별자
pk의 값은 테이블에서 유일해야 함. NULL은 될 수 없다.
##### 쿼리
DB에서 데이터를 조회, 삭제, 생성, 수정을 위해 하는 명령문

## ORM (Object-Relational mapping)
자바의 객체와 데이터베이스를 연결하는 프로그래밍 기법
ORM으로 DB의 값을 객체처럼 사용할 수 있다.

### JPA와 하이버네이트
자바에서는 JPA[^JPA]를 표준으로 사용함. 인터페이스 이므로 ORM 프레임워크를 추가로 사용해야 함.

ORM 프레임워크로 하이버네이트를 사용
JPA 인터페이스를 구현한 구현체로, 내부적으로 JDBC API 사용

### 엔티티 매니저

#### 엔티티
DB 테이블과 매핑되는 객체
DB 테이블과 직접 연결됨.

#### 엔티티 매니저
엔티티를 관리해 DB와 앱 사이에서 객체를 생성, 수정, 삭제하는 역할

엔티티 매니저를 만드는 곳이 **엔티티 매니저 팩토리**임

회원 2명이 동시 회원가입할 때 회원1 요청에 대해 가입 처리를 할 엔티티 매니저를 팩토리가 생성하면 이를 통해 가입 처리. 회원 2를 위해 생성된 앤티티 매니저도 필요한 시점에 DB와 연결한 뒤 쿼리

스프링 부트는 내부에서 엔티티 매니저 팩토리를 하나만 생성해서 관리
`@PersistenceContext`또는 `@Autowired` 를 사용해서 앤티티 매니저를 사용

스프링 부트는 기본적으로 빈을 하나만 생성하여 공유하므로 동시성 문제 발생할 수 있음. 실제로는 엔티티 매니저가 아니라 실제 엔티티 매니저와 연결하는 프록시 엔티티 매니저를 사용하여 필요할 때 실제 엔티티 매니저를 호출

### 영속성 컨텍스트
엔티티 매니저는 엔티티를 영속성 컨텍스트에 저장함
영속성 컨텍스트는 엔티티를 관리하는 가상 공간

#### 1차 캐시
- 키:  엔티티의 `@Id` 어노테이션이 달린 PK 역할을 하는 식별자
- 값: 엔티티
엔티티 조회할 때 1차 캐시에서 데이터가 있으면 반환하고 없으면 1차 캐시에 저장 후 반환
캐시된 데이터 조회할 때 DB를 거치지 않아 빠르다.

#### 쓰기 지연
트랜잭션을 커밋하기 전까지는 DB에 실제로 질의문을 보내지 않고 쿼리를 모았다가 커밋할 때 모았던 쿼리를 한번에 실행
DB 시스템 부담을 줄이기

#### 변경 감지
트랜잭션을 커밋하면 1차 캐시의 엔티티 값과 현재 엔티티 값을 비교해서 변경된 값 있으면 감지해 DB에 자동으로 반영

#### 지연 로딩
쿼리로 요청한 데이터를 앱에 바로 로딩하는 게 아니라 필요할 때 쿼리를 날려 데이터를 조회

위 특징들은 DB의 접근을 최소화해 성능향상


### 엔티티의 상태
엔티티는 네 개의 상태를 가진다.

1. 분리 detached: 영속성 컨텍스트가 관리하고 있지 않은 상태 
2. 관리 managed: 관리중인 상태
3. 비영속 transient: 영속성 컨텍스트와 상관없는 상태
4. 삭제 removed

엔티티를 처음 만들면 비영속 상태가 된다.

`persist()`메서드를 사용해 엔티티를 관리 상태로 만들 수 있다.

엔티티를 영속성 컨텍스트에서 관리하고 싶지 않으면 `detach()`메서드를 사용해 분리 상태로 만들 수 있다.

객체가 더 이상 필요없으면 `remove()`메서드사용해 영컨과 DB에서 삭제가능

## 스프링 데이터와 스프링 데이터 JPA
스프링 데이터는 비즈니스 로직에 더 집중할 수 있게 DB 사용기능을 클래스 레벨에서 추상화

CRUD나 자동으로 쿼리 만들기, 페이징 처리, 메서드 이름으로 자동 쿼리 빌딩 등 기능 제공
각 DB의 특성에 맞춰 기능을 확장해 제공

### 스프링 데이터  JPA
스프링 데이터의 공통적 기능에서 JPA 기술이 추가

스프링 데이터의 인터페이스인 `PagingAndSortingRepository`에서 상속받아 `JpaRepository`인터페이스를 만들었고, JPA를 더 편리하게 사용하는 메서드를 제공

기존에는 메서드 호출로 엔티티 상태를 바꿨지만
스프링 데이터 JPA를 사용하면 리포지토리 역할을 하는 인터페이스를 만들어 CRUD간단히 가능

```java
// MemberService.java

@Service  
public class MemberService {  
  
    @Autowired  
    MemberRepository memberRepository;  
  
    public void test() {  
        // create  
        memberRepository.save(new Member(1L, "A"));  
  
        // read  
        Optional<Member> member = memberRepository.findById(1L);  
        List<Member> allMembers = memberRepository.findAll();  
  
        // delete  
        memberRepository.deleteById(1L);  
    }  
}
```

### 예제 코드 살펴보기

#### `Member.java`

```java
// Member.java

// protected 접근 제어자
@NoArgsConstructor(access=AccessLevel.PROTECTED)
@AllArgsConstructor  
@Getter  
@Entity  
public class Member {  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "id", updatable = false)  
    private Long id;  
  
    @Column(name = "name", nullable = false)  
    private String name;  
}
```
##### `@Entity`
Member 객체를 JPA가 관리하는 엔티티로 지정
Member 클래스와 실제 DB 테이블을 매핑

`@Entity` 속성 중 name을 사용하면 name값을 가진 테이블 이름과 매핑, 사용 안하면 클래스 이름과 같은 이름의 테이블과 매핑

##### `protected`기본 생성자
엔티티는 반드시 기본 생성자가 있어야 하고 접근 제어자는 `public`또는 `protected`여야 한다.

##### `@Id`
Long타입의 id필드를 테이블의 pk로 지정

##### `@GeneratedValue`

pk 생성 방식을 결정.
###### 자동키 생성 설정 방식
- AUTO: 선택한 DB dialect에 따라 방식을 자동으로 선택
- IDENTITY: pk생성을 DB에 위임 (=AUTO_INCREMENT)
- SEQUENCE: DB 시퀀스를 사용해서 PK를 할당 (오라클)
- TABLE: 키 생성 테이블 사용

##### `@Column`
DB 컬럼과 필드를 매핑
###### 속성
- name: 필드와 매핑할 컬럼 이름, 설정 안하면 필드 이름
- nullable: null 허용 여부
- unique: 유일한 값인지 엽
- columnDefinition: 컬럼 정보 설정 (default값)

#### `MemberRepository.java`

```java
// MemberRepository.java

@Repository  
public interface MemberRepository extends JpaRepository<Member, Long> {  
}
```

리포지토리는 엔티티의 데이터들을 CRUD할 때 사용하는 인터페이스로, 스프링 데이터 JPA의 `JpaRepository`클래스를 상속받아 구현

`JpaRepository`상속받을 때 엔티티 클래스와 PK 타입을 파라미터로 넣어줌

[^JPA]:자바에서 관계형 데이터 베이스를 사용하는 방식을 정의한 인터페이스