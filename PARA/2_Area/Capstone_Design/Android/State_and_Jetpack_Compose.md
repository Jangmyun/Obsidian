## State and composition

Compose는 **declarative (선언적)** 이므로 새로운 파라미터를 사용해 Composable을 호출하여 업데이트해야한다.
state가 변경될 때마다 **recomposition**이 발생한다.


```kotlin
@Composable
private fun HelloContent() {
    Column(modifier = Modifier.padding(16.dp)) {
        Text(
            text = "Hello!",
            modifier = Modifier.padding(bottom = 8.dp),
            style = MaterialTheme.typography.bodyMedium
        )
        OutlinedTextField(
            value = "", // 이 값이 변경되어야 텍스트 필드가 업데이트됩니다.
            onValueChange = { }, // 입력 이벤트가 발생해도 이 람다가 현재 value를 변경하지 않습니다.
            label = { Text("Name") }
        )
    }
}
```

`TextField`같은 요소는 스스로 업데이트되지 않기 때문에 새로운 state를 명시적으로 전달받아야 한다.


#### Key Term
- **Initial composition**: Composable을 처음 실행해서 Composition을 생성
- **Recomposition**: 데이터가 변경될 때 Composition을 업데이트하기 위해 Composable을 재실행

## State in composables

Composable 함수는 `remember` API 를 사용해서 메모리에 객체를 저장할 수 있다.

`remember`에 의해 계산된 값은 initial composition 중에 composition에 저장되고, recomposition 중에는 저장된 값이 리턴된다.

> `remember`는 객체를 Composition에 저장하고, `remember`를 호출한 Composable이  Composition에서 제거될 때 객체를 잊어버린다.

```kotlin
interface MutableState<T> : State<T> {
    override var value: T
}
```

`mutableStateOf`는 observable한 타입인 `MutableState<T>`를 생성한다.

#### Composable에서 `MutableState` 객체를 선언하는 방법

- `val mutableState = remember { mutableStateOf(default) }`
- `var value by remember { mutableStateOf(default) }`
- `val (value, setValue) = remember { mutableStateOf(default) }`

`by` delegate 구문은 
```kotlin
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue
```
import를 필요로 한다.

remembered value는 다른 Composable의 매개변수로 사용되거나, 어떤 Composable을 표시할지 여부를 변경하는 로직으로 사용할 수 있다.

```kotlin
@Composable
fun HelloContent() {
    Column(modifier = Modifier.padding(16.dp)) {
        var name by remember { mutableStateOf("") } // 'name' 상태를 선언
        if (name.isNotEmpty()) { // 'name' 상태에 따라 조건부로 텍스트 표시
            Text(
                text = "Hello, $name!",
                modifier = Modifier.padding(bottom = 8.dp),
                style = MaterialTheme.typography.bodyMedium
            )
        }
        OutlinedTextField(
            value = name, // 'name' 상태를 TextField의 값으로 연결
            onValueChange = { name = it }, // TextField의 값 변경 시 'name' 상태 업데이트
            label = { Text("Name") }
        )
    }
}
```

`name` 문자열이 비어있지 않다면 조건부로 텍스트를 표시하도록 할 수 있다.

`remember`는 **configuration change** 시에는 상태가 유지되지 않기 때문에 `rememberSaveable`을 사용하여 `Bundle`에 저장될 수 있는 모든 값을 자동으로 저장할 수 있다.

> `ArrayList<T>`나 `mutableListOf()` 같이  mutable 객체를 상태로 사용하면 non-observable하기 때문에 recomposition 되지 않는다. 대신 `State<List<T>>`나 `listOf()`같은 observable 데이터 홀더를 사용해야 함


## Other supported  types of state

`MutableState<T>` 외에도 다른 observable한 타입도 지원하는데, Composable이 자동으로 recompose 될 수 있도록 `State<T>`로 변환해야 한다.

Compose에서 observable 타입으로부터 `State<T>`를 생성하는 함수를 제공하는데, 사용하기 전에 artifact를 추가해야 한다.

- `Flow`: `collectAsStateWithLifecycle()`은 `Flow`에서 값을 수집하여 라이프사이클을 인식하는 방식으로, 앱 리소스를 절약한다.

```kotlin
dependencies {
      ...
	implementation("androidx.lifecycle:lifecycle-runtime-compose:2.8.7")
}
```

- `Flow`: `collectAsState()`는 `collectAsStateWithLifecycle`과 비슷하게 `Flow`에서 값을 수집해서 `State`로 변환한다.
	- `collectAsStateWithLifecycle`는 Android 전용인데 `collectAsState`는 플랫폼에 구애받지 않는다.

- `LiveData`: `observeAsState()`는 `LiveData`를 관찰하고 그 값을 `State`를 통해 나타낸다.
```kotlin
dependencies {
      ...
      implementation("androidx.compose.runtime:runtime-livedata:1.8.1")
}
```

- `RxJava2`: `subscribeAsState()`는 RxJava2의 반응형 스트림을 `State`로 변환한다.

```kotlin
dependencies {
      ...
      implementation("androidx.compose.runtime:runtime-rxjava2:1.8.1")
}
```

- `RxJava3`: `subscribeAsState()`는 RxJava3의 반응형 스트림을 `State`로 변환한다.
```kotlin
dependencies {
      ...
      implementation("androidx.compose.runtime:runtime-rxjava3:1.8.1")
}
```

## Stateful vs. Stateless

`remember`을 사용해서 객체를 저장하는 Composable은 내부 상태를 생성하여 Stateful하게 만든다.

**stateless** Composable은 어떤 상태도 유지하지 않는다.
[[#State hoising]]을 사용하여 stateless를 유지할 수 있다.

reusable Composable을 개발할 때 stateful 버전과 stateless 버전을 모두 노출하기 원할 수 있다.

## State hoisting

Compose에서 `state hoisting`은 composable을 stateless하게 만들기 위해 composable의 caller(호출자)를 이동시키는 패턴이다. 상태 변수를 두 개의 파라미터로 대체한다.

- `value: T`: 표시할 값
- `onValueChange: (T) -> Unit`: 값 변경을 요청하는 이벤트 `T`는 업데이트할 새로운 값

hoisting된 state는 몇가지 중요한 속성을 가지는데

- **Single source of truth**: 상태를 복제하는 대신 이동시킴으로써 단 하나의 진실된 source가 있음을 보장
- **Encapsulated**: stateful composable만 자신의 state를 수정할 수 있다.
- **Shareable**: hoisting된 상태는 여러 composable과 공유될 수 있다.
- **Interceptable**: stateless composable의 caller는 상태 변경 전에 이벤트를 무시하거나 수정할 수 있다.
- **Decoupled**: stateless composable의 상태는 어디에든 저장될 수 있다. `name`을 `ViewModel`로 이동하는 것이 가능하다.

```kotlin
@Composable
fun HelloScreen() {
    var name by rememberSaveable { mutableStateOf("") } // HelloScreen이 상태를 소유
    HelloContent(name = name, onNameChange = { name = it }) // HelloContent에 상태와 이벤트 전달
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Column(modifier = Modifier.padding(16.dp)) {
        Text(
            text = "Hello, $name",
            modifier = Modifier.padding(bottom = 8.dp),
            style = MaterialTheme.typography.bodyMedium
        )
        OutlinedTextField(value = name, onValueChange = onNameChange, label = { Text("Name") })
    }
}
```

`HelloContent`에서 상태를 hoisting 함으로써 상태가 저장되는 방식으로부터 decoupled 된다.
`HelloScreen`을 수정해도 `HelloContent`가 구현되는 방식 변경이 필요없다.

State는 아래로, Event는 위로 흐르는 패턴을 **unidirectional data flow**라고 한다.
![](https://developer.android.com/static/develop/ui/compose/images/udf-hello-screen.png)
State를 hoisting할 때, state가 어디로 가야하는지 파악하는 규칙

- state는 state를 사용하는 모든 composable들의 **lowest common parent (최소 공통 부모)** 까지 호이스팅되어야 한다.
- state는 변경 가능한 **highest level**까지 호이스팅되어야 한다.
- 두 state가 동일한 event에 반응하여 변경되는 경우 함께 hositing 되어야 한다.


## Restoring state in Compose

`rememberSaveable` API는 `remember`과 유사하게 작동하여 recomposition 간 상태를 유지하고 프로세스 재구성 때도 상태를 유지한다

액티비티가 유저에 의해 완전히 종료될 때는 상태 유지하지 않음

### Ways to store state

`Bundle`에 추가되는 모든 데이터 타입은 자동으로 저장됨
`Bundle`에 추가할 수 없는 것을 저장하고 싶을 때 사용하는 방법에는

#### Parcelize
```kotlin
@Parcelize
data class City(val name: String, val country: String) : Parcelable

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

#### MapSaver
```kotlin
data class City(val name: String, val country: String)

val CitySaver = run {
    val nameKey = "Name"
    val countryKey = "Country"
    mapSaver(
        save = { mapOf(nameKey to it.name, countryKey to it.country) },
        restore = { City(it[nameKey] as String, it[countryKey] as String) }
    )
}

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable(stateSaver = CitySaver) {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

#### ListSaver

```kotlin
data class City(val name: String, val country: String)

val CitySaver = listSaver<City, Any>(
    save = { listOf(it.name, it.country) },
    restore = { City(it[0] as String, it[1] as String) }
)

@Composable
fun CityScreen() {
    var selectedCity = rememberSaveable(stateSaver = CitySaver) {
        mutableStateOf(City("Madrid", "Spain"))
    }
}
```

## State holders in Compose

간단한 state hoisting은 composable 함수 내에서 관리할 수 있는데, 로직이 늘어나면 [**state holder**](https://developer.android.com/topic/architecture/ui-layer/stateholders)에 로직이나 state 책임을 위임하는 것이 좋다.

## Retrigger remember calculations when keys change

`remember` API는 `MutableState`와 함께 자주 사용됨

```kotlin
var name by remember { mutableStateOf("") }
```

`remember` 함수를 사용하면 `MutableState`값이 recomposition 간에 유지된다.

`remember`는 일반적으로 `calculation` 람다 파라미터를 받는다. `remember`가 처음 실행될 때, `calculation`람다를 호출하고 그 결과를 저장하고, recomposition 때 마지막으로 저장된 값을 리턴한다.

상태 캐싱 외에 `remember` 계산 cost가 많이드는 연산 결과를 컴포지션에 저장할 수 있다. 

```kotlin
val brush = remember {
    ShaderBrush(
        BitmapShader(
            ImageBitmap.imageResource(res, avatarRes).asAndroidBitmap(),
            Shader.TileMode.REPEAT,
            Shader.TileMode.REPEAT
        )
    )
}
```

`remember` API가 받는 `key` 혹은 `keys` 파라미터가 변경되면 다음 recompose 때 `remember`는 캐시를 무효화하고 `calculation` 람다 블록을 다시 실행한다.

```kotlin
@Composable
private fun BackgroundBanner(
    @DrawableRes avatarRes: Int,
    modifier: Modifier = Modifier,
    res: Resources = LocalContext.current.resources
) {
    val brush = remember(key1 = avatarRes) { // avatarRes가 변경되면 brush가 다시 계산됨
        ShaderBrush(
            BitmapShader(
                ImageBitmap.imageResource(res, avatarRes).asAndroidBitmap(),
                Shader.TileMode.REPEAT,
                Shader.TileMode.REPEAT
            )
        )
    }
    Box(
        modifier = modifier.background(brush)
    ) {
        /* ... */
    }
}
```

위 코드에서 `remember`는 `avatarRes`를 `key1` 파라미터로 받는데, `avatarRes`가 변경되면 리컴포즈되고, `Box`에 다시 적용된다.

```kotlin
@Composable
private fun rememberMyAppState(
    windowSizeClass: WindowSizeClass
): MyAppState {
    return remember(windowSizeClass) { // windowSizeClass가 변경되면 MyAppState 인스턴스가 다시 생성됨
        MyAppState(windowSizeClass)
    }
}

@Stable
class MyAppState(
    private val windowSizeClass: WindowSizeClass
) { /* ... */ }
```

위 코드에서는 state가 state holder class인 `MyAppState`로 호이스팅된다.

