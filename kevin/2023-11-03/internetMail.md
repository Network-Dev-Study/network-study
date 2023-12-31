# 인터넷 전자메일

오늘날 가장 중요한 애플리케이션 중 하나

일반 우편과 같은 비동기적인 통신매체(상대방 스케쥴에 상관없이 편할 때 보낼 수 있다.)이지만, 일반 우편과 다르게 분배가 쉽고, 빠르고, 저렴하다.

![Untitled](https://user-images.githubusercontent.com/76640167/210542924-1107ca75-eee8-45f6-829a-df0d0df81c15.png)

인터넷 전자메일 시스템의 상위 레벨 개념(주요 요소: 사용자 에이전트, 메일 서버, SMTP(Simple Mail Transfer Protocol))

앨리스가 밥에게 메일을 보내는 과정

1. (준비) 사용자 에이전트는 사용자가 메시지를 읽고, 응답하고, 전달하고, 저장하고, 구성하게 해준다.(예시로는 웹 기반 지메일, 스마트폰 용 지메일 앱 등)
2. 앨리스가 메시지 작성을 끝내면, 사용자 에이전트는 메시지를 메일서버로 보내고, 메시지는 메일 서버의 출력 메시지 큐에 들어간다.
3. 밥이 메시지를 읽고 싶다면, 그의 사용자 에이전트는 메일 서버에 있는 메일박스에서 메시지를 가져온다.
4. 각 수신자는 메일 서버 안에 메일박스를 가지고 수신자에게 온 메시지를 유지하며 관리한다.(수신자인 밥은 자신을 인증하기 위해 사용자 계정과 비밀번호를 이용한다.)
5. 만약 밥의 메일 서버가 고장난다면, 앨리스는 매일서버의 출력 메시지 큐에 넣고 일정 주기(약 30분)로 전송을 시도한다. 만약 계속 실패한다면, 이를 앨리스에게 통보한다.

SMTP는 TCP의 신뢰적인 데이터 전송 서비스를 이용한다.

SMTP는 송수신하는 위치에 따라 SMTP의 서버 혹은 클라이언트로 바뀌어서 동작한다.

## SMTP

SMTP는 인터넷 전자메일의 중심에 있다.(참고로 HTTP보다 훨씬 오래되었다.)

SMTP는 여러 가지 장점이 있지만, 낡은 특성을 가진 오래된 기술이다.

예를 들어, 모든 메일의 메시지의 몸체(+헤더)는 단순한 7비트 ASCII여야하는 단점이 있다.

![Untitled](https://user-images.githubusercontent.com/61978339/91386448-f9e78380-e86d-11ea-9a69-b30131350c79.png)

이 단점은 사실 과거 보낼 수 있는 용량이 적어 텍스트문자로만 제한된 용량으로 보내던 1980년대 초에는 나름대로 의미가 있었지만, 오늘날에는 문제가 되었다.(멀티 미디어를 ASCII로 변환할 필요가 생긴 것이다.)

그냥 ASCII로 변환해서 보내면 되는 것이 아닐까 싶지만, HTTP는 전송 전에 멀티미디어 데이터를 ASCII로 변환하는 것을 요구하지 않는다.

![Untitled](https://user-images.githubusercontent.com/76640167/210545342-e280b01b-fff0-4ec7-b921-9bfcddb94999.png)

이번에는 SMTP의 기본 동작을 보기 위해 앨리스가 밥에게 ASCII 메시지를 밥에게 보낸다고 가정하자.

1. 앨리스는 사용자 에이전트에게 밥의 전자 메일주소로 메시지를 보내라고 명령한다.
2. 앨리스의 사용자 에이전트는 메시지를 앨리스의 메일 서버에게 보내고 그곳에서 메시지는 메시지 큐에 놓인다.
3. 앨리스의 메일 서버에서 동작하는 SMTP의 클라이언트 측은 메시지 큐에 있는 메시지를 보고 밥의 메일서버에서 동작하는 SMTP 서버에게 TCP연결을 설정한다.
4. 초기 SMTP 핸드셰이킹 이후 SMTP클라이언트는 앨리스의 메시지를 TCP연결로 보낸다.
5. 밥의 메일 서버 호스트에서 SMTP의 서버 측은 메시지를 수신하여 그 메시지를 밥의 메일 박스에 놓는다.
6. 밥은 편한 시간에 그 메시지를 읽기 위해 사용자 에이전트를 시작한다.

SMTP는 두 메일 서버가 아무리 멀리 있어도 직접 연결한다. 특히 수신받는 메일서버가 죽어도 송신하는 메일 서버안에서 머물고 있다.

SMTP를 자세히 살펴 보면 SMTP프로토콜이 마주본(face-to-face) 사람의 상호작용에 이용되는 프로토콜과 유사함을 알게 될 것이다.

1. 클라이언트 SMTP에서 서버 SMTP의 25번 포트로 TCP연결을 설정한다.(연결이 죽으면, 다시 시도한다.)
2. 이 연결이 설정되면, 서버와 클라이언트는 애플리케이션 계층 핸드셰이킹을 수행한다. 이 과정에서 SMTP 클라이언트는 송신자의 전자메일 주소와 수신자의 전자메일 주소를 제공한다.
3. 이후, 클라이언트는 메시지를 보낸다. SMTP는 TCP의 신뢰적 데이터 전송 서비스에 의존한다. 그리고 다른 보낼 메시지가 있다면, 클라이언트는 이 과정을 같은 TCP연결상에서 반복한다.(파이프라인)(만약 보낼 메시지가 없다면 TCP에게 닫을 것을 명령한다.)

위와 같은 과정을 클라이언트와 서버가 메시지 전달하는 과정을 예시로 보면

```bash
S:  220 hamburger.edu
C:  HELO crepes.fr
S:  250 Hello crepes.fr, pleased to meet you
C:  MAIL FROM: <alice@crepes.fr>
S:  250 alice@crepes.fr ... Sender ok
C:  RCPT TO: <bob@hamburger.edu>
S:  250 bob@hamburger.edu ... Recipient ok
C:  DATA
S:  354 Enter mail, end with ”.” on a line by itself
C:  Do you like ketchup?
C:  How about pickles?
C:  .
S:  250 Message accepted for delivery
C:  QUIT
S:  221 hamburger.edu closing connection
```

여기서 `crepes.fr` 이 `hamburgur.edu`에게 `“Do you like ketchup? How about pickles?”`메시지를 보낸다.

클라이언트 측에서 사용가능한 명령어로는 HELO, MAIL, FROM, RCPT, TO, DATA, CRLF.CRLF, QUIT이 있다.

텔넷에서 직접 `telnet serverName 25`로 SMTP서버와 대화할 수 있다.

## 메일 메시지 포맷

전자메일을 보낼 때 주변 정보가 포함된 헤더가 메시지 몸체 앞에 오게 된다. 이 주변 정보는 일련의 헤더 라인에 포함되는데, 메일 내용과는 빈 줄로 구분된다.

모든 헤더는 From: 헤더 라인과 To: 헤더 라인을 반드시 가져야 한다. 또한 Subject와 같은 다른 옵션 헤더 라인을 가질 수 있다.(SMTP명령어인 FROM TO와는 다르다. 이 것은 핸드 셰이킹 과정이고 지금 다루는 것은 보내고자 하는 메일 메시지 그 자체다.)

## 메일 접속 프로토콜

SMTP가 엘리스의 메일 서버로 부터 밥의 메일 서버로 전달하고 나면, 메시지는 밥의 메일박스에 놓이게 된다.

그렇다면 밥은 자신의 호스트에서 사용자 에이전트를 수행할 때, 밥의 메일 서버는 자신의 로컬 PC에 놓는 것이 자연스러워 보이겠지만, 이 방식에는 문제가 있다.

메일 서버가 메일박스를 관리하고 SMTP의 클라이언트와 서버 측 모두를 수행한다는 것으로 보아, 밥의 메일 서버는 언제든 도착하는 메일을 수신하도록 항상 켜져있어야 한다. 하지만 이는 인터넷 사용자에게 비현실적이다.(서버 켜놓는 다고 죽치고 컴퓨터 키는 사람은 얼마 없을 것이다.)

대신 일반 사용자는 로컬 호스트에서 사용자 에이전트를 수행하고 늘 켜져 있는 공유 메일 서버에 저장된 메일박스에 접근한다.

![Untitled](https://user-images.githubusercontent.com/76640167/210550668-901b362b-a929-4d44-abe8-da7bf21ca857.png)

그렇다면 왜 두 단계를 거쳐 메일을 보낼까?

그 이유는 앨리스의 메일 서버를 통해 중계하지 않으면 앨리스의 사용자 에이전트는 목적지 메일 서버에 도달할 수 없기 때문이다. 그래서 일단 앨리스의 메일 서버에 저장하고 30분 간격으로 밥에게 메일을 보내려고 한다.

하지만 로컬 호스트 PC에서 사용자 에이전트를 수행하는 수신자는 자신의 ISP 내부의 메일 서버에 있는 자신의 메시지를 어떻게 얻을 수 있을까?

수신자의 사용자 에이전트는 메시지를 얻기 위해 SMTP를 사용할 수 없다. 이는 SMTP가 push프로토콜인 반면에 메시지를 얻는 것은 pull동작(서버로 부터 데이터를 가져오는 동작)이기 때문이다.

그래서 밥은 2가지 방법으로 메시지를 얻을 수 있는데,

하나는 HTTP를 사용해 HTTP로도 통신하는 메일 서버와 통신하는 방법이고

다른 방법은 IMAP과 같은 인터넷 메일 접근 프로토콜을 사용하는 방법이다.
