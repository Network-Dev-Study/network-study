# 소켓 프로그래밍: 네트워크 애플리케이션 생성

일반적인 네트워크 애플리케이션은 2개의 종단 시스템에 존재하는 클라이언트 프로그램과 서버 프로그램으로 구성되어 있다.

이런 클라이언트-서버 애플리케이션에는 두가지 형태가 있는데,

1. RFC에 정의된 표준 프로토콜을 구현하는 클라이언트-서버 애플리케이션

   때때로 개방형이라고 불리는데, 그 동작을 규정하는 규칙들이 모두에게 알려져 있기 때문이다.

   이경우, RFC에 정의된 규칙을 따라야 한다.(대부분의 네트워크 애플리케이션은 독립개발자가 개발한 클라이언트와 서버 프로그램 간의 통신을 포함한다.)

2. 개인의 독점적인 네트워크 애플리케이션

   이 경우 클라이언트와 서버 프로그램은 RFC 또는 다른 곳에 공식적으로 출판되지 않은 애플리케이션 계층 프로토콜을 채택한다.

   개발자는 클라이언트와 서버 프로그램을 모두 생성하고 코드에 무엇을 사용할지 완전히 제어한다.

   하지만 공개된 프로토콜이 아니기 때문에 다른 개발자는 이 애플리케이션과 상호작용하는 코드를 개발할 수 없다.

TCP와 UDP를 파이썬 애플리케이션으로 구현해서 살펴 볼 것이다.

## UDP를 이용한 소켓 프로그래밍

소켓을 이용한 두 통신 프로세스들 간의 상호작용을 살펴보면,

송신 프로세스가 데이터의 패킷을 소켓 밖으로 밀어내기 전에, UDP를 사용할 때 먼저 패킷에 목적지 주소를 붙여야 한다.

이 패킷이 송신자의 소켓을 통과한 후 인터넷은 이 목적지 주소를 이용하여 그 패킷을 인터넷을 통해 수신프로세스에 있는 소켓으로 라우트할 것이다.

패킷이 수신 소켓에 도착하면 수신 프로세스는 소켓을 통해 그 패킷을 추출하고 다음에 패킷의 콘텐츠를 조사하고 적절한 동작을 취한다.

여기서 이 목적지 주소에서 IP는 목적지 주소의 일부분이다. 해당 IP에 도착해도 목적이 있어야 하기 때문에 포트 번호도 추가한다.

그리고 보내는 곳의 IP주소도 포함해야 하는데, 이는 UDP에서 하부 운영체제가 자동으로 실행한다.

![Untitled](https://user-images.githubusercontent.com/76640167/210794628-43c23c47-34d8-4a5b-b5e4-d7d617cdcf90.png)

이제 코드를 살펴 볼 것이다.

클라이언트는 `UDPClient.py`, 서버는 `UDPServer.py`로 부른다.

```python
# UDPClient.py

# socket module이다. 이 module을 통해 소켓을 생성할 수 있다.
from socket import *

#서버의 IP 혹은 서버의 호스트 이름을 할당한다.
#지금 처럼 이름으로 할당하는 경우, DNS 검색이 자동으로 수행된다.
serverName = ’hostname’

# 목적지 port 번호를 나타낸다.
serverPort = 12000

# 클라이언트 소켓을 생성한다.
# 1번째 파라미터는 주소군을 나타내며, 2번째 파라미터는 소켓의 타입을 나타낸다.
# AF_INET은 IPv4를 사용하고 있음을 나타내고, SOCK_DGRAM은 UDP 소켓임을 의미한다.
clientSocket = socket(AF_INET, SOCK_DGRAM)

# 보낼 메시지를 입력 받는다.
message = Input(’Input lowercase sentence:’)

# 소켓으로 바이트 형태를 보내기 위해 먼저 encode()를 통해 바이트 타입으로 변환한다.
# sendto() 메서드는 목적지 주소를 메시지에 붙이고 그 패킷을 프로세스 소켓인 clientSocket으로 보낸다.
# 클라이언트 주소도 같이 보내지는데 이는 자동으로 수행된다.
clientSocket.sendto(message.encode(),(serverName, serverPort))

# 패킷으로 메시지를 보낸 후, 서버로 부터 데이터 수신을 기다린다.

# 패킷 데이터는 modifiedMessage에 저장되고, 패킷의 출발지 주소(IP, port)는 serverAddress에 할당된다.
# 패킷의 출발지 주소는 이미 알고 있지만, 해당 라인에서 제공한다.
# recvfrom() 메서드는 2048의 버퍼 크기로 받아들인다.
modifiedMessage, serverAddress = clientSocket.recvfrom(2048)

# 출력 .decode()를 통해 바이트에서 문자열로 변환한다.
print(modifiedMessage.decode())

# 소켓 닫기
clientSocket.close()
```

```python
# UDPServer.py

from socket import *

# 포트 번호
serverPort = 12000

# UDP 소켓 생성
serverSocket = socket(AF_INET, SOCK_DGRAM)

# 12000 포트 번호를 소켓에 할당한다.
# 이를 통해 서버 IP 주소의 12000 포트로 패킷을 보내면 해당 소켓으로 패킷이 전달된다.
serverSocket.bind((’’, serverPort))

print(”The server is ready to receive”)

# 클라이언트로부터 계속해서 패킷을 수신하기 위해 while 루프를 사용한다.
while True:
    # 패킷이 서버에 도착하면 데이터는 메세지에 할당되고 패킷의 출발지 주소는 clientAddress에 저장된다.
    # 해당 주소로 서버는 응답을 어디에 보내야할지 알 수 있다.
    message, clientAddress = serverSocket.recvfrom(2048)

    # 바이트 데이터를 decode()하고 대문자로 변환한다.
    modifiedMessage = message.decode().upper()

    # 클라이언트 주소를 대문자로 변환된 메시지에 붙이고, 그 결과로 만들어진 패킷을 서버에 보낸다.
    # 서버의 주소도 같이 보내지는데 이는 자동으로 수행된다.
    serverSocket.sendto(modifiedMessage.encode(), clientAddress)
```

서버에서 패킷이 도착할 때 까지 휴면 상태로 있다가 클라이언트에서 요청이 들어오면 작동한다.

## TCP 소켓 프로그래밍

UDP와 달리 TCP는 연결지향 프로토콜로, 클라이언트와 서버가 서로에게 데이터를 보내기 전에 먼저 TCP연결을 설정할 필요가 있다.

TCP연결을 생성할 때 클라이언트 소켓 주소와 서버 소켓 주소를 연결과 연관시킨다.

TCP연결이 설정된 후, 한쪽에서 다른 쪽으로 데이터를 보내려면 소켓을 통해 데이터를 TCP연결로 보내면 된다.

![Untitled](https://user-images.githubusercontent.com/76640167/210809355-8e26c0e0-561c-45bc-ba57-8d34487c68bc.png)

상호작용에 대해 더 자세히 살펴보면,

1. 클라이언트는 서버로 접속을 먼저 시도한다.(서버는 클라이언트의 초기 접속에 응대할 수 있도록 준비해야 한다.)

   여기서 서버는 클라이언트가 접속을 하기 전에 프로세스를 먼저 수행해야 하고, 임의의 컴퓨터에서 수행되고 있는 클라이언트로 부터의 초기접속을 처리하는 특별한 소켓을 가져야 한다.

2. 서버 프로세스가 수행되면 클라이언트 프로세스는 서버로의 TCP연결을 시도한다. 이 때, 클라이언트는 서버에 있는 환영 소켓의 주소로 연결을 설정한다.
3. 소켓을 생성한 후, 클라이언트는 세방향 핸드셰이크를 하고 서버와 TCP연결을 설정한다.

   3-1. 세방향 핸드셰이크를 하는 동안, 클라이언트 프로세스는 서버 프로세스의 출입문을 두드린다.

4. 서버는 이 노크 소리를 들으면, 해당 클라이언트에게 지정되는 새로운 소켓을 생성한다.

애플리케이션 관점에서 볼 때, 클라이언트 소켓과 서버의 연결 소켓은 파이프에 의해 직접 연결된다.

위의 사진처럼, 클라이언트 프로세스는 자신의 소켓으로 임의의 바이트를 보낼 수 있으며 보낸 순서대로 서버 프로세스가 바이트를 수신하도록 TCP가 보장해 준다.(TCP에서 신뢰적 서비스를 제공한다.)

![Untitled](https://user-images.githubusercontent.com/76640167/210813409-0e49cc1d-bab4-4bfb-8ba2-1d039f09f3c1.png)

이제 UDP처럼 각 코드를 확인해 보면

```python
# TCPClient.py

from socket import *

serverName = ’servername’
serverPort = 12000

# 클라이언트 소켓을 의미한다. SOCK_STREAM으로 TCP 소켓임을 명시했다.
# UDP 때와 마찬가지로 따로 출발지 주소를 명시하지 않는다. (운영체제가 대신 해준다.)
clientSocket = socket(AF_INET, SOCK_STREAM)

# 클라이언트가 TCP 소켓을 이용하여 서버로 데이터를 보내기 전에
# TCP 연결이 먼저 클라이언트와 서버 사이에 설정되어야 한다.
# 해당 라인으로 TCP 연결을 시작하고, connect() 메서드의 파라미터는 연결의 서버 쪽 주소이다.
# 이 라인이 수행된 후에 3-way handshake가 수행되고 클라이언트와 서버 간에 TCP 연결이 설정된다.
clientSocket.connect((serverName, serverPort))

sentence = input(’Input lowercase sentence:’)

# 클라이언트 소켓을 통해 TCP 연결로 보낸다.
# UDP 소켓처럼 패킷을 명시적으로 생성하지 않으며 패킷에 목적지 주소를 붙이지 않는다.
# 대신 클라이언트 프로그램은 단순히 문자열에 있는 바이트를 TCP 연결에 제공한다.
clientSocket.send(sentence.encode())

# 서버로부터 바이트를 수신하기를 기다린다.
modifiedSentence = clientSocket.recv(1024)
print(’From Server: ’, modifiedSentence.decode())

# 연결을 닫는다. 이는 클라이언트 TCP가 서버의 TCP에게 TCP 메시지를 보내게 한다.
clientSocket.close()
```

```python
#TCPServer.py

from socket import *

serverPort = 12000

# TCP 소켓 생성
serverSocket = socket(AF_INET, SOCK_STREAM)

# 서버의 포트 번호를 소켓과 연관시킨다.
# 현재 아래 코드는 대기 중이다.(클라이언트가 .connect()하기 전 까지 기다리고 있다.)
serverSocket.bind((’’, serverPort))

# 연관시킨 소켓은 대기하며 클라이언트가 문을 두드리기를 기다린다.
# 큐잉되는 연결의 최대 수를 나타낸다.
serverSocket.listen(1)
print(’The server is ready to receive’)

while True:
    # 클라이언트가 TCP 연결 요청을 하면 accept() 메소드를 시작해서 클라이언트를 위한 연결 소켓을 서버에 생성한다.
    # 그 뒤 클라이언트와 서버는 핸드셰이킹을 완료해서 클라이언트의 소켓과 연결 소켓 사이의 TCP 연결을 생성한다.
    connectionSocket, addr = serverSocket.accept()
    sentence = connectionSocket.recv(1024).decode()
    capitalizedSentence = sentence.upper()
    connectionSocket.send(capitalizedSentence.encode())

    # 응답을 보내고 연결 소켓을 닫는다. 그러나 환영소켓인 serverSocket이 열려있어 다른 클라이언트가 서버에 연결을 요청할 수 있다.
    connectionSocket.close()
```
