---
layout: post
title: Socket
categories: [Java]
---


## Socket
소켓은 TCP/IP 기반의 네트워크 통신에서 데이터 송수신의 마지막 접점을 말한다ㅣ.
네트워크를 통해 두 컴퓨터 간의 데이터 통신을 하고, 클라이언트와 서버 간의 연결을 관리한다.

클라이언트가 서버에 연결 요청을 보내고, 서버가 이 요청을 수락하면 데이터 전송이 시작된다.

지속적으로 연결을 유지하며 실시간으로 데이터를 주고 받는다.

클라이언트와 서버로 구분되며, 네트워크 상에서 클라이언트와 서버에 해당하는 컴퓨터를 식별하기 위해 IP 주소와 
해당 컴퓨터 내에서 현재 통신에 사용되는 응용프로그램을 식별하기 위한 포트번호가 사용된다.

![socket.png](https://github.com/user-attachments/assets/5ee55c98-b46c-433a-9f66-1285d9a1372e)
### Server
데이터 제공

### Client
데이터를 요청하여 제공받는 쪽

<br>


### 주요 클래스
#### Socket
- 클라이언트 측에서 사용하는 클래스로 서버에 연결을 설정하고 데이터를 송수신한다.

#### ServerSocket
- 서버 측에서 사용되는 클래스로 특정 포트에서 클라이언트의 연결 요청ㅇㄹ 기다리고, 요청이 들어오면 이를 수락하여
Socket 객체를 생성한다.

#### InputStream/ OutputStream
- 소켓을 통해 데이터를 주고 받는데 사용한다.

#### InetAddress
- IP 주소를 다루기 위한 클래스로 특정 호스트의 IP 주소를 얻거나, 호스트 이름을 IP 주소로 변환하는데 사용한다.


<br>


### 클라이언트와 서버 간의 연결
1. 서버 소켓 생성 후 대기
   서버가 ServerSocket 를 생성 후, accept() 메서드를 호출하여 클라이언트의 연결 요청을 대기한다.

2. 클라이언트 측 설정 후 연결 요청
   클라이언트는 서버의 IP 주소와 포트 번호로 Socket 객체를 생성하고, 서버에 연결을 요청한다.
   
3. 서버에서 연결 수락
   서버는 클라이언트의 연결 요청을 수락하과, 연결된 클라이언트와 통신하기 위해 Socket 객체를 생성한다.
   
4. 데이터 송수신
   클라이언트와 서버는 InputStream, OutputStream을 사용하여 데이터를 주고 받는다.

5. 연결 종료
   통신이 끝나면 클라이언트와 서버는 각각 소켓을 닫아 연결을 종료한다.


<br>


# 데이터 처리
소켓을 통해 데이터를 주고받는 과정에서 데이터를 적절한 형식으로 변환하고 수신 후 원래의 형싱으로 변환하는 작업이 필요하다.
이 과정을 패킹, 언패킹이라고 한다.

### 패킹 Packing
전송 전 데이터를 바이트 배열이나 스트림으로 변환하는 과정  
- 텍스트 데이터
```java
String message = "Hello";
byte[] data = message.getBytes(message);

outputStream.write(data);
```
- 객체 데이터
객체를 전송하는 경우, 객체를 직렬화하여 바이트 배열로 변환한다.  
ObjectOutputStream을 사용하여 객체를 직렬화할 수 있다.
```java
ObjectOutputStream oos = new ObjectOutputStream(socket.getOutputStream());

oos.writeObject(myObject);
```

### 언패킹 Unpacking
수신된 바이트 데이터를 원래 형식으로 변환하는 과정
- 텍스트 데이터
```java
byte[] buffer = new byte[1024];
int bytesRead = inputStream.read(buffer);
String receivedMessage = new String(buffer, 0, bytesRead, "UTF-8");
```

- 객체 데이터 
직렬화된 객체를 수신한 경우, 역직렬화를 통해 원래 객체로 복원한다.
```java
ObjectInputStream ois = new ObjectInputStream(socket.getInputStream());
MyObject myObject = (MyObject) ois.readObject();
```

<br>


# 예외
#### IOException
 데이터 전송 중 연결이 끊어지거나, 네트워크 오류가 발생하는 경우 발생한.
 서버가 다운되거나, 클라이언트와 연결이 갑자기 끊어지는 경우

#### SocketTimeoutException
 소켓에서 데이터 수신을 대기하는 동안 일정 시간 내에 응답이 없는 경우다 발생한다.  
 소켓에서 타임아웃을 설정할 수 있으며, 타임아웃이 지나면 예외가 발생

#### UnknownHostException
 잘못된 호스트 이름이나 IP주소를 사용하여 소켓을 생성할 때 발생한다.
 호스트 이름을 사용하ㅏ여 소켓을 생성할 때 , 도메인 이름이 잘못되었거나 존재하지 않는 경우 발생한다.
 도메인 이름을 IP 주소로 변환하는 DNS 서버에 문제가 있거나, 네트워크 연결이 끊어지 경우 발생할 수 있다.
 소켓을 생성하기 전 호스트 이름으 유효한지 검증하는 방법을 통해 해당 예외를 예방할 수 있다.
 InetAddress클래스를 사용하여 도메인 이름이 IP 주소로 변환될 수 있는지 미리 확인할 수 있다.
```java
try {
    InetAddress address = InetAddress.gettByteName("invalid.com");
    Socket socket = new Socket(address, 8080);
} catch (UnknownHostException e) {
    System.out.println("Invalid host name: " + e.getMessage());
}
```
 네트워크 연결이 정상적인지, DNS 서버가 올바르게 동작하는지 확인해야 한다.
 서버 측에서 문제가 발생하는 경우, 네트워크 설정을 점검하고, 필요시 DNS 서버를 변경하거나 네트워크 관리자에게 문의할 수 있다.

#### BindException 
 서버 소켓이 이미 사용 중인 포트에 바인딩하려고 할 때 발생한다.
 일반적으로 서버 소켓이 열려고하는 포트가 이미 다른 프로세스에서 사용 중 일때 발생
 리눅스/ 유닉스 기반 시스템에서 권한이 없는 사용자가 1024 이하의 포트 번호에 바인딩하려고 할 때 발생할 수 있다.
 서버 소켓을 생성하기 전에 해당 포트가 이미 사용 중인지 확인하여, 다른 포트를 사용하거나, 사용 중인 포트를 해제한 후 포트를 사용할 수 있다.
```java
try {
    ServerSocket serverSocket = new ServerSocket(8080);
} catch (BindException e) {
    System.out.println("Port 8080 is already in use. Please choose another port.");
    // 대처: 다른 포트를 선택하거나, 현재 사용 중인 포트를 해제 후 재시도
}
```
 1024 이하의 포트를 사용하는 경우, 관리자 권한으로 프로그램을 실행하거나, 관리자 권한을 부여받을 수 있다.


#### EOFException
 클라이언트나 서버가 데이터를 더 이상 전송하지 않을 때 발생한다.
 스트림의 끝을 만나는 경우 발생

#### ClassNotFoundException
 객체를 역렬화할 때, 해당 클래스가 현재 클래스패스에 존재하지 않는 경우 발생
 주로 네트워크 통신에서 클라이언트와 서버 간의 클래스 버전이 일치하지 않거나 클래스가 누락된 경우 발생한다.
 역직렬화하려는 객체의 클래스가 클래스패스에 존재하는지 확인하고, 네트워크 통신을 하는 클라이언트와 서버가
동일한 클래스 버전을 사용하도록 해야 한다.
 서버와 클라이언트의 JAR 파일 또는 클래스파일이 일치하는지 확인하고, 누락된 클래스가 없는지 점검한다.
 동일한 클래스를 포함한 JAR 파일을 클라이언트와 서버에 모두 배포하여 버전 불일치 문제를 방지한다.
 만약 클라이언트와 서버의 환경이 다른 경우, 서버에서 클라이언트로 클래스 파일을 전송하거나, 
  클래스 로더를 사용해 동적으로 로드할 수 있는 방법을 고려해야 한다.



멀티 채팅
![multi.png](https://github.com/user-attachments/assets/cac5f24b-2938-4931-a823-4906bd41c9b4)

서버 소켓이 루프를 돌면서 클라이언트가 연결될 때마다 새로운 스레드를 생성한다.
이렇게 생성된 스레드는 클라이언트에서 메세지가 들어올때마다 전체 클라이언트에 브로드캐스트한다.

```java


```


