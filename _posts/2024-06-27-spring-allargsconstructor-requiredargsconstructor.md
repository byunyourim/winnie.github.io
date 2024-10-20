---
layout: post
title: 어노테이션 RequiredArgsConstructor, AllArgsConstructor
categories: [Spring]
---
프로젝트 진행 중 계속해서 의존성 주입 문제가 생겼다.

```java
@Service
@RequiredArgsConstructor
public class SmsSenderService {

    private final SmsCertification smsCertification;

    @Value("${coolsms.apikey}")
    private String apiKey;

    @Value("${coolsms.apisecret}")
    private String apiSecret;

    @Value("${coolsms.fromnumber}")
    private String fromNumber;
    
    // 코드 생략
}


```

@RequiredArgsConstructor 와 @AllArgsConstructor 를 알아보자

둘 다 Lombok 에서 제공하는 어노테이션으로 코드를 간결하게 작성할 수 있도록 도와주는 역할을 한다.  


<br><br>

### @RequiredArgsConstructor
클래스의 final로 선언된 필드나 @NonNull 로 표시된 필드를 기반으로 생성자를 생성한다.
```java
@RequiredArgsConstructor
public class User {
    private final String name;  // final로 선언
    private final int age;
    
    
    /* 아래와 같은 생성자가 자동으로 생성된다.
    public User(String name, int age) {
      this.name = name;
      this.age = age;
    }
     */
}
```

<br><br>

### @AllArgsConstructor
클래스의 모든 필드를 매개변수로 받는 생성자를 자동으로 생성한다.
final 필드를 포함한 모든 필드를 초기화하는 생성자가 생성되며, 이를 통해 final 필드를 반드시 초기화할 수 있다.
```java
@AllArgsConstructor
public class User {
    private String name;
    private int age;
    
    /* 아래와 같은 생성자가 자동으로 생성된다.
    public User(String name, int age) {
      this.name = name;
      this.age = age;
    }
     */
}
```

<br><br><br>

### @Value 와 @RequiredArgsConstructor?
@Value는 외부 프로퍼티 파일에서 값을 주입받는 데 사용되는데, 이 값은 런타임 시점에 결정된다. 

final로 선언된 필드는 한 번 초기화되면, 변경할 수 없기 때문에 값을 반드시 생성자에서 할당해야 한다.  
@RequiredArgsConstructor가 생성자를 만들 때, 이 생성자는 컴파일 시점에 생성되는데, 이때 final 필드는 생성자의 인자로 받아야 하기때문에, 컴파일러는 이 필드에 값을 초기화해야 한다.

final 필드는 생성자가 호출되기 전에 값이 할당되어야 하는데, @Value를 사용하면
@Value가 값을 주입하려고 할 때 이미 @RequiredArgsConstructor로 인해 생성자가 만들어진 상태이다. 따라서 필드를 초기화할 수 없기 때문에 오류가 발생한다.

결국, 컴파일 시점에서 생성자가 만들어지는 것과 런타임 시점에서 @Value가 값을 주입하려는 시도가 충돌하여 오류가 발생한다.

<br><br>
