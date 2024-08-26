# Linked List

## 구현
###### List node 와 Data node 구분하여 구현

#### Data node를 위한 class선언
```c++
class node {
	public:
		string name;
		double score;
		node *link;
		void set_data(string s, double n);
};

void node::set_data(string s, double n){
	name = s;
	score = n;
}
```
#### List node를 위한 class 선언
```c++
class my_list {
	node *head, *tail;
public:
	my_list();
	void add_to_head(node t);
	void add_to_tail(node t);
	node delete_from_head();
	int num_node();
	bool list_empty();
};
my_list::my_list(){
	head = NULL;
	tail = NULL;
}
```

### List 연산 구현 
#### add_to_head()
1. ##### Dynamic memory 로부터 node 공간 확보 `node *p;` `p= new node;` 
2.  ##### Node에 데이터값 저장 `(*p)=t;`
3. ##### Link를 조정하여 list에 연결 `p->link = head;` `head = p;`
4. ##### List의 empty 체크와 tail처리

```cpp
void my_list::add_to_head(node t){
	node *p;
	p = new node;
	(*p) = t;
	head = p;
	if( tail == NULL){
		tail = p;
	}
}
```

