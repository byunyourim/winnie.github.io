---
layout: post
title: Lambda 와 함수형 인터페이스
categories: Java
---

<br>

람다 표현식은 **자바의 메서드 하나의 식으로 간결하게 표현한 것** 이다.

<br>

이전에는 자바에서 메서드를 표현하려면 클래스를 정의해야 했다. 하지만 자바 8 부터 람다식을 통해 메서드의 이름과 반환값을 
생략할 수 있고, 이를 변수에 넣어 코드를 간결하게 한다.


```java
int add (int a, int b) {
    return a + b; 
}


// 이는 아래와 같이 변경할 수 있다.
(a, b) -> a + b;
```

<br>

**(매개변수) -> {함수 본문}** 의 형태로,

매개 변수가 없는 경우 () 를 사용하고, 매개 변수가 하나 인 경우 괄호를 생략할 수 있으며, 
함수의 본문이 하나인 경우 중괄호와 return을 생략할 수 있다.  



```java
// 매개변수가 없고, 표현식이 하나
() -> System.out.println("hello"):

// 매개 변수가 하나
x -> x * x;

// 매개변수가 여러 개이고, 본문이 여러 줄
(x, y) -> {
    int sum = x + y;
    return sum;
}
```  



<br><br><br>

## 람다 표현식의 특징  

### 1. 간결하다
람다 표현식을 사용하면 코드가 간결해진다.
```java
// 익명 클래스로 Runnable 구현
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello, World!");
    }
};

// 람다 표현식으로 Runnable 구현
Runnable r2 = () -> System.out.println("Hello, World!");
```


<br>


### 2. 익명성
람다 표현식은 익명함수로, 함수의 이름을 정의하지 않고 사용할 수 있다.  

> ##### 익명 함수란?
> 이름이 없는 함수로, 익명 함수는 모두 일급 객체이다.
> 일급 객체인 함수는 변수처럼 사용이 가능하며, 매개변수로 전달이 가능하다.  



<br>


### 3. 타입 추론
람다 표현식에서는 컴파일러가 타입을 추론할 수 있기 때문에, 매개변수의 타입을 명시적으로 지정하지 않아도 된다.  
```java
// 타입 명시
Function<String, Integer> stringLength1 = (String s) -> s.length();

// 타입 추론
Function<String, Integer> stringLength2 = s -> s.length();
```


<br>


### 4. 클로저 지원
자신이 선언된 스코프의 변수에 접근할 수 있다.
```java
public class LambdaExample {
    public static void main(String[] args) {
        int number = 10;  
        Runnable r = () -> System.out.println(number);

        r.run();  // 출력: 10
    }
}
```

<br>


### 5. 고차 함수 지원
람다 표현식은 고차 함수를 지원한다. 
고차 함수는 다른 함수를 매개변수로 받거나, 함수를 반환하는 함수이다.

람다식을 이용하면, 고차 함수를 쉽게 정의하고 사용할 수 잇따.

람다 표현식을 사용하여 코드를 더 간결하게 할 수 있다. filter 메서드를 이용해 조건을 나타내는 람다 표현식을 매개변수로 받아 필터링을 쉽게 구현할 수 있다.

또한 이런 filter 메서드는 여러 상황에서 재사용할 수 있다. 조건만 다르게 해서 재사용할 수 있기 때문에, 코드의 중복이 줄고 유지보수가 쉽다.


컬렌션의 필터링, 매핑, 집계 등 여러 작업을 람다를 이용해서 간결하게 처리할 수 있으며, 재사용성을 높인다.

```java
import java.util.function.Function;

public class LambdaExample {
    public static void main(String[] args) {
        Function<Integer, Function<Integer, Integer>> add = x -> y -> x + y;

        System.out.println(add.apply(2).apply(3));  // 출력: 5
    }
}
```


<br>


### 6. 병렬 처리 가능
```java
import java.util.Arrays;
import java.util.List;

public class LambdaExample {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);

        // 병렬 처리
        list.parallelStream()
            .filter(n -> n % 2 == 0)
            .forEach(System.out::println);
    }
}
```


<br>


### 7. 함수형 인터페이스 구현
함수형 인터페이스는 하나의 추상 메서드를 갖는 인터페이스이다.

```java
public class FunctionalInterfaceExample {

   public static void main(String[] args) {

      MyFunctionInterface add = (a, b) -> a + b;
      System.out.println(add.execute(5, 3));
   }
}

// 함수형 인터페이스 정의
@FunctionalInterface
interface MyFunctionInterface {
   int execute(int x, int y);
}
```  
  

### 인터페이스에 추상 메서드가 여러 개라면?
함수형 인터페이스는 추상 메서드를 하나만 갖는 인터페이스이다. 이 추상 메서드는 람다표현식을 사용하여 구현할 수 있다.

추상 메서드가 여러 개인 인터페이스의 경우 함수형 인터페이스가 아니기 때문에 람다 표현식으로 구현할 수 없다.  
만약 여러 개의 메서드가 필요한 경우에는 각각의 메서드를 분리하여 함수형 인터페이스로 만들어야 한다.



```java
@FunctionInterface
interface FunctionInterface {
   void execute();
}

public class Example {
   public static void main(String[] args) {
      FunctionInterface function = () -> Systemout.println("hello");
      function.execute();
   }
}
``` 
  
<br>

## 8. 기존 인터페이스와 호환

자바에서 제공하는 함수형 인터페이스에는 **Runnable, Function, Predicate, Consumer, Supplier, Callable** 등이 있다.
  
<br>

### ◾️ Runnable
스레드를 실행하기 위한 함수형 인터페이스

### ◾️ Function<T, R>
- T타입의 매개변수를 입력 받아, R 타입으로 반환하는 함수형 인터페이스
- **R apply (T t)**
```java
Function<String, Integer> lengthFunction = s -> s.length;
int length = lengthFunction.apply("Hello");
```
입력을 받아서 출력으로 변환하며, 주로 변환 작업에 사용된다.

### ◾️ Predicate< T >
- T타입의 매개변수를 입력 받아, 조건 검사 후 boolean 값을 반환하는 함수형 인터페이스
- **boolean test(T t)**
```java
Predicate<String> isEmpty = s -> s.isEmpty();
boolean result = isEmpty.test(""); // true
```
입력값에 대해 boolean 값을 반환하며, 조건 검증에 사용한다.

### ◾️ Consumer< T > 
- T타입의 입력을 받아 동작을 수행한 후, 결과를 반환하지 않는 함수형 인터페이스이다.
- **void accept(T t)** 
```java
Consumer<String> str = i -> System.out.println(i);
print.accept("Hello, World!");
```
입력을 받아서 동작을 수행하며, 결과를 반환하지 않는다.

### ◾️ Supplier< T > 
- 매개변수 없이 T타입의 값을 반환하는 함수형 인터페이스로 객체를 생성하거나 값을 제공한다.
- **T get()**
```java
Supplier<Integer> randomSupplier = () -> (int) (Math.random() * 100);
int randomValue = randomSupplier.get(); // 임의의 정수 반환
```
매개 변수를 받지 않고, 값을 생성하거나 제공한다.


