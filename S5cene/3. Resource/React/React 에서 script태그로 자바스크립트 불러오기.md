# `index.html`에 script태그 추가
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1" />
		<meta name="theme-color" content="#000000" />
		<meta name="description" content="Web site created using create-react-app"/>
		<title>Example</title>
	</head>
	<body>
		<noscript>You need to enable JavaScript to run this app.</noscript>
		<div id="root"></div>
		<script type="text/javascript" src="//dapi.kakao.com/v2/maps/sdk.js?appkey=54ee9989a4bb5b14eab732e07bd561cc"></script>
	</body>
</html>
```
### `index.html` 파일에 `<script>` 태그만 추가해주면 되는데
##### 외부 JS 파일이 필요한 컴포넌트에서만 import 되는게 아니라서 비효율적임<br>그래서 `index.html` 대신 React 컴포넌트에서 script 태그를 동적으로 삽입할거임

<br><br>

# React 컴포넌트에서 script 태그 삽입
### `useEffect` 훅을 써서 script를 넣어주면 된다.
```javascript
import {useEffect} from 'react';

function CompoenentEx() {
	useEffect(()=> {
		const script = document.createElement("script");
		script.src = "scriptSrc";
		script.async = true;
		document.body.appendChild(script);
	});
	return(); 
}
```
#### 위 예시처럼 js로 html을 제어해서 scirpt태그를 동적으로 넣어주면 된다.

### 컴포넌트가 여러번 사용되면 body에 sciprt태그 계속 들어가는거 아님?

#### 그래서 조건문 걸어서 해당 스크립트 태그가 존재하는지 확인해서 있으면 리턴해야됨
```javascript
function ComponentEx(){
	useEffect(()=> {
		// 해당 src 값을 가진 sciprt 태그가 존재하면 useEffect 함수 종료함
		if( document.querySelector('sciprt[src="scirptSrc"]')){
			return;
		}
		const script = document.createElement("script");
		script.src = "scriptSrc";
		script.async = true;
		document.body.appendChild(script);
	});
}
```

