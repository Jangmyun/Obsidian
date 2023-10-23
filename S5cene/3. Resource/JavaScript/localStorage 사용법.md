## Local Storage 사용법
### local storage에 데이터 저장
```js
loclaStorage.setItem('key', 'value');
```
### local storage에서 데이터 가져오기
```js
localStorage.getItem('key');  // 'value'
```
### local storage에서 데이터 삭제
```js
localStorage.removeItem('key');
```
<br>
### 데이터를 수정하는 문법은 없어서 데이터를 직접 꺼내서 수정하고 다시 저장하는 방법을 사용해야 함

## JSON 변환을 통한 array / object 자료형 저장
### js 자료형 => json 변환 : `JSON.stringify(obj)`
```js
let obj = {name : 'kim'};
localStorage.setItem('obj', JSON.stringify(obj));
```
### json => js 자료형 변환 : `JSON.parse(json)`
```js
let obj2 =localStorage.getItem('obj');
let obj2 = JSON.parse(obj2); 
```