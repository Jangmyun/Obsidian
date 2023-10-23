## 설치 / 세팅
### redux 설치
```bash
$ npm install @reduxjs/toolkit react-redux
```

### `store.js` 파일 생성
```javascript
import {configureStore} from '@reduxjs/toolkit';

export default configureStore({
	reducer: {
	
	}
})
```
#### store.js에 state 보관하고 꺼내쓸거임

### `index.js`
```javascript
import { Provider } from 'react-redux';
import store from "./store.js";

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <Provider store={store}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </Provider>
  </React.StrictMode>
); 
```
#### index.js에 store랑 `Provider` import 해주고 `<Provider store={store}>`로 App 컴포넌트 감싸면 됨

#### 이제 store.js에 있는 state를 App의 하위 컴포넌트까지 다 사용가능함

## Redux store에 state 보관하기
```javascript 
//createSlice import 해오기
import {configureStore , createSlice} from '@reduxjs/toolkit';

// 변수에 createSlice 써서 state 이름이랑 값 넣기
let user = createSlice({
	name: 'state이름',
	initialState: '값'
});

export default configureStore({
	// reducer 안에 작명 : 변수이름.reducer 쓰면 됨
	reducer:{
		user : user.reducer
	}
})
```

## 보관한 state 꺼내 쓰기
```javascript
import {useSelector} from 'react-redux';
function Compo() {
	let a = useSelector((state)=>{return state})
	return ();
}
```

### `useSelector` 안에 콜백함수 매개변수 state = store.js에 저장된 모든 state
#### `return state.원하는state` 해주면 원하는 state만 변수에 넘겨줄 수 있음
```javascript
let a = useSelector((state)=> return state.user);
```

### 컴포넌트 간 공유가 필요없으면 그냥 `useState()` 쓰면 됨

## Redux에서 state 변경하기

1. ### `createSlice()`안에 state 변경함수 만들어 넣기 
2. ### 만든 state 변경함수 export
#### store.js
```javascript
import {configureStore, createSlice} from '@reduxjs/toolkit';

let user = createSlice({
	name: 'user',
	initialState: 'kim',
	reduceres : { // reducers에 Object 형식으로 함수 만들기
		setUser(user){
			return user
		}
	}
})
export let {setUser} = user.actions
```

3. ### `useDispatch` import 후 변수에 넣어서 `dispatch(setState())` 로 사용
#### App.js
```javascript
import {useDispatch , useSelector} from "react-redux"
import {setUser} from './store.js';

function App(){
	let state = useSelector((state)=>{ return state})
	let dispatch = useDispatch();
	return(
		<button onClick={()=>{
			dispatch(setUser('park'));
		}}>setUser</button>
	);
}
```


## state가 Object / Array 자료형일때 state 변경하는 방법
### array / object 의 경우 `state.key` 로 직접 수정해도 변경됨 (Immer.js)
#### 이게 편해서 일부러 문자 하나만 필요할때도 object 자료형으로 담아서 쓸 때 있음

## state 변경함수에 파라미터 사용
```javascript
let user = createSlice({
	name: 'user',
	initialState : {name: 'kim', age : 20},
	reducers: {
		changeName(state, name){
			state.name = name.payload;
		},
		increaseAge(state, a){
			state.age += a.payload;
		}
	}
})
```
#### `state변경함수(state, arg...){ state.key = arg.payload}`
#### 매개변수 뒤에 `.payload` 붙여서 쓰면 됨 <br>매개변수 이름은 action으로 하는게 관습

