## 리액트 프로젝트 생성하는 방법

### 맥OS homebrew로 Node.JS 설치

```bash
$ brew install node
```

#### 이러면 npm까지 같이 설치됨



### 프로젝트 생성하려는 디렉토리로 이동해서

```bash
$ npx create-react-app ProjectName
```

#### 이러면 해당 디렉토리에 이름이 `ProjectName`인 폴더 생성됨


![[appjs.png]]

#### 만들어진 폴더안의 src 폴더 내의 `App.js`가 메인 파일임

### 프로젝트 디렉토리로 들어가서  `$ npm start` 하면 브라우저 열리고 만들어진 웹페이지 볼 수 있음









## 리액트에서 사용하는 JSX 문법



### JSX: JS파일에서 사용하는 HTML 비슷한거

```react
<div className="nav"></div>
```



#### 1. JSX에서는 `class` 대신 `className`을 써서 클래스 적용함

```react
import `css파일 경로`;
```

### css파일 import하려면 `App.js` 파일 상단에 import 문 넣어주면 됨



### 2. 변수를 넣을 때 `{}` 중괄호 사용함  (데이터 바인딩)

```react
function App() {
  let blogTitle = "New Blog";	//변수 생성
  return (
    <div className="App">
      <div class="nav">
        <h4>{blogTitle}</h4>   
      </div>
    </div>
  );
}
```

#### 위처럼 blogTitle이라는 변수를 만들고 그 변수를 h4의 innerText로 넣을 수 있음

#### innerText 말고도 id나 className도 변경할 수 있음



### 3. style 넣을 때 `style={}` 써서 스타일 적용함

```react
<div className="App">
	<div class="nav">
		<h4 style={ {color : 'red'} }>{blogTitle}</h4>     
  </div>
</div>
```

#### `style={ { style : 'value'} }` 처럼 중괄호를 사용하고 스타일 속성은 **camelCase** 로 작성해야함<br>js에서 `-`기호는 빼기라서



### 참고로 `return ()` 안에 태그 작성하는데 태그 하나 안에 모든게 다 들어가야함 태그를 병렬로 작성하면 오류남

```react
return (
  <div className="App">
  	<div></div>
    <div></div>
  </div>
);
```

#### 위 예시처럼 `className`이  `App`인 div 태그 안에 모든 태그들이 들어가있어야 함









## state

### state 생성

```react
import { useState } from 'react';

function App() {
  let [articlTitle,b] = useState('Value');
}
```

#### `let [a,b] = useState()` 를 이해하려면 JS의 **Destructuring assignment** 문법에 대해 알아야 한다.

```javascript
const numbers = [1,2,3,4];
const [ first, second, ,fourth ] = numbers; // first에 1, second에 2, fourth에 4를 대입함

let person = {
  firstName: 'John',
  lastName: 'Doe',
  age: 21
};
let { firstName, lastName, age } = person;	//firstName, lastName, age에 객체의 키값 할당
```

#### 배열이나 객체의 값을 대입할 때 편함



```react
import {useState} from 'react';

function App() {
  let [articleTitle, setArticleTitle] = useState('Blog article 1');
  articleTi
  return (
    <div className="App">
    	<div className="article">
      	<h4>{ articleTitle }</h4>	{/* useState로 만들어진 변수 바인딩*/}
      </div>
    </div>
  );
}
```

#### 일반적인 변수로 html에 데이터를 바인딩 해도 변수가 갑자기 바뀔 때 그 값이 자동으로 반영이 안된다.

### `state`쓰면  값이 바뀌면 html이 자동 재렌더링 됨<br>state값 변경시 html에 바로바로 반영하고 싶으면 state 쓰면 됨



### state 값 변경

```react
let [like, setLike] = useState(0);
```

#### JS의 Destructuring 문법을 써서 state와 state를 변경하기위한 함수를 넣었었다. state 변경함수를 사용해야 값이 바뀔때마다 html에 바로바로 반영됨

```react
<div className="list">
	<h4>{ articleTitle[0] }   <span onClick={()=>{setLike(++like)}}>👍</span>{like}</h4>
  <p>2023.07.23</p>
</div>
```



#### state 값 변경시 배열이나 객체값 변경할 때 기존 값 보존하는 식으로 코드 짤 때 state 변경함수가 객체의 값이 같은지 (주소값) 체크해서 같으면 값 변경 안해줌 => 주소값을 변경해야하는데 전개연산자 사용하면 됨

```react
<button onClick={()=>{ 
	let copy = [...articleTitle];	// 전개연산자 사용
	copy[0] = '가나다라마바사';	//복사한 배열에 새값 대입
	setArticleTitle(copy );		//복사한 배열로 state변경
}}></button>
```

### 전개 연산자 `[...]`는 다른 주소값을 갖는 독립적인 데이터를 가지게 한다. <br>근데 얕은 복사만 됨









## Component

### 컴포넌트 만드는 방법

#### 함수 return() 안에 html 태그 넣기<br>`<함수이름></함수이름>` 쓰면 됨

```react
function Modal(){
  return(
    <div className='modal'>
      <h4>제목</h4>
      <p>날짜</p>
      <p>상세내용</p>
    </div>
  );
}
```

#### 컴포넌트에서도 하나의 태그안에 모든 태그들이 들어가야함 <br>만약 병렬로 html 태그를 만들고 싶으면

```react
function Modal(){
  return(
    <>
      <div className='modal'>
        <h4>제목</h4>
        <p>날짜</p>
        <p>상세내용</p>
      </div>
    </>
  );
}
```

#### `<> </>` 안에 감싸면 됨

### 컴포넌트는 state값 가져올 때 문제 좀 있으니까 남발하지 말고 반복되는 html에 활용









## React에서 동적 UI 만드는 방법



### state에 따라 UI가 어떻게 보일지 적으면 됨

```react
function App() {
  let [modal, setModal] = useState(false); //안보이는 상태
  return (
    <Modal></Modal>
    {
    	true ? 'true' : 'false';
    }
  );
}
```

#### html 중간에 if문 사용이 안됨. 그래서 삼항 연산자 써야됨

### 삼항연산자: `조건문 ? 참일 때 : 거짓일 때;`

### 삼항 연산자 사용해서 코드 짜보면

```react
function App() {
	let [modal, setModal] = useState(true);
	return (
    <button onClick={()=>{
        setModal(!modal); //버튼을 누를때마다 모달 true false 바뀌게
      }}>Modal Toggle</button>
    {
    	modal ? <Modal></Modal> : null
    //modal값이 true면 <Modal/> 보이게 false면 안보이게
    }
  );
}
```