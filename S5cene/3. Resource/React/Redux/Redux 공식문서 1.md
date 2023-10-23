# #Redux 란?
### **action**이라고 불리는 이벤트를 사용해서 어플리케이션의 상태(state)를 관리하고 업데이트하는 라이브러리
<br>

## Redux 사용해야 하는 이유
### redux는 global state 관리에 이점이 있다.<br>redux가 제공하는 도구와 패턴을 사용하면 앱에서 업데이트되는 state가 언제, 어디서, 어떻게, 왜 사용되는지 이해하기 쉬워진다.
<br>

## Redux 언제 씀?
+ ### 앱 내에 많은 양의 state를 다룰 때
+ ### state가 빈번하게 업데이트될 때
+ ### state를 업데이트하는 로직이 복잡할 때
+ ### 앱의 코드베이스(소스코드)를 다루는 사람이 많을 때
<br>

## Redux 라이브러리 / 툴

+ ### React-Redux
#### Redux는 주로 React와 함께 사용된다. **React-Redux**는 react컴포넌트가 redux store와 상호작용할 수 있도록 한다.
+ ### Redux Toolkit
#### Redux에서 공식적으로 추천하는 Redux 사용방법으로<br>Redux 앱 제작에 있어 필수적이고 편리한 패키지와 함수가 들어있음.
+ ### Redux DevTools Extension
#### Redux store 내부의 state 변화를 추적할 수 있게 해주는 디버깅 툴이다.
<br>

# Redux 용어와 개념
## State Management
### one-way data flow
```jsx
function Counter(){
	// state: counter 값
	const [counter, setCounter] = useState(0)
	// action: 특정 상황에서 state의 값을 변경하는 코드
	const increment = ()=>{
		setCounter(prevCounter=>prevCounter + 1)
	}
	// view: current state에 따라 변하는 UI 정의부분
	return(
		<div>
			Value: {counter} 
			<button onClick={increment}>Increment</button>
		</div>
	)
}
```
+ #### UI는 state에 따라 렌더링 된다.
+ #### 특정 event (action)가 발생했을 때 state가 업데이트 된다.
+ #### UI가 새로운 state에 맞추어 재렌더링 된다.
### 근데 같은 state를 공유하는 컴포넌트가 많아지면 관리 힘들어질 듯?
#### 컴포넌트에서 state를 꺼내서 한곳에 모아놓으면 컴포넌트는 출력부분에만 집중하면 되고 컴포넌트의 포함관계에 상관없이 state값에 접근하거나 action을 실행할 수 있다.<br>UI부분과 state부분을 분리하면서 코드의 구조와 유지보수 측면에 도움이 됨
## Immuntability (불변성)
### JavaScript에서 Object / Array 자료형은 기본적으로 변경가능한 상태이다.
```js
const obj = { a:1, b:2 }
obj.b = 3 // 겉으로는 같은 Object 자료형인데 내부의 값이 바뀐다.

const arr = ['a', 'b']
arr.push('c');
arr[1] = 'd';  // Array도 내부 content 변경이 가능하다. (Mutable 하다.)
```
#### 값을 불변하게 바꾸려면 이미 존재하는 Object/Array 를 복사해서 복사본을 수정하는 방법을 사용해야한다.
#### JavaScript에서는 **spread 문법**을 사용해서 복사본을 다룰 수 있다.
```js
const obj = {
	a: {
		c:3
	},
	b: 2
}
const obj2 = {
	...obj,  // obj 객체 복사
	a: {  // a 덮어쓰기
		...obj.a,  // obj.a 객체 복사
		c: 4  // c 덮어쓰기
	}
}

const arr = ['a', 'b']
const arr2 = arr.concat('c');

const arr3 = arr.slice();
arr3.push('c');

```
#### Redux에서 모든 state는 복사본으로(**불변하게**) 업데이트해야 한다.
## Terminology
### Actions
#### Action은 `type` 속성을 가진(가져야하는) 순수 JS 객체이다. 앱에서 일어나는 일련의 이벤트를 설명해준다.
#### `type`의 **value는** action을 잘 설명할 수 있는 **문자열**이어야 함<br>관습적으로 `type`의 문자열은 `'domain/eventName'`으로 앞부분에 action의 카테고리, 두번째의 action이 하는 일을 명시해주면 편하다고 한다
```js
const addTodoAction = {
	type: 'todos/todoAdded',
	payload: 'Buy milk'
}
```
##### action 객체는 이렇게 생겼다. `payload`에 발생한 이벤트의 정보를 작성한다.
### Action Creators
#### Action Creator는 action 객체를 만들고 리턴한다. 보통 action 객체를 하드코딩하기보단 Action Creator를 사용해서 action객체를 생성한다.
```js
const addTodo = text => {
	return {
		type: 'todos/todoAdded',
		payload: text
	}
}
```
### Reducers
#### Reducer는 현재**state**와 **action**객체를 받아서 어떻게 업데이트할지 결정하는 함수이다. `function reducer(state, action) => newState`의 형태가 된다.
#### Reducer는
+ ##### 매개변수로 받는 **state**와 **action**에 기반해서 **new State**를 연산해야 한다.
+ ##### **state**를 직접 수정하는게 아니라 **state의 복사본**을 이용해서 불변적인 업데이트
+ ##### 비동기 로직, 랜덤 값 계산, 또는 기타 부작용이 없어야 한다.
#### **action**(`action.type`)을 체크하고, 해당사항이 있을경우 state의 복사본으로 업데이트 후 리턴한다. 해당사항이 없으면 기존 state를 리턴한다.
```js
const initialState = {value: 0}
function ReducerEx(state = initialState, action){
	//action.type 체크 후 일치하면
	if(action.type === 'counter/increment'){  
		return{
			...state,  // 기존 state를 복사하고
			value: state.value + 1  // 복사본의 값을 수정하고 리턴
		}
	}

	return state  // 일치하는 action.type이 없으면 그냥 state return
}
```
### Store
#### Redux 앱의 state는 **store**라고 불리는 객체 안에 존재한다.<br>**Reducer**를 넘겨주면서 **store**를 생성하고, `getState()`메서드로 state값에 접근할 수 있다.
```js
import {configureStore} from '@reduxjs/toolkit'

const store = configureStore({reducer: counterReducer})
```

### Dispatch
#### Redux store는 **dispatch**메서드를 가지고 있는데, **dispatch**메서드로 action객체를 넘겨줘야지만 state를 업데이트 할 수 있다.
```js
store.dispatch({type: 'counter/increment'})
console.log(store.getState()); // {value:1}
```
#### **dispatch**가 action 객체를 보내면 **Reducer**가 그걸 받아서 해당하는 action을 실행하고 state가 수정된다.
```js
const increment = ()=> {
	return {
		type: 'counter/increment'
	}
}
store.dispatch(increment())
```
##### 보통 action creator를 사용해서 reducer에게 action을 전달(dispatch)한다.

### Selectors
#### Selector는 store에 저장된 state값중 특정 부분만 추출하는 함수인데, 수정할 데이터의 크기가 커지고 계산이 복잡해질 때 성능저하를 예방할 수 있다.

## Redux 데이터 흐름
+ #### 앱 사용중 이벤트 발생 (ex. 버튼클릭)
+ #### **dispatch**를 통해 store에 action객체 전달
+ #### store가 `currentState`와 `action`을 매개변수로 받는 reducer를 실행하고 action에 맞는 코드를 실행해서 새로운 state를 리턴
+ #### store가 컴포넌트에 state변경이 일어났음을 알려주고
+ #### store의 데이터를 사용하는 컴포넌트는 컴포넌트가 사용하는 state값이 변경됐는지 체크한다.
+ #### 바뀐 state값에 맞추어 컴포넌트가 리렌더링된다.