
# Jetpack Compose란?

Jetpack Compose는 안드로이드 UI를 만들기 위한 modern toolkit
**composable functions**를 통해 UI를 정의할 수 있다

## Composable functions

- UI의 일부를 설명
- 아무것도 리턴하지 않음
- 입력을 받아 화면에 표시될 내용을 생성
- Pascal case로 함수명 작성

### Composable function name convention

- 명사여야 함
- 동사여선 안됨
- 명사화된 전치사여서는 안됨 `TextFieldWithLink()`
- 형용사여서는 안됨 `Bright()`
- 부사여서는 안됨 `Outside()`
- 명사는 설명적인 형용사로 접두어를 가질 수 있음 `RoundIcon()`

```kotlin
// Do: This function is a descriptive PascalCased noun as a visual UI element
@Composable
fun FancyButton(text: String) {}


// Do: This function is a descriptive PascalCased noun as a non-visual element
// with presence in the composition
@Composable
fun BackButtonHandler() {}


// Don't: This function is a noun but is not PascalCased!
@Composable
fun fancyButton(text: String) {}


// Don't: This function is PascalCased but is not a noun!
@Composable
fun RenderFancyButton(text: String) {}


// Don't: This function is neither PascalCased nor a noun!
@Composable
fun drawProfileImage(image: ImageAsset) {}
```


