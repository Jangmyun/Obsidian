1. ## 컴포넌트 내부에서 사용하는 `if`/`else`
```js
function Component(){
	if(true){
		return <p>참이면 HTML 태그 보여줌</p>
	}
	return null
}
```
### 평소에 컴포넌트 만들 때 쓰던 `return()` 안에서는 `if`문 사용이 안됨
### 따라서 조건문 + `return JSX뭐시기` 방식으로 사용하면 됨

2. ## JSX 내부에서 삼항연산자
### `조건문 ? 참일때 : 거짓일때`
```js
function Componenet(){
	return (
		<div>
			{
				true ? <p>참이면 HTML 태그 보여줌</p> : null
			}
		</div>
	)
}
```

3. ## `&&` 연산자로 `if` 기능 대신하기
### JS에서 ``&&`` 로 연산하는 값 중에 처음 등장하는 falsy 값을 찾고 없다면 마지막 값을 남긴다.
```js
true && 'hi';  // hi
false && 'hi';  // false
```
### 이 특성을 응용하면
```javascript
function Component(){
	return (
		<div>
			{ 1 === 1 && <p>참이면 HTML 태그 보여줌</p>}
		</div>
	)
}
```
### `조건식 && JSX` 사용해서 조건식이 참이면 HTML로 렌더링 된다.

4. ## `switch`/ `case` 조건문 해
```js
function Component(){
	var user = 'a';
	switch(user){
		case 'a':
			return <p>user가 a일때 HTML태그 보여줌</p>
		case 'b':
			reutnrn <p>user가 b일때 HTML태그 보여줌</p>
			          
	}
}
```