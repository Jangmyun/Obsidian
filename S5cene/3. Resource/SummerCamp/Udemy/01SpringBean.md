# Spring Bean 및 Spring 설정

Launch a Spring Context
Configure the things that we want Spring to manage

###### `@Configuration` Annotation으로 Bean 메서드를 선언함을 나타냄
```java
// HelloWOrldConfiguration.java
import org.springframework.context.annotation.Configuration;  
  
@Configuration  
public class HelloWorldConfiguration {  
	@Bean  
	public String name(){  
	    return "MyName";  
	}
}
```

```java
// App02HelloWorldSpring.java
package com.example.spring_study;  
  
import org.springframework.context.annotation.AnnotationConfigApplicationContext;  
  
public class App02HelloWorldSpring {  
    public static void main(String[] args) {  
        // 1. Launch a Spring Context  
        var context = new AnnotationConfigApplicationContext(HelloWorldConfiguration.class);  
  
        // 2. Configure the things that we want Spring to manage - @Configuration  
		// @Configuration - HelloWorldConfiguration
		// @Bean 을 통한 name 메서드 선언

		// 3. Spring에 의해 관리되는 Bean 값 가져오기
        System.out.println(context.getBean("name"));
  
    }  
}
```

##### `@Bean(name = "customName")`으로 Bean 이름 변경가능
```java
// HelloWOrldConfiguration.java
import org.springframework.context.annotation.Configuration;  
  
@Configuration  
public class HelloWorldConfiguration {  
	@Bean(name = "name2")
	public String name(){  
	    return "MyName";  
	}
}
```

##### `context.getBean`대신 클래스 전달 가능
 ```java
// App02HelloWorldSpring.java
package com.example.spring_study;  
  
import org.springframework.context.annotation.AnnotationConfigApplicationContext;  
  
public class App02HelloWorldSpring {  
    public static void main(String[] args) {  
        var context = new AnnotationConfigApplicationContext(HelloWorldConfiguration.class);  
        System.out.println(context.getBean(Address.class));
  
    }  
}
```

#### String Bean 간 관계
##### 1. 메서드 호출
```java
package com.example.spring_study;  
  
import org.springframework.context.annotation.AnnotationConfigApplicationContext;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
record Person (String name, int age) {};  
record Address(String firstLine, String city ) {}  
  
@Configuration  
public class HelloWorldConfiguration {  
  
    @Bean  
    public String name(){  
        return "MyName";  
    }  
  
    @Bean  
    public int age(){  
        return 22;  
    }  
  
    @Bean  
    public Person person() {  
        Person person = new Person("Tom" , 25);  
        return person;  
    }  
  
    @Bean  
    public Person person2MethodCall() {  
        return new Person(name(),age());  
    }  
  
    @Bean(name = "address2")  
    public Address address() {  
        Address address = new Address("Baker", "London");  
        return address;  
    }  
}
```

##### 2. 자동 기존 Bean 객체 주입
```java
@Bean  
public Person person3Parameters(String name, int age, Address address2) {  
    return new Person(name, age, address2);  
}
```


