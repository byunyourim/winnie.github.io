---
layout: post
title: Serialization
categories: Java
---

전송 계층에서 사용하는 두 가지 주요 프로토콜

# Serialization
<span style='background-color:#D0E4FC'>**객체를 바이트 스트림으로 변환하여 저장하거나 전송할 수 있게 하는 과정**</span>  
네트워크를 통해 객체를 전송하거나 파일에 저장할 때 사용됩니다.


<br>


# 직렬화 구현 방식
자바에서 직렬화를 구현하기 위해서는 java.io.Serializable 인터페이스를 사용합니다. 
직렬화할 객체의 클래스가 Serializable 인터페이스를 구현해야 하며, 클래스의 필드 중에 직렬화하지 말아야 할 필드는 transient 키워드를 사용하여 표시해야 합니다.


<br>


```java
import java.io.Serializable;

public class User implements Serializable {
    private static final long serialVersionUID = 1L; // 클래스 버전 관리용
    private String username;
    private transient String password; // 직렬화에서 제외

}

```
#### Serializable 인터페이스 구현
직렬화할 클래스는 java.io.Serializable 인터페이스를 구현합니다.

#### transient 키워드 사용
객체를 직렬화할 때, 직렬화하지 않을 필드는 transient 키워드를 사용하여 표시합니다. 
예를 들어, 암호와 같은 민감한 정보를 직렬화에서 제외하려면 transient를 사용합니다.

#### 직렬화 및 역직렬화
ObjectOutputStream과 ObjectInputStream 클래스를 사용하여 객체를 파일에 저장하거 읽어올 수 있습니다.
- 직렬화: 객체를 파일에 저장할때 ObjectOutputStream을 사용합니다.
- 역직렬화: 저장된 파일에서 객체를 읽어올때 ObjectInputStream을 사용합니다.


```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;

public class SerializationExample {
    public static void main(String[] args) {
        User user = new User("john_doe", "password123");

        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("user.ser"))) {
            oos.writeObject(user); // 객체를 파일에 직렬화
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```


## ObjectInputStream의 readObject()
readObject() 메서드는 직렬화된 파일에서 객체를 읽어올 때 사용니다. 이 메서드는 직렬화된 데이터를 역직렬화하여 원래의 객체로 복원합니다.
Object 타입을 반환하기 때문에, 읽어온 객체를 원래의 클래스 타입으로 형 변환해야 합니다.

역질렬화할 때 해당 클래스가 클래스패스에 없는 경우 ClassNotFoundException이 발생할 수 있습니다.
또한 클래스의 구조나 패키지 이름이 변경된 경우에도 발생할 수 있습니다.
직렬화된 객체가 다른 JVM 버전이나 다른 시스템에서 직렬화된 경우, 특정 클래스에 의존할 때도 예외가 발생할 수 있습니다.


# SerialVersionUID의 역할은 무엇이며, 재정의할 필요성
SerialVersionUID 는 직렬화된 객체의 버전을 식별하는 고유 식별자입니다.

클래스에 새로운 필드가 추가됙거나 제거되는 등 변경 사항이 있을 때 재정의하지 않으면 InvalidClassException 예외가 발생할 수 있습니다.
또한 여러 버전의 클래스가 존재할 수 있는 경우 SerialVersionUID 를 사용하여 호환성을 유지할 수 있기 때문에 직렬화시 버전 불일치의 문제를 방지할 수 있습니다.


## Externalizable 인터페이스와 Serializable 인터페이스의 차이
Externalizable 인터페이스를 구현하면, 직렬화 및 역질렬화 과정에서 writeExternal()과 readExternal() 메섯드를 사용자가 직접 구현해야 합니다.
또한 직렬화할 필드와 방법을 명시적으로 제어할 수 있어 불필요한 데이터를 직렬화하지 않을 수 있습니다.
이 과정에서 성능 최적화가 가능하며 직렬화된 데이터의 구조를 명확하게 정의할 수 있습니다.

Serializable 는 직렬화를 자동으로 처리합니다. 따라서 특정 필드를 제외하려면 transient를 사용해야 합니다.


# transient
transient로 선언된 필드는 ObjectOutputStream을 사용하여 객체를 직렬화할 때 데이터 스트림에 저장되지 않습니다.
역직렬화 과정에서는 transient로 선언된 필드는 기본값으로 초기화됩니다. 
int 타입의 transient 필드는 0으로, boolean 타입의 transient 필드는 false로 초기화됩니다.
직렬화가 필요한 데이터와 필요하지 않은 데이터를 구분할 때 유용합니다. 



# transient 키워드가 적용된 필드가 사용자 정의 객체나 참조형 변수를 가질 때의 동작은 어떻게 되나요? 또한, transient 키워드의 적합한 활용 방안과 주의할 점에 대해 조사해보세요.
transient 키워드가 적용된 필드가 사용자 정의 객체나 참조형 변수를 가지면, 직렬화 시 해당 필드의 값은 저장되지 않으며, 역직렬화 후에는 null이 되거나 기본값으로 초기화됩니다.

적합한 활용 방안으로는 민감한 정보나, 직렬화가 필요 없는 데이터가 있을 때 사용합니다. 
주의할 점은, transient로 지정된 필드는 역직렬화 후에 제대로 복원되지 않으므로, 필요한 경우 readObject 메서드에서 직접 복원 로직을 구현해야 합니다.


# readObject 메서드를 구현하여 transient 필드를 복원할 때, 어떤 주의사항을 고려
readObject 에섯 복원 로직을 정확하게 구현해야합니다. 직렬화되지 않기 때문에 복원시 적절한 값을 설정해야 합니다.
또한 직렬화와 역직렬화시 필드의 순서가 맞아야 하며, 복원과정에서 발생할 수 있는 예외를 적절히 처리하여야 합니다.

# Serializable 인터페이스 내부의 writeObject 메서드
writeObject() 메서드에서 transient 필드는 직렬화됟지 않으므로 이 ㅁ메서드에서는 transient 필드를 저장하지 않아야 합니다.