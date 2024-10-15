---
layout: post
title: Socket
categories: [Java]
---

자바의 소켓은 네트워크 상에서 데이터를 주고 받기 위한 통신 채널을 제공하는 프로그래밍 인터페이스이다.  

소켓은 TCP/IP나 UDP같은 프로토콜을 사용하여 네트워크에 연결된 두 프로그램 사이에서 데이터를 송수신할 수 있도록 한다.  

이를 통해 자바 프로그램 간, 또는 자바 프로그램과 다른 시스템 간에 통신할 수 있다.  
  

<br>


## Socket
소켓은 TCP/IP 기반의 네트워크 통신에서 데이터 송수신의 마지막 접점으로 IP주소와 포트 번호를 결합한 것이다.  

- IP 주소 : 통신 대상의 주솟
- 포트 번호 : 특정 애플리케이션이 사용하는 네트워크 포트  

네트워크를 통해 두 컴퓨터 간의 데이터 통신을 하고, 클라이언트와 서버 간의 연결을 관리한다.

클라이언트가 서버에 연결 요청을 보내고, 서버가 이 요청을 수락하면 데이터 전송이 시작된다.

지속적으로 연결을 유지하며 실시간으로 데이터를 주고 받는다.

클라이언트와 서버로 구분되며, 네트워크 상에서 클라이언트와 서버에 해당하는 컴퓨터를 식별하기 위해 IP 주소와 
해당 컴퓨터 내에서 현재 통신에 사용되는 응용프로그램을 식별하기 위한 포트번호가 사용된다.

![socket.png](https://github.com/user-attachments/assets/5ee55c98-b46c-433a-9f66-1285d9a1372e)
  
<br>

### Server
데이터 제공

### Client
데이터를 요청하여 제공받는 쪽


<br><br><br>


## 주요 클래스
### Socket
- 클라이언트가 서버에 연결할 때 사용하는 클래스
- 클라이언트가 서버에 연결을 요청한 후, 서버에서 수락하면 통신이 가능하다.  
- 소켓 객체를 생성하면 서버와의 연결이 즉시 이루어진다.  
- 이후 입력 스트림과 출력 스트림을 통해 데이터를 송수신한다.  
```java
Socket socket = new Socket("서버 IP 주소", 포트 번호);
```

  
### ServerSocket
- 서버가 클라이언트의 연결을 기다릴 때 사용되는 클래스  
- 서버는 특정 포트에서 클라이언트의 연결 요청을 수신한다.  
- 연결이 수락되면 Socket 객체를 생성하여 통신을 시작한다.
- accept()메서드를 호출시 클라이언트와의 연결을 수락하고, Socket 객체를 반환한다.
```java
ServerSocket serverSocket = new ServerSockekt(포트번호);
Socket socket = serverSocket.accept();   // 클라이언트 연결 수락
```
  
  
### InputStream/ OutputStream
- 소켓을 통해 데이터를 주고 받기 위한 스트림  
- 소켓이 연결되면, 이 스트림을 통해 데이터를 읽고 쓰는 작업을 한다.  
- 바이트 단위  

```java
InputStream input = socket.getInputStream();  // 소켓에서 입력 스트림을 가져옴
BufferedReader reader = new BufferedReader(new InputStreamReader(input));
String message = reader.readLine() // 데이터를 읽음
```

```java
OutputStream output = socket.getOutputStream();  // 소켓에서 출력 스트림을 가져옴
PrintWriter writer = new PrintWriter(output, true);
writer.println("Hello, Server!");  // 데이터를 보냄
```

- 바이트 단위로 데이터를 처리하며, 텍스트, 이미지, 파일과 같은 데이터를 전송할 때 사용된다.  
- 텍스트 데이터를 다루는 경우 InputStreamReader(), OutputStreamWriter를 사용하여 바이트 스트림을 문자 스트림으로 변환할 수 있습니다.


<br>

### InetAddress
- IP 주소를 다루는 클래스
- 네트워크에서 호스트의 IP 주소를 얻거나, 호스트 이름을 IP 주소로 변환한다.

#### InetAddress.getByName(String host)
주어진 호스트 이름을 IP 주소로 변환한다.

```java
InetAddress address = InetAddress.getByName("www.naver.com");
```

#### InetAddress.getLocalHost()
로컬 컴퓨터의 IP  주소를 가져온다.
```java
InetAddress localAddress = InetAddress.getLocalHost();
```

#### InetAddress.getByAdderss(byte[] addr) 
주어진 바이트 배열로부터 IP 주소 생성  


<br>
<br>
<br>


## 클라이언트와 서버 간의 연결
1. ServerSocket 생성 후 대기  
   서버가 ServerSocket 를 생성 후, accept() 메서드를 호출하여 클라이언트의 연결 요청을 대기한다.

2. 클라이언트 측 설정 후 연결 요청  
   클라이언트는 서버의 IP 주소와 포트 번호로 Socket 객체를 생성하고, 서버에 연결을 요청한다.
   
3. 서버에서 연결 수락  
   서버는 클라이언트의 연결 요청을 수락하고, 연결된 클라이언트와 통신하기 위해 Socket 객체를 생성한다.
   
4. 데이터 송수신  
   클라이언트와 서버는 InputStream, OutputStream을 사용하여 데이터를 주고 받는다.

5. 연결 종료  
   통신이 끝나면 클라이언트와 서버는 각각 소켓을 닫아 연결을 종료한다.


<br><br><br>


## 데이터 처리
소켓을 통해 데이터를 주고받는 과정에서 데이터를 적절한 형식으로 변환하고 수신 후, 원래의 형식으로 변환하는 작업이 필요하다.
이 과정을 패킹, 언패킹이라고 한다.

<br>

### 패킹 Packing
전송 전 데이터를 바이트 배열이나 스트림으로 변환하는 과정  
- 텍스트 데이터

```java
String message = "Hello";
byte[] data = message.getBytes(message);

outputStream.write(data);
```

<br>

- 객체 데이터  
객체를 전송하는 경우, 객체를 직렬화한 후, 바이트 배열로 변환한다.    
ObjectOutputStream을 사용하여 객체를 직렬화한다.  
  
```java
ObjectOutputStream oos = new ObjectOutputStream(socket.getOutputStream());

oos.writeObject(myObject);
```

<br>

### 언패킹 Unpacking
수신된 바이트 데이터를 원래 형식으로 변환하는 과정
- 텍스트 데이터

```java
byte[] buffer = new byte[1024];  // 수시할 데이터를 담을 버퍼
int bytesRead = inputStream.read(buffer);   // 읽은 바이트 수 반환
String receivedMessage = new String(buffer, 0, bytesRead, "UTF-8");
```
- buffer : 수신된 바이트 데이터가 저장된 배열 
- 0 : 바이트 배열의 시작 위치
- bytesRead : 실제로 읽어들인 바이트 수
- UTF-8 : 데이터가 UTF-8 인코딩으로 전송되었음을 가정하고 변환

<br>

- 객체 데이터   
직렬화된 객체를 수신한 경우, 역직렬화를 통해 원래 객체로 복원한다.
  
```java
ObjectInputStream ois = new ObjectInputStream(socket.getInputStream());
MyObject myObject = (MyObject) ois.readObject();
```

<br><br><br>


## 예외
### IOException
 - 데이터 전송 중 연결이 끊어지거나, 네트워크 오류가 발생하는 경우 
 - 서버가 다운되거나, 클라이언트와 연결이 갑자기 끊어지는 경우 발생한다.

### SocketTimeoutException
- 소켓에서 데이터 수신을 대기하는 동안 일정 시간 내에 응답이 없는 경우 발생한다.    
-  소켓에서 타임아웃을 설정할 수 있으며, 타임아웃이 지나면 예외가 발생한다.  

### UnknownHostException
 잘못된 호스트 이름이나 IP주소를 사용하여 소켓을 생성할 때 발생  

 - 호스트 이름을 사용하여 소켓을 생성할 때, 도메인 이름이 잘못되었거나 존재하지 않는 경우 발생한다.
 - 도메인 이름을 IP 주소로 변환하는 DNS 서버에 문제가 있거나, 네트워크 연결이 끊어지는 경우 발생할 수 있다.
 - 소켓을 생성하기 전 호스트 이름으 유효한지 검증하는 방법을 통해 예방할 수 있다.  
 - InetAddress클래스를 사용하여 도메인 이름이 IP 주소로 변환될 수 있는지 미리 확인할 수 있다.

```java
try {
    InetAddress address = InetAddress.gettByteName("invalid.com");
    Socket socket = new Socket(address, 8080);
} catch (UnknownHostException e) {
    System.out.println("Invalid host name: " + e.getMessage());
}
```

 네트워크 연결이 정상적인지, DNS 서버가 올바르게 동작하는지 확인해야 한다.  


### BindException 
- 서버 소켓이 이미 사용 중인 포트에 바인딩하려고 할 때 발생한다.
- 리눅스/유닉스 기반 시스템에서 권한이 없는 사용자가 1024 이하의 포트 번호에 바인딩하려고 할 때 발생할 수 있다.
- 서버 소켓을 생성하기 전에 해당 포트가 이미 사용 중인지 확인하여, 다른 포트를 사용하거나, 사용 중인 포트를 해제한 후 포트를 사용할 수 있다.

```java
try {
    ServerSocket serverSocket = new ServerSocket(8080);
} catch (BindException e) {
    System.out.println("Port 8080 is already in use. Please choose another port.");
    // 다른 포트를 선택하거나, 현재 사용 중인 포트를 해제 후 재시도 로직 추가
}
```

 1024 이하의 포트를 사용하는 경우, 관리자 권한으로 프로그램을 실행하거나, 관리자 권한을 부여 받을 수 있다.


### EOFException
 클라이언트나 서버가 데이터를 더 이상 전송하지 않는 경우 발생한다.
 - 스트림의 끝을 만나는 경우 발생

### ClassNotFoundException
- 객체를 역렬화할 때, 해당 클래스가 현재 클래스패스에 존재하지 않는 경우 발생 
- 주로 네트워크 통신에서 클라이언트와 서버 간의 클래스 버전이 일치하지 않거나 클래스가 누락된 경우 발생한다.
- 동일한 클래스를 포함한 JAR 파일을 클라이언트와 서버에 모두 배포하여 버전 불일치 문제를 방지한다.
- 만약 클라이언트와 서버의 환경이 다른 경우, 서버에서 클라이언트로 클래스 파일을 전송하거나, 클래스 로더를 사용해 동적으로 로드할 수 있는 방법을 고려해야 한다.

<br><br><br>

## 구현
```java
public class Socket_Server {
    public static void main(String[] args) {
        BufferedReader br = null;
        PrintWriter pw = null;

        ServerSocket serverSocket = null;
        Socket socket = null;

        Scanner sc = new Scanner(System.in);

        try {
            try {
                serverSocket = new ServerSocket(8081);
                socket.setSoTimeout(10000);

            } catch (BindException e) {
                System.out.println("Port(8081) is already in use.");
                return;

            }

            System.out.println("[Server] Client 연결 대기 중");

            try {
                socket = serverSocket.accept();
                socket.setSoTimeout(5000);
            } catch (SocketTimeoutException e) {
                System.out.println("Client connection timed out");
                return;
            }

            System.out.println("Client 연결 성공");
            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream());

            while (true) {
                try {
                    String input = br.readLine();
                    if ("quit".equalsIgnoreCase(input)) break;

                    System.out.println("From Client: " + input);
                    System.out.println("Send ==> ");

                    String output = sc.next();
                    pw.println(output);
                    pw.flush();
                    if ("quit".equalsIgnoreCase(output)) break;
                } catch (SocketTimeoutException e) {
                    System.out.println("Socket read Time out");
                } catch (IOException e) {
                    System.out.println("I/O error occurred while reading or writing data.");
                    break;
                } catch (Exception e) {
                    System.out.println("Unexpected error occurred: " + e.getMessage());
                    e.printStackTrace();
                    break;
                }

            }
        } catch (UnknownHostException e) {
            System.out.println("Unknown host: " + e.getMessage());

        } catch (SocketTimeoutException e) {
            System.out.println("Server socket timed out while waiting for a client connection.");

        } catch (IOException e) {
            System.out.println("I/O error occurred during socket setup: " + e.getMessage());

        } catch (Exception e) {
            System.out.println("Unexpected error occurred: " + e.getMessage());

        }
        finally {
            try {
                sc.close();
                socket.close();
                serverSocket.close();
                System.out.println("연결 종료");

            } catch (IOException e) {
                System.out.println("Error Closing Resources: " + e.getMessage());
            }
        }
    }
}
```


```java
public class Socket_Client {
    public static void main(String[] args) {
        BufferedReader br = null;
        PrintWriter pw = null;

        Socket socket = null;

        Scanner sc = new Scanner(System.in);

        try {
            socket = new Socket("127.0.0.1", 8081);

            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream());

            while (true) {
                System.out.println("Send ==>> ");
                String output = sc.nextLine();
                pw.println(output);
                pw.flush();

                if("quit".equalsIgnoreCase(output)) break;

                String input = br.readLine();
                System.out.println("From Server: " + input);

                if("quit".equalsIgnoreCase(input)) break;
            }
        } catch (IOException e) {
            System.out.println(e.getMessage());

        } finally {
            try {
                sc.close();
                if (socket != null) socket.close();
                System.out.println("서버 연결 종료");

            } catch (IOException e) {
                System.out.println("소켓 통신 error");

            }
        }
    }
}

```



<br><br><br>

## 멀티 채팅
![multi.png](https://github.com/user-attachments/assets/cac5f24b-2938-4931-a823-4906bd41c9b4)

서버 소켓이 루프를 돌면서 클라이언트가 연결될 때마다 새로운 스레드를 생성한다.
이렇게 생성된 스레드는 클라이언트에서 메세지가 들어올때마다 전체 클라이언트에 브로드캐스트한다.
