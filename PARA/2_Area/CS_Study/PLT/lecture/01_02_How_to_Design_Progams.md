
# Programming Languages

모든 언어는 다음으로 구성됨
- a peculiar syntax
	- 특유의 구문
- some behavior associated with each syntax
	- 각 구문과 관련된 특정 동작
- numerous useful libraries
	- 라이브러리
- a collection of idioms that programmer of that language use
	- 해당 언어의 프로그래머들이 사용하는 관용구들의 집합


### Syntax

- Syntax: How it looks like
- Semantic: how it behaves

Syntax에는 두가지 종류가 있다.

- **Concrete Syntax** (for people)
- **Abstract Syntax** (for computers)
 
![[Screenshot 2025-06-24 at 00.14.40.png]]

### Semantics
코드 조각이 어떤 의미를 가지며 어떻게 평가되는지를 정의

- Mathematical techniques
	- Denotational semantics
	- **Operational semantics**
	- Axiomatic semantics
- Interpreter semantics

### Programming language

programming language는 다음으로 정의된다.
- a grammar for programs (syntax)
- rules for evaluating any program to produce a result (semantics)

### A Grammar for Arithmetic Expression

#### A grammar of expressions in **BNF ([[Backus-Naur_Form]])**

![[Screenshot 2025-06-24 at 00.51.32.png]]
위 사진에서 expression을 만드는 방법을 세 가지로 정의하고 있다.

```bnf
num ::= "1" | "42" | "17" | ... number
```
`num`은 모든 숫자의 집합이다.
"7" ∈ `num`
"7" ∈ `expr`

```bnf
expr ::= "(" expr "+" expr ")" addition
		|"(" expr "-" expr ")" subtraction
		| num
```

"8" ∈ `num` ⊂ `expr` 이므로
"(8 + 8)" ∈ `expr`


## Type Definitions

![[Screenshot 2025-06-24 at 01.29.55.png]]

```scala
trait Shape
case class Triangle(a:Int, b:Int, c:Int) extends Shape
case class Rectangle(h:Int, w:Int) extends Shape
case class Square(side:Int) extends Shape
```

![[Screenshot 2025-06-24 at 02.55.21.png]]

# Pattern Matching

함수형 프로그래밍의 핵심 기능 중 하나로 복잡하지만 구조화된 데이터 처리에 도움

## Algebraic Data Types

단일 타입에 다양한 형태의 값을 포함한다.

- **Product type**: 특정 순서로 여러 타입의 값을 열거한(enumeration) 형태의 타입
- **Sum type**: tagged union type. 여러 타입의 값들을 자신의 값으로 가지는데 이때 각 component는 태그를 가지고 있어 다른 component와 구별될 수 있음


### 패턴 매칭을 위한 문법
![[Screenshot 2025-06-24 at 10.07.08.png]]

#### example
```scala
def perimeter(sh: Shape): Int = sh match {
  case Triangle(a, b, c) => a + b + c
  case Rectangle(h, w) =>  2 * (h + w)
  case Square(side) => 4 * side
}
```

```scala
def interp(expr: Expr): Int = expr match {
  case Num(num) => num
  case Add(l, r) => interp(l) + interp(r)
  case Sub(l, r) => interp(l) - interp(r)
}
```

## How to Design Programs

- Determine the data representation
	- `trait`, `case class`
- Write tests
	- `test`
- Create a template for the implementation
	- `match`
- Finish implementation case-by-case
- Run tests

## Test

```scala
println("Hello, world!")

error("message")

test(1, 1) // right의 값이 left와 같은가?
test(1, 0) // prints "FAIL: 1 is not equal to 0"s
```


## Lists

A list is either the constant `Nil` or it is a pair whose second value is a list

scala에서 list는 immutable하다. list 생성하는 방법은 두가지가 있다.
```scala
List([expr], [expr], ... , [expr])

//혹은

[expr] :: [expr] :: ... :: [expr] :: Nil
```

함수형 프로그래밍에서 index를 통해 element에 접근하는 건 드물고 대신 pattern matching을 많이 사용한다.

```scala
[expression] match {
	case Nil => [expression]
	case [name] :: [name] => [expression]
}
```

list가 비었으면 `Nil`case에 매칭된다.
아니면 nonempty list는 `[name] :: [name]` 케이스에 매칭되는데 첫번째 `[name]`은 리스트의 head를, 두번째는 tail을 의미한다.

```scala
// integer list l에서 
def headOrZero(l: List[Int]): Int = l match {
	case Nil => 0
	case h :: t => h
}
```

# Syntax

scala를 사용하여 코딩할 때 권장 금기 사항
- `break`문 사용하지 않기
- `while` 반복문 사용하지 않기
- mutation 사용하지 않기
이는 functional programming 스타일에 맞지 않음

```scala
def ... (expr: Expr): ... expr match {
	case Num(n) => ...
	case Add(l, r) => ...
}
```

pattern matching을 쓰지 않으면

```scala
def interp(expr: Expr): Int = 
	if(expr.isInstanceOf[Num])
		expr.asInstanceOf[Num].n
	else if (expr.isInstanceOf[Add]){
		val add = expr.asInstanceOf[Add]
		interp(add.left) + interp(add.right)
	}
	else {
		val sub = expr.asInstanceOf[Sub]
		interp(sub.left) - interp(sub.right)
	}
```
위와 같이 드러운 코드 작성해야 함 **(error-prone)**

## Type Checking

```scala
val expr = Add(Num(3), Sub(Num(8), Num(2)))
expr.left.num

// error: value num is not a member of Expr
```

compile time에서는 left의 type을 보장할 수 없음


## Arithmetic Expression

### Concrete Syntax

```bnf
expr ::= num
		| "(" expr "+" expr ")"
		| "(" expr "-" expr ")"
```

```bnf
expr ::= num
		| "{" "+" expr expr "}"
		| "{" "-" expr expr "}"
```


### Abstract Syntax

```scala
trait Expr
	case class Num(n: Int) extend Expr
	case class Add(l: Expr, r: Expr) extends Expr
	case class Sub(l: Expr, r: Expr) extends Expr
```

```scala
Add(Num(4), Num(2))
Add(Num(3) Sub(Num(8), Num(2)))
```

## Parser for Arithmetic Expressions

**Parser**: function인데 concrete syntax받아서 abstract syntax로 변환하는


# Semantics

### Operational Semantics
- A method of defining the meaning of programs by describing the actions carried out during a program's execution
- Many different styles
	- Evaluator semantics
	- Natural semantics, **big-step**
	- Reduction semantics, SOS, **small-step**
	- Abstract machine semantics

#### Operational Semantics for What?
- Specifying a programming language
- Communicating language design ideas
- Validating claims about languages
- Validating claims about type systems, etc
- Proving correctness of a compiler


## Abstract Syntax of `Expr`

#### 1
![[Screenshot 2025-06-24 at 15.19.13.png]]

#### 2
![[Screenshot 2025-06-24 at 15.19.52.png]]

##### Inference rules
![[Screenshot 2025-06-24 at 15.21.25.png]]

#### 3
![[Screenshot 2025-06-24 at 15.26.28.png]]

## Evaluator Semantics of `Expr`

## Natural Semantics of `Expr`

![[Screenshot 2025-06-24 at 15.40.14.png]]
- `⇒ ⊆ A × Z`
	- `⇒`는 해석 관계 (interp relation)을 나타낸다
	- `A x Z`는 어떤 입력 `expr ∈ A`이 정수로 해석되는 관계라는 뜻
- `⊢ 4 + (2 - 1) ⇒ 5`
	- `⊢`는 판단의 시작을 알리는 기호 ("전제 없이")
	- `4 + (2 - 1)`은 `expr` 표현식
	- `⇒ 5`는 표현식이 5로 평가된다는 뜻

![[Screenshot 2025-06-24 at 17.04.57.png]]

![[Screenshot 2025-06-24 at 17.09.25.png]]

![[Screenshot 2025-06-24 at 17.09.51.png]]

