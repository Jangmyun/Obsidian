# 리액트 기초

---



## `map` 반복문 써서 html 요소 여러개 만들기

#### `map()` : 메서드는 배열요소 하나마다 함수 호출하고 callback함수 리턴값을 모은 배열 리턴함

```javascript
let arr = ['a', 'b', 'c', 'd'];
arr.map(function(currentValue, index, array){
  console.log(currentValue);	// 반복중 현재요소의 값
  console.log(index);	//반복중 현재요소의 인덱스
  console.log(array);	// map 메서드를 호출한 배열전체
})
```



### 블로그 글 리스트를 이걸 써서 만들면 될듯하다.

```react
function App(){
  articleTitle.map(function(value, i){
    return (
      <div className="list">
        <h4>{value}</h4>
        <p>2023.08.29</p>
      </div>
    );
  })
}
```

#### 여기다가 좋아요도 개별로 만들어봄

```react
function App(){
  let likeArr = articleTitle.map(function(){
    return 0;	//articleTitle배열의 길이만큼 0으로 채워진 배열 생성
  })
}
```







## props

### 부모 컴포넌트로부터 자식 컴포넌트로 state 전송 가능함<br>그 때 사용하는게 props

```react
{
  modal == true ? <Modal articleTitle={articleTitle}></Modal>
}
```

#### 자식 컴포넌트에 state이름으로 `작명={state이름}` 넘겨주고

```react
function Modal(props){
  return(
  	<div className='modal'>
      <h4>props.state이름</h4>
      <p>날짜</p>
      <p>상세내용</p>
    </div>
  );
}
```

#### 자식 컴포넌트 파라미터로 `props` 넘긴다음 `props.작명`으로 state값 사용하면 됨







## 사용자 입력 다루기

### 바닐라 JS 쓸때처럼 `onChange` 이벤트 핸들러 만들어 놓고 파라미터로 `e` 넘겨서 `e.target.value` 이런거 사용하면 됨

```react
{
  let [input, setInput] = useState('');
}
<input type="text" onChange={(e)=>{
    setInput(e.target.value);	//변경된 입력값 
  }}></input>
```





## React Bootstrap

### 리액트에서 부트스트랩 쓰려면 React Bootstrap 설치하면 됨

```bash
$ npm install react-bootstrap bootstrap
```

### Component 같은거 부트스트랩 웹사이트에서 찾아서 쓰면 되는데 해당 컴포넌트 import 잘 시켜줘야함



## JSX에서 이미지 넣기

```react
import imgName from './이미지경로'; 	// 사용전에 import

function App(){
  return(
  	<div className="main-bg" style={{backgroundImage: 'url('+ imgName +')'}}></div>	
  ); 
}
```

### `<img src={}>` 으로 public 폴더안의 이미지 에러없이 잘 쓰려면

```react
function App(){
  return(
  	<img src={process.env.PUBLIC_URL + '/퍼블릭 폴더 내 경로'} />
  );
}
```

#### 이렇게 설정해주면 리액트 프로젝트를 발행할 때 경로 문제 없을듯





## 다른 js 파일에 데이터 저장하고 export 하는 법

```react
let a = 10;

export deafult a;
```

#### 다른 js 파일에 `export deaulft 변수명` 하고

```react
import a form './data.js'
```

#### 데이터 쓰려는  js 파일에 `import 변수명 from '파일경로'` 해주면 됨





## React Router

### 리액트 없이 페이지 구분하는 방법: html 파일 새로 만들어서 그거 연결함

### 리액트는 싱글페이지 어플리케이션이라서 `index.html`만 씀<br>컴포넌트로 페이지 내용을 채우고 다른페이지 접속하면 그 페이지 컴포넌트 보여줌

### `react-router-dom` 이거 쓰면 쉽게 할 수 있음



```react
//index.js
import {BrowserRouter} from "react-router-dom";

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

####  index.js 파일열고 `react-router-dom`에서  `BrowserRouter` import 시키고 `<App />`태그를 `<BrowserRouter>`로 감싸면 됨



```react
//App.js
import {Routes, Route, Link} from 'react-route-dom';
```

### `App.js` 에 Routes랑 Route import 시키고

```react
function App({
	return(
  	<Routes>
			<Route path="/" element={<div>Main Page</div>}></Route>  
    	<Route path="/cart" element={<div>Cart Page</div>}></Route>
  	</Routes>
  );
}
```

### `<Routes>` 태그안에 `<Route>` 태그 넣고 경로랑 해당 경로에 들어가면 표시될 html 태그 넣어주면 됨





## Navigate, nested Routes, Outlet

```react
import {useNavigate, Outlet} from 'react-router-dom';
```

#### Navigate랑 Outlet 사용하려면 import 시켜줘야함

```react
function App(){
	let navigate = useNavigate();
  
  return(
		<button onClick={()=>{
        navigate('/cart')
      }}></button>
  );
}
```

#### `useNavigate()`는 페이지 이동 도와주는 함수<br>onClick 열고 `navigate('경로')` 적으면 이동시켜줌



## 404 페이지 만드는 방법

### 404 페이지: 링크 주소 잘못 쳤을 때 나오는 페이지

```react
<Routes>
	<Route path="*" element={<ErrorPage404/>}></Route>
</Routes>
```

#### `<Route>` path 속성에 `*` 적으면 내가 라우팅한 경로 외에 모든 경로에 html 요소를 보여줄 수 있음



## Nested Routes

```react
<Routes>
	<Route path="/about"></Route>
  <Route path="/about/member"></Route>
  <Route path="/about/location"></Route>
</Routes>

<Routes>
	<Route path="/about">
  	<Route path="member"></Route>
    <Route path="location"></Route>
  </Route>
</Routes>
```

### 위 두 `<Routes>` 태그는 똑같은거임

### 이렇게 nested된 `<Route>` 의 html요소는 부모 `<Route>` 태그 안에 보여지는데 이때 `<Outlet></Outlet>` 사용하면 됨

```react
function App(){
  return(
  	<Routes>
			<Route path="/about" element={<About/>}>
  			<Route path="member" element={<div>우리 회사 멤버</div>}></Route>
    		<Route path="location"></Route>
  		</Route>
		</Routes>
  );
}
function About(){
  return(
  	<div>
    	<h4>회사정보</h4>
      <Outlet></Outlet>
    </div>
  );
}
```

### /about/member 접속하면 `<About>` 이랑 `<div>우리 회사 멤버</div>`가 둘 다 보인다.



## URL 파라미터

```react
<Route path="/detail/:id" element={<div></div>}/>
```

### 라우터 경로에 `:id` 이거 추가해서 URL 파라미터 사용하면 라우트 태그 많이 쓸 필요 없어짐

#### 이렇게 받아온 URL 파라미터는

```react
import {useParams} from "react-router-dom";

function Detail() {
  let {id} = useParams();
}
```





## styled-components

#### styled-components 쓰면 css 파일에 스타일링 안해도 js 코드로 컴포넌트에 스타일 넣을 수 있음

```react
let YellowBtn = styled.button`
	background: yellow;
	padding: 10px;
`

return(
	<YellowBtn></YellowBtn>
)
```





#### props 문법 사용하면 같은 컴포넌트 재활용할 수 있음

```react
function App(){
  let Btn = styled.button`
  	background : ${ props => props.bg};
  	color : black;
  	padding : 10px;
  `
  return(
  	<Btn bg="blue"></Btn>
  );
}

```

```react
let Btn = styled.button`
	background : ${ props => props.bg };
	color : ${props=> props.bg == 'blue' ? 'white' : 'black'};
	padding 10px;
`
```

#### 위에 처럼 조건문 넣을수도 있음

```react
let NewBtn = styled.button(Btn)`
	margin : 10px;
`
```

#### 기존 컴포넌트 가져와서 스타일링 추가도 됨



## Lifecycle

### 컴포넌트의 mount, update, unmount 과정 중간에 코드 실행 가능

```react
//옛날방식
class Component1 extends React.Component {
  componentDidMount(){
    // 컴포넌트 mount 시 실행할 코드
  }
  componentDidUpdate(){
    // 컴포넌트 update 시 실행할 코드
  }
  componentWillUnmount(){
    // 컴포넌트 unmount시 실행할 코드
  }
}

//요새 방식
import {useEffect} from "react";
function Component1(props){
  useEffect(()=>{
    // mount update시 실행할 코드
  })
}
```



### 근데 사실 `useEffect` 바깥에 코드 적어도 동작하는데 `useEffect`는 HTML 렌더링 끝내고 동작함<br>서버에서 데이터 가져오거나 타이머 사용할 때 사용하면 좋을듯

