Iteration 1 : Tightly Coupled Java Code
+ GameRunner class
+ Game classes: Mario, SuperContra, Pacman etc

Iteration 2: Loose Coupling - Interfaces
+ GameRunner class
+ GamingConsole interface
	+ Game Classes: Mario, SuperContra, Pacman etc

Iteration 3: Loose Coupling - Spring Level 1
+ Spring Beans
+ Spring framework will manage objects and wiring

Iteration 4 : Loose Coupling - Spring Level 2
+ Spring Annotations
+ Spring framework will create , manage & auto wire object

## 스프링 프로젝트 생성
### [Spring initializr](https://start.spring.io/)
에서 Maven, java, Group, Artifact 설정하고 생성

![[Screenshot 2024-05-30 at 11.57.53 AM.png]]
main java - 모든 소스파일
main resources - 모든 설정 파일
test java - 테스트 코드

## Iteration 1 : Tightly Coupled Java Code

```java
// AppGamingBasicJava.java
package com.in28minutes.learn_spring_framework;

import com.in28minutes.learn_spring_framework.game.GameRunner;
import com.in28minutes.learn_spring_framework.game.MarioGame;

public class AppGamingBasicJava {
	public static void main(String[] args) {
		var marioGame = new MarioGame();
		var gameRunner = new GameRunner(marioGame);
		gameRunner.run();
	}
}
```

```java
//MarioGame.java
package com.in28minutes.learn_spring_framework.game;

  

public class MarioGame {

public void up() {

System.out.println("Jump");

}

public void down() {

System.out.println("Go into a hole");

}

public void left() {

System.out.println("Go back");

}

public void right() {

System.out.println("Accelerate");

}

}
```

```java
//GameRunner.java
package com.in28minutes.learn_spring_framework.game;

  

public class GameRunner {

MarioGame game;

public GameRunner(MarioGame game) {

this.game = game;

}

  

public void run() {

System.out.println("Running gmae: " + game);

game.up();

game.down();

game.left();

game.right();

}

}
```
### 강한 결합과 느슨한 결합 (Tight Coupling vs. Loose Coupling)
#### Coupling: "How much work is involved in changing something?"
##### 최대한 느슨한 결합을 사용해야한다. (적은 코드 변경으로 기능 변경 가능하게)

## Iteration 2: Loose Coupling - Interfaces



## Spring Framework 도입해서 Loose Coupling


JVM에서 스프링 컨텍스트 생성하고 

`import org.springframework.context.annotaion.Configuration;`
`@Configuration`

Spring에서 관리하는 것 = `Spring Bean` 
설정 클래스에서 메서드 정의해서 Spring Bean 생성 가능

