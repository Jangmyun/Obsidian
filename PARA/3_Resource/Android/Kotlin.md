# Kotlin 기초
## 변수 선언

- `val`: 값이 변하지 않는 변수 선언
- `var` 값이 변경될 수 있는 변수 선언

```kotlin
var count: Int = 10
count = 15 // 값 변경 가능

val languageName: String = "Kotlin"
```

## 타입 추론 (**Type Inference**)

위 `languageName`에 값을 할당할 때 Kotlin 컴파일러는 할당된 값의 타입을 기반으로 타입을 추론할 수 있다.

`val languageName = "Kotlin"` 표현도 가능하다.

## Null Safety

Kotlin 변수는 기본적으로 null 값을 가질 수 없다.

변수가 null값을 가지려면 `nullable` 타입이어야 한다.

타입 뒤에 `?`를 붙여 변수를 `nullable` 지정할 수 있다.

```kotlin
val languageName: String? = null
```

## 조건문

### 조건 표현식 (**contidional expression**)

```kotlin
val answerString: String = if (count == 42){
	"정답입니다."
} else if (count > 35) {
	"정답에 가깝습니다."
} else {
	"정답을 찾지 못했습니다."
}
```

### `when` 표현식

```kotlin
val answerString = when {
	count == 42 -> "정답입니다."
	count > 35 -> "정답에 가깝습니다."
	else -> "정답을 찾지 못했습니다."
}
```

### 스마트 캐스팅
nullable 값을 다룰 때 조건문을 사용해서 non-nullable로 처리할 수 있음

```kotlin
val languageName: String? = null
if (languageName != null) {
	println(languageName.toUpperCase())
}
```

조건 분기가 null 값을 포함하지 않는다는 것을 인식 가능


## 함수 선언의 단순화

```kotlin
fun generateAnswerString(countThreshold: Int): String {
	val answerString = if (count > countThreshold) {
		"정답입니다."
	} else {
		"정답을 찾지 못했습니다."
	}
	return answerString
}
```

단일 표현식 (single expression)의 결과가 함수에서 리턴될 때 `if-else` 표현식의 결과를 직접 리턴하여 지역변수 선언을 생략가능

```kotlin
fun generateAnswerString(countThreshold: Int): String = if (count > countThreshold) {
	"정답"
} else {
	"정답x"
}
```

## Anonymous function

```kotlin
val stringLengthFunc: (String) -> Int = { input ->
	input.length
}
```

```kotlin
val stringLengthFunc: (String) -> Int = { input ->
	input.length
}
val stringLength: Int = stringLengthFunc("Android")
```

## Higher-order functions

함수는 다른 함수를 파라미터로 받을 수 있다.

```kotlin
fun stringMapper(str: String, mapper: (String) -> Int): Int {
	return mapper(str)
}

stringMapper("Android", { input ->
	input.length
})
```

혹은 익명함수가 마지막 파라미터일 경우 함수 호출 바깥으로 전달할 수 있음

```kotlin
stringMapper("Android") { input ->
	input.length
}
```

## Classes

### Property

```kotlin
class Car {
	val wheels = listOf<Wheel>()
}
```
여기서 `wheels`는 `public val`임.

```kotlin
class Car(val wheels: List<Wheel>)
```

클래스 constructor는 `List<Wheel>`을 파라미터로 받고 `wheel`을 초기화한다.

### Class functions and encapsulation

```kotlin
class Car(val wheels: List<Wheel>) {
	private val doorLock: DoorLock = ...
	fun unlockDoor(key: Key): Boolean {
		// key가 유효하면 true, 아니면 false 리턴
	}
}
```

`doorLock`은 클래스 외부에서 접근 금지임. `unlockDoor()` 호출을 통해 제어할 수 있음

```kotlin
class Car(val wheels: List<Wheel>) {
	private val doorLock: DoorLock = ...

	var gallonsOfFuelInTank: Int = 15
		private set // private setter

	fun unlockDoor(key: Key): Boolean {
		// key가 유효하면 true, 아니면 false 리턴
	}
}
```

`private set`을 통해 private setter을 지정할 수 있음

# Work with Fragment

안드로이드 개발에 Kotlin 사용하는 일반적인 패턴

## Inheritance

```kotlin
class LoginFragment : Fragment()
```

`:` 콜론 연산자를 사용해서 상속을 나타낼 수 있는데, 자식 클래스 선언에서 부모 클래스의 생성자를 호출하여 상속

```kotlin
override fun onCreateView(
		inflater: LayoutInflater,
		container: ViewGroup?,
		savedInstanceState: Bundle?): View? {
	return inflater.inflate(R.layout.login_fragment, container, false)	
}
```

`override`키워드를 사용하여 부모 클래스의 함수를 오버라이드 할 수 있다.

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
	super.onViewCreate(view, savedInstanceState)
}
```

`super` 키워드를 사용하여 부모 클래스의 함수를 참조할 수 있다.

### Nullability and Initialization

메서드의 일부 매개변수의 타입 뒤에 `?`가 붙어 nullable임을 표시한다.
이런 매개변수들은 **nullability**를 안전하게 처리해야 한다.

Kotlin에서는 객체 property를 선언할 때 initialize 해야한다.
(클래스의 인스턴스를 획득하면 즉시 접근 가능한 모든 property를 참조할 수 있다.)

`Fragment`의 View 객체의 경우에는 `Fragment#onCreateView`를 호출하기 전까지는 inplate 준비되지 않았으므로 초기화를 지연시켜야 한다.

`lateinit`을 통해 property의 초기화를 지연시킬 수 있다.

```kotlin
class LoginFragment : Fragment() {
    private lateinit var usernameEditText: EditText
    private lateinit var passwordEditText: EditText
    private lateinit var loginButton: Button
    private lateinit var statusTextView: TextView

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        usernameEditText = view.findViewById(R.id.username_edit_text)
        passwordEditText = view.findViewById(R.id.password_edit_text)
        loginButton = view.findViewById(R.id.login_button)
        statusTextView = view.findViewById(R.id.status_text_view)
    }
    // ...
}
```

property가 initialize되기 전에 접근하면 `UninitializedPropertyAccessException`을 발생시킨다.

## SAM conversion (Single Abstract Method)

동일한 Single Abstract Method를 가지는 `OnClickListener`를 파라미터로 받는 `setOnClickListener()`의 경우 익명함수를 사용해서 나타낼 수 있다.

```kotlin
loginButton.setOnClickListener {
    val authSuccessful: Boolean = viewModel.authenticate(
            usernameEditText.text.toString(),
            passwordEditText.text.toString()
    )
    if (authSuccessful) {
        // 다음 화면으로 이동
    } else {
        statusTextView.text = requireContext().getString(R.string.auth_failed)
    }
}
```

`setOnClickListener()`에 전달된 익명 함수는 loginButton 클릭 시 실행된다.

## Companion Object

`Companion object`는 