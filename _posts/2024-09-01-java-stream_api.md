---
layout: post
title: Stream API
categories: Java
---

## Stream API
**데이터 처리를 간결하고 효율적으로 할 수 있도록 도와주는 API**  

Java8 부터 도입되어 컬렉션의 데이터를 함수형으로 처리할 수 있도록 해준다.
데이터 소스 (컬렉션, 배열, I/O 채널 등)에서 파이프라인 방식으로 연속적인 연산을 수행할 수 있다.


<br>


### 특징
#### 1. 불변성
스트림은 기존의 데이터를 변경하지 않고, 새로운 스트림을 생성하여 작업을 수행한다.
  
#### 2. 지연 연산 Lazy Evaluation
스트림에는 중간 연산과 최종 연산이 있는데, 최종 연산이 호출될 때까지 중간 연산이 실제로 수행되지 않는 것을 말한다.
지연 연산으로 불필요한 계산이 줄어들어 성능이 최적화 될 수 있다.

#### 3. 파이프라이닝 Pipelining
여러 중간 연산을 연결하여 하나의 파이프라인을 구성할 수 있다.
파이프라인은 한 번의 최종 연산을 통해 처리 될 수 있다. 
   
```java
List<String> fruits = Arrays.asList("apple", "banana", "orange");

Listt<String> result = fruits.stream()
                             .filter(name -> name.length() > 5)
                             .map(String::toUpperCase)
                             .sorted()
                             .collect(Collectors.toList());
```

#### 4. 함수형 프로그래밍 지원
   함수형 인터페이스와 람다 표현식을 활용하여 함수형 프로그래밍을 지원하며, 이를 통해 코드를 간결하게 할 수 있다.

#### 5. 다양한 데이터 소스 지원
   Collection 인터페이스를 구현한 클래스뿐 아니라 배열, 파일 등 다양한 데이터를 사용할 수 있다.

#### 6. 유형 스트림 지원
   기본형 타입을 위한 스트림을 별도로 제공하여 박싱 비용을 줄일 수 있다.



<br><br>



## Lazy Evaluation
Java Stream API에서 중간 연산이 최종 연산이 호출될 때까지 실제로 수행되지 않는 것을 말한다.
  
  

### 구현 방식
#### 1. 스트림을 생성한다.
   스트림은 데이터의 요소들의 연속적인 데이터 흐름으로 스트림을 생성하면, 데이터를 일관된 방식으로 처리할 수 있는 파이프 라인을 만들 수 있다.
   
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
Stream<String> nameStream = names.stream(); // 스트림 생성
        
String[] nameArray = {"Alice", "Bob", "Charlie"};
Stream<String> nameStream = Arrays.stream(nameArray); // 스트림 생성
        
Stream<String> nameStream = Stream.of("Alice", "Bob", "Charlie"); // 스트림 생성
```


#### 2. 중간 연산을 정의한다.
   중간 연산(filtter, map, sorted)이 스트림에 연결된다. 각 중간 연산은
   스트림을 변환하는 새로운 스트림을 반화하며, 실제 데이터는 처리되지 않는다.
   중간 연산은 다음 연산을 위한 연산 체인을 설정한다.  

#### 3. 최종 연산을 호출한다.
   최종 연산(collect, forEach, reduce)이 호출되면 스트림의 데이터가 실제로 처리된다.  
   이때 스트림의 중간 연산이 실행된다.


<br>


#### 장점
- 스트림은 필요한 데이터만 처리하기 때문에 성능을 향상시킨다.

- 중간 연산이 지연되어 필요한 시점에 처리되기 때문에 메모리 사용이 줄어든다.

- 여러 중간 연산이 연결된 경우, 하나의 파이프라인에서 순차적으로 처리되기 때문에 불필요한 중간 결과가 생성되지 않는다.
  모든 연산이 최종 연산이 호출될 때 한 번에 처리된다.

#### 단점
- 지연 평가로 중간 연산이 언제 실행될지 모르기 때문에 디버깅이 복잡해질 수 있다.

- 오류가 발생할 경우, 실제 실행 시점에서 오류가 발생하기 때문에 중간 연산에서 발생한 문제를 추적하기 어렵다.



<br><br>



#### 스트림은 데이터를 변경하지 않는다? 왜?
스트림은 연산 과정에서 원본 데이터를 복사하는 것이 아닌, 참조된 데이터를 바탕으로 필요한 연산을 수행하여 새로운 데이터를 생성한다.

한 번 사용된 이후 재사용할 수 없는 일회성 객체이며, 이를 통해 원본 데이터의 불변성을 유지할 수 있다.

또한 참조 변수에 저장하지 않고 곧바로 연산을 수행할 수 있다. 즉, 스트림 객체를 별도로 저장하지 않고 사용하는 것이 가능하다.

```java
List<String> names = Arrays.asList("apple", "banana", "coke");

names.stream()          // 스트림 생성
     .filter(name -> name.startsWith("a"))   // 중간연산 
     .filter(String::toUpperCase)   // 중간연산 
     .forEach(System.out::println);          // 최종연산
```
위 코드에서는 names.stream()으로 스트림을 생성한 후, 이를 Stream<String> stream = names.stream(); 같은 참조 변수에 할당하지 않고 
바로 연산(filter, forEach)을 수행한다.

한 번 사용하면 다시 사용할 수 없는 일회성 객체로, 최종 연산이 수행되면 더 이상 해당 스트림을 사용할 수 없다.
만약 동일한 연산을 다시 수행하려면 names.stream()으로 새로 스트림을 생성해야 한다.  

스트림의 파이프라인은 최종 연산이 호출될 때 한 번에 실행되고 그 후에 스트림은 종료된다.
다시 사용하려고 하면 IllegalStateException이 발생한다.

```java
Stream<String> stream = names.stream().filter(name -> name.startsWith("A"));
stream.collect(Collectors.toList()); // 최종 연산
stream.collect(Collectors.toList()); // IllegalStateException 발생
```


<br><br><br>


## Stateful & Statefuless

#### Stateful 연산 (Stateful Operations)

Stateful Stream 은 스트림의 중간 연산 중 이전 단계의 결과를 바탕으로 현재 연산을 수행하는 것을 말한다.  
전체 스트림의 상태를 추적하기 때문에 성능이 떨어질 수 있다.
sorted(), distinct() 가 있으며, 전체 데이터의 상태를 고려해야 하기 때문에 연산 중 일시적으로 전체 데이터를 저장하거나 처리해야 한다.

연산을 수행할 때 스트림의 요소들 간의 상태나 순서를 유지하거나 추적해야 하는 연산  
연산은 스트릠의 모든 요소를 고려하여 상태를 유지하거나 변경할 필요가 깅ㅅ다.
상태를 추적해야 하기 때문에 메모리 사용량이 증가하고 성능이 저하될 수 있다.
요소 간의 관계를 고려하거나, 스트림 전체를 정렬, 필터링해야 할 때 사용한다.

- sorted 
스트림의 모든 요소를 정렬하기 위해 전체 스트림을 고려한다. 요소를 정렬하려면 전체 데이터를 메모리에
  로드하고 정렬해야 하기 때문에 상태를 유지한다.
  
- distinct
중복을 제거하기 위해 스트림의 모든 요소를 기억하고 비교해야 하기 때문에 상태를 유지한다.


<br>


#### Stateless 연산 (Stateless Operations)
연산을 수행할 때 스트림의 요소 간의 상태를 유지하지 않고, 각 요소를 독립적으로 처리하는 연산  
요소 간의 관계를 고려하지 않는다.  

연산은 각 요소를 독립적으로 처리하며, 이전 또는 다른 요소의 상태에 의존하지 안흔다.
상태를 유지하지 않기 때문에 메모리 사용량이 적고 성능이 좋으며, 각 요소를 독립적으로 처리하여 결과를 생성한다.


- filter()
각 요소를 개별적으로 검사하여 조건에 맞는 요소만 남기며, 다른 요소의 상태나 관계를 고려하지 않는다.


- map()
각 요소를 다른 형태로 변환하며, 이 과정에서 요소 간이 관계를 고려하지 않는다.



<br><br><br>



## 중간 연산과 최종 연산

<br>

### 중간 연산
스트림 파이프라인에서 스트림을 변환하거나 필터링한다.
스트림을 변경하지 않고 새로운 스트림을 반환하며, 지연 평가를 통해 최종 연산이 호출될 때까지 실제로 수행되지 않는다.
연속해서 호출할 수 있으며, 스트림 파이프라인을 구성할 수 있다.
- filter(Predicate<T>) : 조건에 맞는 요소만 필터링하여 새로운 스트림 반환
```java
List<String> names = Arrays.asList("amy", "jenny", "bob", "jun");
Stream<String> filteredStream = names.streaam()
                                     .filter(name -> name.startsWith("j"));
```

- map(Function<T, R>) : 각 요소를 다른 형태로 변환하여 새로운 스트림 반환
```java
List<String> names = Arrays.asList("amy", "jenny", "bob", "jun");
Stream<String> upperCaseStream = names.stream()
                                      .map(String::toUpperCase);
```

- sorted(Comparator<T>) : 스트릠의 요소를 정렬하여 새로운 스트림 반환
```java
List<Integer> numbers = Arrays.asList(5, 3, 1, 4, 2);
Stream<Integer> sortedStream = numbers.stream().sorted();
```


<br>


### 최종 연산
스트림 파이프라인의 결과를 생성한다.
스트림의 요소를 실제로 처리하여 결과를 반환하며, 스트림이 종료되고 더 이상 사용할 수 없다. 최종 연산이 호출되면, 중간 연산이 실행된다.

  
  
 
##### - forEach(Consumer< T >)
스트림의 각 요소에 대해 주어진 작업 수행 
```java
List<String> names = Arrays.asList("amy", "jenny", "bob", "jun");

names.stream().forEach(System.out::println);
```  
  
  

##### - collect(Collector<T, A, R>)  
스트림의 요소를 수집하여 컬렉션 또는 다른 형태의 결과 생성 
```java
List<String> names = Arrays.asList("amy", "jenny", "bob", "jun");

List<String> collectedNames = names.stream()
        .collect(Collectors.toList());
```  
  
  

##### - reduce(BinaryOperator<T>)  
스트림의 요소를 하나로 결합하여 단일 결과 생성  
```java
List<Integer> numbers = Arrays.asList(5, 3, 1, 4, 2);
int sum = numbers.stream()
        .reduce(0, Integer::sum);
```
