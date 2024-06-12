# Dynamic Data Structure and Generics

ArrayList 정의 및 사용법 알기
linked list 자료구조의 아이디어 설명하기 / 다루기
linked list 구조에서 inner class 사용하기
iterator 생성 및 사용 및 설명
generic type 정의하기

### ArrayList
자바 배열은 동적으로 변할 수 없지만 ArrayList는 런타임 동안 길이가 변할 수 있다.
배열보다 비효율적이고, 객체만 저장가능하고 primitive type은 안됨

구현: 배열을 사용함. 공간이 부족하면 늘린 배열에 기존 값을 복사한다.

원소는 end, beginning, in between items에 추가가능함
![[Screenshot 2024-06-12 at 19.19.25.png]]
  `import java.util.ArrayList`

```java
import java.util.ArrayList;
import java.util.Scanner;

public class ArrayListDemo {
	public static void main(String[] args){
		ArrayList<String> toDoList = new ArrayList <String>();
		System.out.println ("Enter items for the list, when prompted.");
		Scanner keyboard = new Scanner(System.in);
		boolean done = false;
		while(!done){
			System.out.println("Type an entry: ");
			String entry = keyboard.nextLine();
			toDoList.add(entry);
		}
	}

}
```

ArrayList 요소에 접근할 때는 `forEach`문 사용
`trimToSize()` 메서드는 메모리 save에 사용
ArrayList 복사는 `clone()` 메서드 사용

#### ArrayList = `parameterized class` (매개변수화된 클래스)
type 값을 매개변수로 가진다.

# Collections Framework

framework란? : 유용한 데이터 구조와 알고리즘을 담고있는 클래스와 인터페이스의 집합이다.

#### Collection Framework
1. Bags
	+ **unordered**, **dublicate**(중복) 허용
	+ known as **multisets**
	+ 제약이 가장 적고 collection에서 
2. Sets
	+ **unordered**, 중복 허용안함
	+ `{e1 ,e2, e3}` 처럼 표시
	+ sorted sets, ordered sets 도 있음
3. Lists
	+ **ordered**, **sequence**(요소가 0부터 indexed됨), **duplicate**중복 허용
4. Maps
	+ **unordered**, **key-value**쌍으로 이루어진 구조
	+ key를 통해 value값에 접근 (key 값은 고유해야 함)
	+ sorted map => key값에 따라 자동 정렬 (SortedMap interface)

#### Collection interface
Collection interface는 객체가 어떻게 추가되고 삭제되고 접근되는지 지정한다.

#### HashSet
객체 set을 저장하기 위해 사용
`ArrayList`처럼 데이터타입 명시를 위해 `<>` 사용
사용자 class를 답기 위해서는 `hashCode()`와 `equals()`  #오버라이딩 해야됨

#### Map Interface
unordered pair를 다루기 위해 사용
검색을 위한 key값과 value로 이루어짐

#### HashMap
key값으로 객체를 찾는 데이터베이스처럼 사용됨
`ArrayList`처럼 데이터타입 명시를 위해 `<>` 사용, **key과 object 타입 두 개 명시해야함**
사용자 class를 답기 위해서는 `hashCode()`와 `equals()`  #오버라이딩 해야됨

```java
HashMap<String, Integer> hm = new HashMap<>();

hm.put(key, value);
hm.get(key);
hm.clear();
hm.remove(key);
hm.containsKey(key);
hm.containsValue(value);
hm.isEmpty();
hm.size();
hm.keySet();  // key Set 리턴
hm.values();  // 모든 value값 collection으로 리턴
```

# #LinkedList
list의 head는 node가 아니다. (node의 주소값 참조)

1. Node Class가 linkedlist 클래스 밖에 있는 경우
	+ Linked List는 node 객체로 이루어져 있고, 각 노드는 data와 다음 노드를 가리키는 link로 구성
```java
public class ListNode{
	private String data;
	private ListNode link;

	public ListNode(){
		link = null;
		data = null;
	}

	public ListNode(String newData, ListNode linkValue){
		data = newData;
		link = linkValue;
	}
	// getter, setter
}

public class StringLinkedList {
	private ListNode head;
	public StringLinkedList(){
		head = null;
	}
	public int length() {
		int count=0;
		ListNode p = head;
		while(p != null){
			count++;
			position = position.getLink();
		}
		return count;
	}
	public void addNodeToStart(String addData){
		head = new ListNode(addData, null);
	}
	public void deleteHeadNode(){
		if(head!=null){
			head = head.getLink();
		}else {
			System.out.println("Deleting from an empty list");
			System.exit(0);
		}
	}
	public boolean onList(String target){
		return (Find(target)!= null);
	}
	private ListNode Find(String target){
		ListNode p = head;
		String dataAtPosition;
		while(p!=null){
			dataAtPosition = p.getData();
			if(dataAtPosition.equals(target)){
				return p;
			}
			p = p.getLink();
		}
		return null;
	}
	public void showList() {
		ListNode p = head;
		while(p != null){
			System.out.println(p.getData());
			p = p.getLink();
		}
	}
}
```

```java
public void addANodeToStart(String addData){
	head = new ListNode(addData, head);
}
```

new node는 head가 가리켰던 list start지점을 가리킨다.
head는 새로운 node로 변경됨

```java
public void deleteHeadNode() {
	if(head != null){
		head = head.getLink();
	}else {
		// error message
	}
}
```
node 첫부분 삭제하기

```java
private ListNode Find(String target){
	ListNode position;
	position = head;
	String DataAtPosition;
	while(position != null){
		dataAtPosition = position.getData();
		if(dataAtPosition.equals(target)){
			return position;
		}
		position = position.getLink( );
	}
	return null;
	// target 못찾으면 null
}
```

### Null Pointer Exception
+ java 프로그램이 `null`값을 참조하려 할 때 발생하는 exception
+ **NullPointerException**은 catch문 추가가 아니라 코드를 수정해야 하는 것


### Node Inner Class
```java
public class StringLinkedList {
	private ListNode head;
	// method for StringLinkedList
	private class ListNode {
		// define ListNode
	}
}
```

+ inner class 사용으로 LinkedList가 별도 파일에 의존하지 않기 때문에 완전해진다.
+ inner class를 `private`로 선언함으로서 Node가 외부에서 직접접근할 수 없어 **information hiding** 관점에서 안전하다.

### Linked List Data into an Array
```java
public String[] arrayCopy(){
	String[] a = new String[length()];
	ListNode p;
	p = head;
	int i=0;
	while(position != null){
		a[i] = p.data;
		i++;
		p = p.link; 
	}
	return a;
}
```

# Iterator
+ **객체 컬렉션이 객체를 순차적으로 탐색할 수 있게 하는 객체**
+ array의 경우 index 변수를 iterator로서 사용가능 `index++;`
+ linked list는 **node의 reference**를 iterator로 사용 `current = current.link;`

```java
public class StringLinkedListWithIterator{
	private ListNode head;
	private ListNode current;
	private ListNode previous;
	
	public StringLinkedListWithIterator() {
		head = null;
		current = null;
		previous = null;
	}
	
	public boolean moreTolterate() { // iteration이 끝나지 않았으면 true
		return current != null;
	}
	
	public void goToNext() {
		if(current != null){
			previous = current;
			current = current.link;
		}else if(head !=null){
			System.out.println("Iterated too many times or unitialize iteration");
			System.exit(0);
		}else {
			System.out.println("Iterating with an empty list");
			System.exit(0);
		}
	}
	
	public void addANodeToStart(String addData){
		head = new ListNode(addData, head);
		if(current == head.link && current!=null){
		// current가 이전 start node면
			previous = head;
		}
	}
	
	public void resetIteration() {
		current = head;
		previous = null;
	}
}
```

#### Other Method in the linked list with **iterator**
+ `getDataAtCurrent`
+ `moreToIterate()`  : return **boolean** value if iterator is not at the end of the list
+ `resetIteration` : move the iterator to the beginning
#### Adding a Node (After Current node)
+ `insertNodeAfterCurrent(String newData)`
+ newNode.link = current.link  / current.link = newNode;



### 12-3 복습- e