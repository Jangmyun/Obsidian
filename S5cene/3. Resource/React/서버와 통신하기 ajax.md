#### get 요청은 브라우저 주소창에 치면 되는데 주소창에 GET요청하면 페이지 새로고침 됨
#### 이럴 때 ajax 쓰면 새로고침 없이 데이터 가져올 수 있음

#### 버튼 누르면 새로운 상품 데이터 가져오기 해보자

```zsh
$ npm install axios
```
##### axios로 ajax 사용할거라서 axios 설치해주면 됨
```js
import axios from 'axios';
```
##### axios import 해주고
```jsx
<button onClick={()=>{
	axios.get('데이터받아올 경로')
	.then((받아온데이터)=>{ /*받아온데이터로 실행할 코드*/ });
}}></button>
```
### `axios.get('링크').then((결과)=>{결과.data})` 이런식으로 적으면 데이터 가져올 수 있음
