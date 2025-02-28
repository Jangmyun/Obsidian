	#### #canvas
# 기본 세팅
```javascript
const canvas = document.getElementById('canvas'); // 캔버스요소 찾기
const ctx = canvas.getContext('2d');  // ctx getContext 작성
```
#### 변수 `ctx` 설정해주면 `ctx`를 통해 렌더링 컨텍스트에 접근할 수 있다.

## canvas 지원하는 브라우저인지 확인하기
```javascript
const canvas = document.getElementById("canvas");
if(canvas.getContext){
	const ctx = canvas.getContext('2d');
	// canvas 지원할때 코드
} else {
	// canvas 지원 안할때 코드
}
```
# canvas grid
![Canvas grid with a blue square demonstrating coordinates and axes.](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Drawing_shapes/canvas_default_grid.png)

# 사각형 그리기
### canvas에서는 선과 사각형만 그릴 수 있음. <br>다른 도형은 이 두개를 조합해서 만듬

+ #### `fillRect(x , y , width, height)` - 속이 채워진 사각형
+ #### `strokeRect(x,y,width,height)` - 사각형 선
+ #### `clearRect(x,y,width,height)` - 사각형의 영역을 지움

# 경로 (path, 선) 그리기
## 그리는 순서
1. #### `beginPath()`로 새로운 경로 생성
2. #### drawing 명령어 사용해서 경로에 저장
3. #### path 생성이 끝나면 선을 잇거나 색 채울 수 있음

## 경로 명령어
+ #### `closePath()` - 가장 최근 경로에서 시작점까지 선분 잇고 경로 닫음
+ #### `stroke()` - 점 찍은 곳 이어서 선으로 만듬
+ #### `fill()` - 이은 경로의 영역을 색칠함

### 삼각형 그리는 예제
```javascript
fucntion drawTriangle(){
	const canvas = document.getElementById("canvas");
	if(canvas.getContext){
		const ctx = canvas.getContext('2d');
		ctx.beginPath();
		ctx.moveTo(75,50);
		ctx.lineTo(100, 75);
		ctx.lineTo(100,25);
		ctx.fill();
	}
}
```

### `moveTo(x, y)`
#### 그리기 시작할 위치 지정

### `lineTo(x,y)`
#### 최근 위치에서 x,y 위치로 선을 그음


# 호 , 원 (arc)

## arc 메서드
+ ### `arc(x, y, radius, startAngle, endAngle, counterclockwise)`
	+ ##### `x`와 `y`는 원의 중심점<br>`radius`는 반지름<br>`startAngle`과 `endAngle`은 호의 시작점과 끝점 (radian)<br>`counterclockwise`는 boolean값으로 `true`면 시계반대방향
+ ### `arcTo(x1, y1, x2, y2, radius)`



