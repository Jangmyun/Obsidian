# Java 2-2

## Chapter 2
### 2.1 Var and Expressions
##### variable - a named location to store data / a container for data
```java
public class EggBasket {
	public static void main(String[] args){
		int numberOfBaskets, eggsPerBasket, totalEggs;
	}
}
```

#### primitive data types
##### the simplest types/ values only no methods/ begin with a lowercase letter
#### class types
##### type for class( an object) / both data and method/ begin with a uppercase letter

```java
import java.util.Scanner;

public class FirstProgram {
	public static void main(String [] args){
		int n1, n2;
		Scanner keyboard = new Scanner(System.in);
	}
}
```

##### class = 붕어빵 틀 / object = 붕어빵

#### Identifiers
##### cannot be reserved words / cannot have a digit for the first character / no official length limit / are case sensitive => `junk` `JUNK` `Junk` are three valid and different identifiers/ no spaces or dots

#### Naming Conventions
#####  always use meaningful names / use only letters and digits/ capitalize interior words in multi-word names/ names of classes start with an uppercase letter / 

#### keyword & reserved word
##### keyword : a word of a programming language that is special only in certain context
##### reserved word: a special word of a programming language that cannot be used as a name

#### primitive numberic data type
##### integer - byte 1bytes  /  short 2bytes /int 4bytes /long 8bytes
##### floating-point number - float 4bytes / double 8bytes

#### `char` Data type
##### the char data type stores a single printable character / single quotes / x bytes

#### Assignment Compatibility
##### 