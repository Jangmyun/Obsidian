
## The declarative programming paradigm

선언형 프로그래밍 패러다임

View를 수동으로 조작하는 것은 오류가 발생하기 쉽다. 하나의 데이터가 여러 곳에 렌더링 될 때, 해당 데이터를 표시하는 뷰 업데이트를 하나하나 추적하기 쉽지 않다.
또 두 개의 업데이트가 충돌할 때 illegal한 상태를 만들기도 쉽다.
업데이트가 필요한 뷰의 수가 늘어날수록 유지보수 복잡성도 증가한다.

**declarative(선언형) UI 모델**:
전체 화면을 처음부터 다시 생성한 다음 필요한 변경 사항만 적용하는 방식으로 작동하는데, cost를 줄이기 위해 Compose는 UI의 어떤 부분이 리렌더링 되어야하는지 선택함.

이를 **Recomposition**이라고 부른다.

## The declarative paradigm shift

선언형 패러다임으로의 전환

Compose의 선언형 접근 방식에서는 위젯이 상대적으로 상태를 가지지 않고, setter와 getter를 expose하지 않는다.

Composable 함수를 다른 파라미터로 호출하면서 업데이트 된다.

![](https://developer.android.com/static/develop/ui/compose/images/mmodel-flow-data.png)

앱 로직이 최상위 Composable 함수에 데이터를 제공하고, 데이터를 사용하여 다른 Composable을 호출함으로써 UI를 설명하고, 적절한 데이터를 아래로 전달한다.

![](https://developer.android.com/static/develop/ui/compose/images/mmodel-flow-events.png)

유저가 UI와 상호작용할 때, UI는 `onClick`같은 이벤트를 발생시키고, 앱 로직이 이벤트를 알고 앱의 state를 변경할 수 있다. 변경된 state를 가지고 Composable 함수가 다시 호출되고, UI가 다시 그려지는데, 이것을 **Recomposition**이라고 한다.

## Dynamic Content

Composable함수는 XML대신 Kotlin으로 작성되기 때문에 동적으로 작동할 수 있다.

```kotlin
@Composable
fun Greeting(names: List<String>) {
    for (name in names) {
        Text("Hello $name")
    }
}
```

UI 를 표시할지 여부를 `if`문으로 결정할 수 있다.
반복문을 통해 List를 받아 동적인 UI를 생성할 수 있다.
helper function을 호출할 수 있다.

## Recomposition

명령형 UI 모델에서는 위젯의 내부 상태를 변경하기 위해 setter를 호출하는 것과 달리 Compose에서는 변경된 새로운 데이터를 가지고 Composable 함수를 다시 호출하여 리컴포즈한다.

```kotlin
@Composable
fun ClickCounter(clicks: Int, onClick: () -> Unit) {
    Button(onClick = onClick) {
        Text("I've been clicked $clicks times")
    }
}
```

위 코드는 버튼을 클릭할 때마다 해당 함수의 caller는 `clicks` 값을 업데이트하고, 새로운 값을 표시하기 위해 `Text`와 람다를 다시 호출한다. (recompose)

Composable 함수에 의한 **side-effect**에 의존하면 안된다. 함수의 리컴포지션이 skip될 수 있다.

**side-effect**란 앱의 나머지 부분에 가시적인 모든 변경을 의미한다.

- Writing to a property of a shared object
- Updating an observable in `ViewModel`
- Updating shared preferences

같은 것들이 있다.

Composable 함수는 애니메이션이 렌더링될 때처럼 매우 자주 재실행될 수 있기 때문에 빨라야 한다. Cost가 많이 드는 작업을 수행해야 할 때, 백그라운드 코루틴에서 수정 후 그 결과를 Composable 함수에 파라미터로 전달해야 한다.

```kotlin
@Composable
fun SharedPrefsToggle(
    text: String,
    value: Boolean,
    onValueChanged: (Boolean) -> Unit
) {
    Row {
        Text(text)
        Checkbox(checked = value, onCheckedChange = onValueChanged)
    }
}
```

위 코드는 `SharedPreferences` 값을 업데이트하는 Composable을 생성한다. Composable는 shared preferences를 읽거나 쓰지 않아야 한다. 
앱 로직이 현재 값을 콜백과 함께 전달하여 업데이트를 trigger 한다.

## Recomposition을 사용할 때 주의해야할 사항

- [[#Recomposition skips as much as possible]]
- [[#Recomposition is optimistic]]
- [[#Composable functions might run quite frequently]]
- [[#Composable functions could be run in parallel]]
- [[#Composable functions can execute in any order]]

### Recomposition skips as much as possible

 Compose는 UI 일부가 유효하지 않을 때 필요한 부분만 recompose하고자 한다.

모든 Composable 함수와 람다는 스스로 recompose될 수 있다.

```kotlin
/** * Display a list of names the user can click with a header */
@Composable
fun NamePicker(
    header: String,
    names: List<String>,
    onNameClicked: (String) -> Unit
) {
    Column {
        // this will recompose when [header] changes, but not when [names] changes
        Text(header, style = MaterialTheme.typography.bodyLarge)
        HorizontalDivider()
        // LazyColumn is the Compose version of a RecyclerView.
        // The lambda passed to items() is similar to a RecyclerView.ViewHolder.
        LazyColumn {
            items(names) { name ->
                // When an item's [name] updates, the adapter for that item
                // will recompose. This will not recompose when [header] changes
                NamePickerItem(name, onNameClicked)
            }
        }
    }
}

/** * Display a single name the user can click. */
@Composable
private fun NamePickerItem(name: String, onClicked: (String) -> Unit) {
    Text(name, Modifier.clickable(onClick = { onClicked(name) }))
}
```

위 코드에서 `header`가 변경될 때 `Column` 람다로 건너뛰어서 상위 Composable을 실행하지 않을 수 있다.
`Column`을 실행할 때, `names`가 변경되지 않았다면 `LazyColumn`의 아이템들을 건너뛰도록 선택할 수 있다.

> 모든 Composable 함수나 람다는 **side-effect** 가 없어야 한다.
> **side-effect**를 수행해야 할 때는 콜백을 통해 trigger 해야 한다.


### Recomposition is optimistic

Recomposition은 Composable의 매개변수가 변경되었을 수 있다고 판단할 때마다 실행된다.

Recomposition은 **optimistic (낙관적)** 인데, 이는 Compose가 파라미터가 변경되기 전에 Recomposition을 완료할 것이라고 가정한다는 뜻이다. 

Recomposition이 완료되기 전에 파라미터가 변경된다면 Recomposition을 취소하고 새로운 파라미터로 다시 실행할 수 있다.

Recomposition이 취소되면 해당 Recomposition에서 생성된 UI트리를 폐기하는데 이때 side-effect가 있다면 해당 효과는 컴포지션이 취소돼도 적용될 것이기 때문에

> optimistic recomposition을 처리하기 위해 Composable 함수와 람다는 **idempotent (멱등적)** 이고 side-effect가 없도록 보장해야 한다.

### Composable functions might run quite frequently

Composable 함수는 매우 자주 실행될 수 있기 때문에 cost 많이 드는 작업을 Composable 함수에서 분리해야 한다.

Composable 함수에 데이터가 필요한 경우 해당 데이터에 대한 파라미터를 정의하고, cost가 많이 드는 작업을 컴포지션 외부의 다른 스레드로 옮기고, `mutableStateOf`나 `LiveData`를 사용해서 데이터를 Compose에 전달할 수 있다.


### Composable functions could be run in parallel

Compose는 컴포저블 함수를 병렬로 실행하여 Recomposition을 최적화 할 수 있다.

Composable 함수가 호출될 때 호출은 호출자와 다른 스레드에서 발생할 수 있기 때문에 **Composable 람다 내에서 변수를 수정하는 코드를 피해야 한다.**

```kotlin
@Composable
fun ListWithBug(myList: List<String>) {
    var items = 0
    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Card {
                    Text("Item: $item")
                    items++ // Avoid! Side-effect of the column recomposing.
                }
            }
        }
        Text("Count: ${items}")
    }
}
```

위 코드처럼 함수 내에서 지역 변수를 사용하는 경우

`items`는 모든 recomposition에서 수정되기 때문에 잘못된 item개수를 표시할 수 있다.

### Composable functions can execute in any order

> Compose는 메인 스레드에서만 동작하지만 멀티스레딩을 염두에 두고 설계했기 때문에 멀티스레딩 상황을 가정하고 작성해야 한다.

Composable함수가 다른 composable함수에 대한 호출을 포함하고 있다면, 그 함수들의 순서는 보장되지 않는다.

```kotlin
@Composable
fun ButtonRow() {
    MyFancyNavigation {
        StartScreen()
        MiddleScreen()
        EndScreen()
    }
}
```

위 코드에서 `StartScreen`, `MiddleScreen`, `EndScreen` 에 대한 호출은 어떤 순서로도 발생할 수 있다.

때문에 `StartScreen()`에서 side-effect를 설정하고 `MiddleScreen()`에서 side-effect를 활용하는 코드를 작성할 수 없다.

모든 함수는 **self-contained**여야 한다.