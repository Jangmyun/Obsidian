# props 전송 없이 state 공유 가능

## 세팅

#### App.js
```js
import {createContext, useState} from 'react'; // createContext import하기

export let Context1 = createContext(); // Context = state 보관함

function App(){
	let [stock, setstock] = useState([10,2,6]);
	
	return (
		<Context1.provider value={{stock}}> // Context이름.provider로 원하는 컴포넌트 감싸기
		//value속성에 state 넣어주기
			<Detail></Detail>
		</Context1.provider>
	);
}
```

#### Detail.js
```js
import {useContext} from 'react';
import {Context1} from './App.js'; // App.js로부터 Context1 가져오기

function Detail(){
	let {stock} = useContext(Context1);  // stock state 쓰기
}
```


## 특징

1. #### state 변경될 때 state 사용 안하는 요소도 재렌더링됨 (비효율적)
2. #### 컴포넌트 재사용 어려움
	+ ##### 다른 페이지에서 컴포넌트 import 해서 쓰려고 할때 관리 힘듬
3. #### 라이브러리 깔아서 쓰는게 편할 수 있음 (redux)
