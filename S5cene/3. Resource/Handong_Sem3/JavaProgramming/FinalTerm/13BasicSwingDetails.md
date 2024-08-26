Learn the basics of event driven programming
Learn to design and code a simple GUI includes buttons and text
Learn about the SWING classes 

### Intro
#### Swing :
+ 자바에 window interface를 가질 수 있도록 하는 라이브러리
+ **Java Foundation Classes (JFC)** 에 속한 라이브러리
+ **Abstract Window Toolkit (AWT)** 의 상위 버전이다.

#### GUI
+ Graphical - 창 메뉴 버튼 등등
+ User - 프로그램 사용하는 사람
+ Interface - 프로그램과 상호작용하는 방법
<br>
+ Window 
+ Menu
+ button

#### Event-Driven Programming
프로그램은 이벤트가 발생하기를 기다렸다가 응답한다. (마우스 클릭, 드래그 , 키보드 키)
+ **Firing an event**: 객체가 이벤트를 생성할 때 발생
+ **Listener**: 이벤트가 발생하기를 기다리는 객체
+ **Event Handler**: 이벤트에 응답하는 메서드!

![[Screenshot 2024-06-15 at 21.20.41.png]]

### Basic Swing Details
![[Screenshot 2024-06-15 at 21.26.44.png]]
+ **JFrame** 객체
+ **Content Pane** : 창 내부
+ **setSize(width, height)**
+ 리스너 등록
+ **setVisible()** 로 visivility 설정

![[Screenshot 2024-06-15 at 21.31.18.png]]
![[Screenshot 2024-06-15 at 21.31.27.png]]
**WindowAdapter** 상속해서 그 안에 있는 **windowClosing()** 사용

#### WindowAdapter VS. WindowListener

**WindowListener** Interface는 아래 7가지 메서드 implements해야함
1. `void windowOpened(WindowEvent e)`
2. `void windowClosing(WindowEvent e)`
3. `void windowClosed(WindowEvent e)`
4. `void windowIconified(WindowEvent e)`
5. `void windowDeiconified(WindowEvent e)`
6. `void windowActivated(WindowEvent e)`
7. `void windowDeactivated(WindowEvent e)`

**WindowAdapter** Class는 필요한 메서드만 오버라이드 가능

##### **setVisible** 메서드
boolean 타입 하나만 매개변수로 받음
window를 보이게 할지, 안보이게 할지 결정 


#### Example

```java
// class FirstWindow
import javax.swing.JFrame; 
import javax.swing.JLabel;

public class FirstWindow extends JFrame {
	public FirstWindow() {
		super();
		setSize(300, 200);
		JLabel myLabel = new JLabel("Please don't click that button!");
		getContentPane().add(myLabel);
		WindowDestroyer listener = new WindowDestroyer();
		addWindowListener(listener);
	}
}
```

```java
//class FirstWindowDemo
public class FirstWindowDemo{
	public static void main(String[] args){
		FirstWindow window1 = new FirstWindow();
		window1.setVisible(true);
	}
}
```

#### Adding Items to a **JFrame** window
+ `getContentPane().add(JLabel Object);`
+ `JLabel myLabel() = new JLabel("blah blah")`<br>`getContentPane().add(myLabel);`

![[Screenshot 2024-06-15 at 22.30.18.png]]![[Screenshot 2024-06-15 at 22.31.58.png]]\

13-2b 할차례
