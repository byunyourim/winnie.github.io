---
layout: post
title: 어노테이션 RequiredArgsConstructor, AllArgsConstructor
categories: Spring
---
프로젝트 진행 중 계속해서 의존성 주입 문제가 생겼다.

도대체 뭘까..

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

둘 다 Lombok 에서 제공하는 어노테이션으로 코드를 간결하게 작성할 수 있도록 도와주는 역할을 합니다.




## @RequiredArgsConstructor
클래스의 final로 선언된 필드나 @NonNull 로 표시된 필드를 기반으로 생성자를 생성합니다.
생성자는 이러한 필드들을 매개변수로 받아 초기화합니다.
```java
@RequiredArgsConstructor
public class User {
    private final String name;  // final로 선언
    private final int age;
    
    
    /* 아래와 같은 생성자가 자동으로 생성됩니다.
    public User(String name, int age) {
      this.name = name;
      this.age = age;
    }
     */
}
```
* 즉, 초기화되지 않은 final 필드의 생성자를 생성합니다.



## @AllArgsConstructor
모든 필드를 기반으로 생성자를 생성합니다.
final로 선언되지 않은 필드도 포함하여 모든 필드를 매개변수로 받는 생성자를 생성합니다.
```java
@AllArgsConstructor
public class User {
    private String name;
    private int age;
    
    /* 아래와 같은 생성자가 자동으로 생성됩니다.
    public User(String name, int age) {
      this.name = name;
      this.age = age;
    }
     */
}
```



## @Value 와 @RequiredArgsConstructor?
@Value 이 뭘까?


final 로 선언한 필드에 @Value를 사용하면?
스프링이 빈을 생성할 때 해당 필드를 초기화할 수 없기 때문에 에러가 발생합니다.
@Value 을 사용할 때는 생성자 인젝션을 사용하지 않기 때문에 @RequiredArgsConstructor 를 사용하여
생성자를 자동을 생성할 수 없습니다.


## @Value 와 @AllArgsConstructor?
스프링은 빈을 초기화 할 때 @Value 어노테이션을 사용하여 외부 프로퍼티 파일에서 값을 주입할 수 있습니다.
이것은 생성자 인젝션을 지원하지 않습니다.
따라서 @AllArgsConstructor을 사용할 때는 스프링이 @Value 어노테이션을 통해 초기화할 수 없는 필드에 대해서는 생성자를 자동으로 생성하지 않습니다. 
이로 인해 apiKey, apiSecret, fromNumber 필드는 @Value 어노테이션을 통해 초기화할 수 없어서 에러가 발생합니다.
