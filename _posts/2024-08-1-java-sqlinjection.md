---
layout: post
title: SQL Injection
categories: [DB]
---

## SQL Injection
<span style='background-color:#D0E4FC'>
**공격자가 악의적인 SQL문을 입력하여 데이터베이스가 비정상적으로 동작하도록 하는 공격**
</span>  
  
이 공격을 통해 데이터를 유출하거나, 데이터를 조작할 수 있다.  


<br><br>

### 언제?
1. 사용자가 입력한 데이터를 SQL쿼리에 직접 삽일할 때
    - 사용자의 이름과 비밀번호를 확인하는 쿼리에서 입력 값이 제대로 검증되지 않은 경우

2. 사용자의 입력에 대한 검증이 부족한 경우로, 문자열의 길이나 형식에 대한 검증이 없으면 발생할 수 있다.


예를 들어

공격자가 로그인 폼에 다음과 같이 입력한다면?
```text
admin' OR '1'='1'; --
```

이 입력은 아래와 같은 쿼리가 된다.
```sql
SELECT * FROM users WHERE userId = 'admin' OR '1'='1'; -- AND password = '';
```
- 1=1 조건은 항상 참으로, 사용자가 비밀번호를 입력하지 않아도 로그인을 할 수 있도록 한다.
- --은 주석으로 이후의 쿼리를 무시하도록 한다.
  
이렇게 되면 데이터베이스가 의도치 않은 동작을 하게 만들어 사용자의 정보를 유출할 수 있다.  
      
이러한 작업을 통해 데이터를 유출할 수 있으며, 특정 사용자의 비밀번호를 변경, 관리자 권한을 얻어 시스텡 서버를 장악할 수 있다.  
또한 로그인시 비밀번호 검증을 피하여 로그인이 가능하게 된다.  


<br>


## 예방 

#### 1. PreparedStatement 
PreparedStatement를 사용하면 드라이버가 자동으로 매개변수 값을 이스케이프 처리하여 SQL Injection 공격을 방지할 수 있다.  
PreparedStatement는 SQL 쿼리의 구조와 데이터를 분리하여 공격자가 악의적인 SQL 코드를 삽입할 가능성을 줄여준다.
```java
// PreparedStatement 사용 예시
String query = "SELECT * FROM users WHERE username = ? AND password = ?";
PreparedStatement preparedStatement = connection.prepareStatement(query);
preparedStatement.setString(1, username); // 사용자 입력을 바인딩
preparedStatement.setString(2, password);
ResultSet resultSet = preparedStatement.executeQuery();
```

<br>

#### 2. ORM 프레임워크 사용
Hibernate와 같은 ORM 프레임워크를 사용하여 SQL 쿼리 작성을 자동화함으로써 위험을 줄일 수 있다.  

<br>

#### 3. 입력 값 검증 및 이스케이프 처리
모든 사용자의 입력을 검증하고 특수 문자를 이스케이프 처리한여, SQL 쿼리의 구조에 영향을 미치지 않도록 해야 한다.
입력 값의 유형, 길이, 형식을 검증하여 공격 범위를 제한한다.  
```java
// 입력 값 검증 예시
if (username.length() > 20 || !username.matches("[a-zA-Z0-9]+")) {
    throw new IllegalArgumentException("Invalid username");
}
```  

```sql
// 강화된 입력 값 검증 및 PreparedStatement 사용 예시
if (input.contains(";") || input.contains("--")) {
    throw new IllegalArgumentException("Invalid input");
}
PreparedStatement ps = connection.prepareStatement("SELECT * FROM users WHERE username = ?");
ps.setString(1, input); // 안전하게 바인딩
```



<br>

#### 4. 최소 권한 원칙 적용
데이터베이스 사용자에게 최소한의 권한을 부여해야 한다.
- 특정 사용자에게 SELECT 권한만 부여
```sql
GRANT SELECT ON database.table TO 'user'@'host';
```

<br>


#### 5. 동적 쿼리
SQL 쿼리를 동적으로 생성하는 코드에서 PreparedStatement의 파라미터 바인딩을 적절히 사용하지 않으면 취약점이 발생할 수 있다.  
예를 들어, 사용자 입력을 직접 쿼리 문자열에 포함시키는 것은 위험하다.  
```java
// 위험한 동적 쿼리 생성 예시
String query = "SELECT * FROM users WHERE username = '" + username + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query); // SQL Injection에 취약
```


