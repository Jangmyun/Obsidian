# Queue
##### First In First Out (FIFO) 특성을 갖는 linear list

##### Linear list의 한쪽 끝 (rear) 에서 insert가 이루어지고 다른 끝 (front)에서 delete가 이루어짐

#### Array Queue 문제점:
##### Array 끝위치에 rear가 도달하면 delete가 이루어진 빈 공간 사용 불가능
#### 해결 = circular queue
##### `rear = (rear+1) % Q_SIZE;`
##### `front = (front+1) % Q_SIZE;`
##### front / rear 값 증가시 끝 원소 도달하면 첫 원소로 이어짐

#### Circular queue 문제점:
##### Array 전체 공간이 다 채워지면 front == rear 라서 empty랑 똑같아짐
#### 해결 :
##### 마지막 빈원소 사용 안함 ( front직전 한 원소가 비어있는 상태를 full로 판정)

### 구현:

```cpp
my_queue::my_queue(){
	front = 0;
	rear = 0;
}
```

#### insert
```cpp
void my_queue::insert_q(int x){
	if(!queue_full()){
		q[rear] = x;
		rear = (rear+1) % Q_SIZE;
	}
}
```

#### delete
```cpp
int my_queue::delete_q(){
	int tmp;
	if(!queue_empty()){
		tmp = q[top];
		front = (front+1) % Q_SIZE;
		return (tmp); 
	}
}
```