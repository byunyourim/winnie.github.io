---
title: try-catch 와 try-with-resources
author: winnie
date: 2024-06-10 14:10:00 +0800
categories: [Java]
render_with_liquid: false
---


## try-catch-finally

try-catch-finally 문은 많이 들어보았을 것이다.
try-catch-finally 문은 자바7 이전에 나온 예외 처리 구문으로 모든 객체를 포함할 수 있다.
사횽 후 반납을 해주어야 하는 자원들은 Closeable 인터페이스를 구현하고 있으며 사용 후 close 메서드를 직접 호출하여 반납해야 한다.

자원 반납이 이루어지지 않는 경우 메모리 누수가 생길 수 있으며 다른 프로세스, 스레드에 영향을 줄 수 있습니다. 따라서 finally 블록 내에서 자원을 해제하도록 해야합니다.

<script src="https://gist.github.com/byunyourim/149d0d35b071e490779cd5ea11bec579.js"></script>

자원을 반납하는 추가적인 코드가 필요하며 실수나 에러로 자원을 반납하지 못하는 경우가 생길 수 있습니다.

이러한 문제를 해결하기 위해 자바 7 부터 자원을 자동으로 반납해주는 try-with-resources 문법이 도입되었습니다.

### try-with-resources ?

try-with-resources 는 Java7 부터 도입된 자원 관리를 위한 구문입니다.
AutoClosable 인터페이스를 구현하고 있는 자원을 사용 후 자동으로 반납해주는 기능을 제공합니다.
이를 사용함으로써 가독성이 좋아지고 예외 발생 시 자원이 안전하게 해제될 수 있습니다.

구조
```
try (/*자원 초기화*/) {
  // 자원을 사용하는 코드
} catch (예외 타입 변수) {
  // 예외 처리 코드
}
```
주의할 점은 AutoCloseable 인터페이스를 구현한 객체만이 try 블록내에서 리소스로 사용될 수 있다는 것입니다. 주로 파일, 소켓, 데이터베이스 연결 등에 사용됩니다.

<script src="https://gist.github.com/byunyourim/3e1add1509cd417850aded31ff5ca3d4.js"></script>


Connection.java
<script src="https://gist.github.com/byunyourim/f339136bca1017050e4238589b4e780a.js"></script>

## Closeable과 AutoCloseable의 관계
기존의 Closeable 에 부모 인터페이스 AutoCloseable 를 추가한 형태입니다.
Closeable은 AutoCloseable을 확장한 하위 인터페이스입니다. 따라서 Closeable은 AutoCloseable이 제공하는 모든 기능을 포함합니다. 이러한 구조로 기존에 구현된 자원 클래스 모두 try-with-resources 를 사용할 수 있습니다.


## 정리
- `try-catch` 문에서는 예외가 발생할 수 있는 모든 코드가 포함될 수 있으며, 특정 객체 타입에 제한되지 않습니다.

- `try-with-resources` 문에서는 AutoCloseable 를 구현한 객체만을 사용할 수 있으며, 구문을 사용하면 리소스 누수를 방지할 수 있어 자원 관리에 매우 유용합니다.

