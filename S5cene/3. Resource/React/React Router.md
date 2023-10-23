# React #라우터 #Router 
## 설치
```bash
$ npm install react-router-dom@6
```
<br><br>

## 세팅
```js
// index.js
import {BrowserRouter} from 'react-router-dom';

const root = ReactDOM.createRoot(document.getElementById('root')); 
root.render( 
	<React.StrictMode> 
		<BrowserRouter> 
			<App /> 
		</BrowserRouter> 
	</React.StrictMode> );
```
### `<BrowserRouter>` 태그로 `<App>` 태그 감싸기

## 라우터로 페이지 구분
```javascript
import {Routes, Route, Link} from 'react-router-dom';

function App(){
	return(
		<Routes>
			<Route path='/pagePath' element={ <div></div> } />
		</Routes>
	);
}
```

1. ### `Routes`, `Route` import해주기
2. ### ``<Routes>`` 태그 안에 ``<Route>`` 태그 작성
3. ### `path='경로' element={페이지에 띄울 html 태그}` 

<br>

## 페이지 이동 버튼

### `<Link>` 태그 사용하면 됨
```javascript
<Link to='경로'></Link>
```