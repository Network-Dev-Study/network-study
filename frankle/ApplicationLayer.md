# 네트워크 애플리케이션의 원리

## 네트워크 모델

컴퓨터 네트워크는 데이터 통신의 복잡성을 관리하기 위해 각각의 역할과 책임을 가지는 계층으로 나누어져 있다.
계층화는 복잡한 네트워크 시스템의 구성을 이해하고 쉽게 만들어준다.

가장 널리 알려진 네트워크 계층 구조로는 OSI 모델, TCP/IP 모델이 있다.

| OSI 7 Layer           |
| --------------------- |
| 7. Application Layer  |
| 6. Presentation Layer |
| 5. Session Layer      |
| 4. Transport Layer    |
| 3. Network Layer      |
| 2. Data Link Layer    |
| 1. Physical Layer     |

| TCP/IP 4 Layer             |
| -------------------------- |
| 4. Application Layer       |
| 3. Transport Layer         |
| 2. Internet Layer          |
| 1. Network Interface Layer |

## 컴퓨터 통신

컴퓨터에서 실행되는 애플리케이션끼리의 소통 매커니즘에 대해 살펴보자.

애플리케이션의 소통을 OS(Operating System) 관점에서 바라보면 애플리케이션(=프로세스(Process))이며, 프로세스는 고유한 **소켓**(Socket)이란 애플리케이션 계층과 트랜스포트 계층 간의 인터페이스를 통해 네트워크상에서 서로 메시지를 주고 받으며 통신한다.

소켓은 주로 **IP 주소와 Port 번호의 조합**으로 구성되어 있으며, 이를 통해 목적지 프로세스를 특정할 수 있다.

- IP 주소: 호스트(컴퓨터)의 주소
    - 32bit 숫자로 표현 (192.168.1.1)
- Port 번호: 호스트 내에서 실행 중인 프로세스들 중 목적지 프로세스를 식별하기 위해 사용되는 번호
    - 0 ~ 65535까지의 16비트 범위 숫자로 구성됨
        - 0 ~ 1023 (Well-Known Port): 자주 사용되는 프로그램들에 대한 포트들이 규약으로 지정되어 있음
            - [TCP/UDP Well-Known Port List](https://ko.wikipedia.org/wiki/TCP/UDP%EC%9D%98_%ED%8F%AC%ED%8A%B8_%EB%AA%A9%EB%A1%9D)
        - 1024 ~ 49151 (Registered Port): Well-Known Port와 반대로 잘 알려지지 않은 프로그램을 위해 사용됨
        - 49152 ~ 65535 (Dynamic Port): 서버가 클라이언트를 식별할 때 사용됨

웹 브라우저 주소창에 www.naver.com 같은 URL로 검색하면 내부적으로 **DNS**란 시스템을 통해 URL을 IP 주소로 변환하고 웹 서버의 포트인 80으로 해석하여(HTTP로 가정) 실질적으로는 IP:Port 형태로 검색을 수행한다.

### 애플리케이션과 트랜스포트

- 소켓은 애플리케이션 프로세스와 트랜스포트 프로토콜 간의 인터페이스임을 상기
    - 송신 프로세스에서 소켓을 통해 메시지 송신한다면, 트랜스포트 프로토콜은 네트워크상에서 메시지를 수신 프로세스의 소켓으로 이동시키는 역할을 수행
- 트랜스포트 계층 프로토콜은 애플리케이션에게 넓은 범위에서 신뢰적 데이터 전송, 처리율, 시간, 보안을 제공함
    - 애플리케이션 목적에 따라 여러 트랜스포트 프로토콜 중 어느 것을 사용할지 적절히 선택해야 함

## Web, HTTP

### Web (World Wide Web, WWW)

- 웹은 인터넷 상의 정보공간을 의미하며, HTTP는 웹에서 데이터를 전송하는 방법을 정의한 프로토콜이다
- 웹은 HTTP를 기반으로 웹 서버에서 웹페이지에 필요한 리소스를 요청하고 받아온다

### HTTP (HyperText Transfer Protocol)

- HTTP는 말 그대로 HyperText를 전송하는 프로토콜
    - HyperText란 일반 문자를 포함하여 링크 등 다양한 Object를 포함하는 텍스트
- Client-Server 모델로 요청과 응답을 주고받는 프로토콜
- TCP 기반으로 동작
    - 신뢰성 보장
        -	순서 보장, 에러 검출 및 재전송, 흐름 제어, 혼잡 제어
    - 연결지향형
        - 요청/응답 메시지를 보내기 전, 먼저 3-way handshake를 통해 TCP 커넥션을 먼저 맺음
            - 매번 handshake 하는 과정은 오버헤드이므로 HTTP 버전이 오르면서 개선되었음
                - HTTP/1.0: 매 요청마다 새로운 커넥션을 맺음
                - HTTP/1.1: 'Keet-Alive' 매커니즘을 통해 여러 요청을 하나의 커넥션을 통해 처리할 수 있도록 개선
                - HTTP/2.0: 기본적으로 커넥션을 유지함. 서버 혹은 클라이언트가 종료를 원하거나 네트워크 활동이 일정 시간 동안 없으면 해제

- Stateless (무상태)
    - 요청/응답을 보내면 이전 상태를 기억하지 않음

#### HTTP 요청/응답 메시지

- HTTP는 클라이언트와 서버간에 요청/응답 메시지를 전송하며 소통한다.

    - Request Message (Client)

  ```
  GET /restapi/v1.0 HTTP/1.1
  Accept: application/json
  Authorization: Bearer xxx
  - Response Message (Server)	
  ```

  TTP/1.1 200 OK
  Date: Mon, 23 May 2005 22:38:34 GMT
  Content-Type: text/html; charset=UTF-8
  Content-Encoding: UTF-8
  Content-Length: 138
  Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
  Server: Apache/1.3.3.7 (Unix) (Red-Hat/Linux)
  ETag: "3f80f-1b6-3e1cb03b"
  Accept-Ranges: bytes
  Connection: close

  <html>

  <head>
  	<title>An Example Page</title>
  </head>

  <body>
  	Hello World, this is a very simple HTML document.
  </body>
  </html>

- https://ko.wikipedia.org/wiki/HTTP

## 쿠키

- 쿠키는 HTTP의 Stateless 특성상, 상태 정보를 유지하기 위해 도입됨
- 특징
    - 사용자 로그인, 장바구니, 오늘만 팝업 닫기
    - 사용자 브라우저에 저장되므로 쉽게 노출되어 보안에 취약함
    - 쿠키의 구조는 이름, 값, 만료 날짜, 경로, 도메인 등 속성이 포함됨
- 동작 원리
    1. 웹 서버는 응답 헤더에 'Set-Cookie'에 쿠키 값을 함께 전송
    2. 이후 브라우저는 동일 서버에 대한 요청에는 'Cookie' 요청 헤더에 쿠키 값을 포함하여 전송

## 웹 캐시 (Proxy Server)

- Client와 Server 사이에 프록시 서버를 두어 다양한 목적으로 사용할 수 있다.
- 목적
    - **웹 캐싱**
        - 요청이 잦은 리소스에 대해 프록시 서버에 저장해두면, 이후 클라이언트의 요청에 필요한 리소스는 서버까지 가지 않고 프록시 서버에서 미리 반환할 수 있음
            - **응답 속도 향상**
                - 프록시에서 미리 준비된 리소스를 반환하므로 실제 원본 서버에서 받아오는 것에 비해 응답 속도가 더 빠름
            - **트래픽 부하 절감**
                - 트래픽 부하를 개선하기 위해 회선을 증설하는 것이 아닌 캐싱을 통해 성능을 올려 실제 비용 절감 효과
    - 콘텐츠 필터링, 보안 등..

- **캐시 사용시 주의할 점**

    - 원본 서버의 데이터가 변경되어도, 캐시에는 아직 과거 데이터가 남아 클라이언트가 이전 정보를 보게될 수 있음

    - 캐시된 데이터는 언젠가는 만료되기에 때문에 캐시를 만료시키는 기준이 필요함
        - 만료 시간이 너무 빠르면 캐시 효과가 줄고, 길다면 오래된 데이터를 제공할 위험이 있음

    - 캐시 유효성 검사 또한 네트워크 리소스를 사용하므로, 네트워크 오버헤드를 발생시킴

    - 캐시 스토리지의 공간은 한정적이므로 어떤 데이터를 캐싱할지, 공간이 부족하다면 어떤 데이터를 삭제할지 전략을 결정해야 함



    ### 조건부 GET

    - 캐시 데이터의 유효성 검사를 위해 `ETag` 또는 `Last-Modified` 를 통해 서버에 리소스 변경 여부를 확인 (혼용해서 사용 가능하지만, ETag가 더 강력해서 우선됨)

      - `ETag`: 리소스의 버전을 나타내는 문자열
        - 요청 헤더`If-None-Match`에 캐시된 리소스의 ETag 값을 담아 보내고, 서버에서 원본 리소스와 일관성을 검사
      - `Last-Modified`: 리소스의 최종 수정일자
        - 요청 헤더`If-modified-since` 날짜와 `Last-Modified` 날짜를 비교하여 Last-Modified 날짜가 더 최신이면 예전 리소스로 판단
          - `If-modified-since`: 클라이언트측에 캐시되어 있는 리소스의 최종 수정일자

      - 웹서버에서 캐싱 리소스를 최신 버전으로 판단하면 `304 Not Modified` 회신

        ![With a stale cache, the conditional request is sent. The server can determine if the resource changed, and, as in this case, decide not to send it again as it is the same.](https://developer.mozilla.org/ko/docs/Web/HTTP/Conditional_requests/httpcache2.png)

      - 웹서버에서 캐싱 리소스를 이전 버전으로 판단하면 `200 OK`, 새로운 리소스와 함께 `Etag` 또는 `Last-Modified`를 헤더에 포함해 응답 및 새로운 캐싱 리소스로 저장

        ![In the case where the resource was changed, it is sent back as if the request wasn't conditional.](https://developer.mozilla.org/ko/docs/Web/HTTP/Conditional_requests/httpcache3.png)

# DNS: Domain Name System

## DNS란?

이전에 살펴봤듯 프로세스간 통신을 하기 위해서는 소켓 간에 TCP 연결을 맺어야 하고, 이를 위해서는 프로세스를 식별하기 위해 IP와 Port가 필요하다고 했었다. 일반적으로 인터넷 환경에서 통신을 할 때는 숫자로 표현된 IP와 Port를 직접적으로 사용하기 보다는 `www.google.com` 같이 사람에게 친숙하고 기억하기 쉬운 **호스트 이름(hostname)**을 이용한다.

사람은 인식하기 쉬운 호스트 이름을 좋아하지만, 라우터는 고정된 길이의 계층구조를 갖는 IP 주소를 좋아한다. 이를 위한 절충안으로 호스트 이름을 IP 주소로 변환해주는 시스템이 바로 DNS이다. 클라이언트 입장에서 DNS는 호스트 이름을 IP로 변환해주는 단순한 시스템으로 보이지만, 변환 외의 다른 서비스들을 제공하며 내부적으로 꽤 복잡한 과정을 거친다.

DNS는 UDP상에서 수행되는 애플리케이션 프로토콜이며 포트 번호는 53을 이용한다. TCP가 아닌 UDP를 이용하도록 설계된 이유는 다음과 같다.

1. DNS에서 다뤄지는 패킷의 사이즈는 굉장히 작아 유실될 확률이 낮음
2. TCP handshake 과정을 거치지 않아 성능을 극대화

### DNS가 제공하는 서비스

DNS는 호스트 이름을 IP 주소로 변환하는 것 외에 추가적인 서비스를 제공한다.

- **호스트 에일리어싱(host aliasing)**: 호스트 이름은 하나 이상의 별칭을 가질 수 있다.
    - `relay1.west-coast.enterprise.com` 같은 호스트 이름을 `enterprise.com`, `www.enterprise.com` 같이 더 단순한 별칭으로 지정할 수 있음
    - 여기서 `relay1.west-coast.enterprise.com`는 **정식 호스트 이름**이라고 불리며, DNS는 별칭 호스트 이름에 대한 정식 호스트 이름을 얻기 위해 이용될 수 있음

- **부하 분산(load distribution)**: 동일 호스트 이름에 대해 여러 웹 서버로 분산되어 있는 경우, 트래픽을 분산하는 역할을 한다.
    - `www.naver.com` 호스트 이름에 실제 물린 PC를 5대로 가정하면, DNS는 해당 호스트에 대한 IP 주소 집합 전체를 갖고 있음
    - `www.naver.com` 에 대한 질의 요청이 오면, IP 주소 집합에서의 IP 주소를 순환식으로 내보내어 부하 분산
        - 라운드 로빈 방식

## 분산 설계

만약 DNS 서버가 전셰계에 하나라면 어떨까? 다음과 같은 문제들이 발생할 수 있다.

- **서버 고장**: 단일 서버 구성이므로 해당 서버에 문제가 생기면 전체 인터넷에 장애다.
- **먼 거리의 중앙 집중 데이터베이스**: 인터넷은 전세계에서 사용된다. 서울과 부산에서 주고받는 요청의 RTT 보다 서울과 뉴욕에서 주고받는 RTT가 더 길 것이고 이는 곧 심각한 지연 문제로 이어질 수 있다.
- **유지관리**: 모든 인터넷 호스트에 대한 레코드를 유지하고 신규로 추가되는 호스트를 반영하기 위해 갱신해야 한다. 이를 하나의 장소에서 모두 처리하기엔 너무 거대하다.

즉, 위와 같이 단일 DNS 서버에 있는 중앙 집중 방식은 확장성이 전혀 없다. 따라서 결과적으로 현재 DNS의 서버들은 계층구조로 분산되도록 설계되었다.

### DNS 서버 계층구조

![img](https://velog.velcdn.com/images/imphj3/post/dd64d7a1-b28c-4998-b327-a3bc5f639cb3/image.png)

확장성을 위해 DNS는 수많은 서버들과 함께 계층 형태로 구성되어 전 세계에 분산되어 있다. 그림처럼 세 가지 유형의 DNS 서버가 있다.

- **Root DNS 서버**: TLD 서버의 IP 주소를 제공
- **TLD(Top-Level-Domain) DNS 서버**: com, org, edu 같은 상위 레벨 도메인과 kr. uk, jp 같은 국가의 상위 레벨 도메인을 관리하는 서버이자 책임 DNS 서버에 대한 IP 주소를 제공한다.
- **책임(Authoritative) DNS 서버**: DNS 레코드에서 호스트 이름의 IP를 찾아서 제공
    - 대중적인 호스트를 소유한 기관은 반드시 호스트 이름을 IP 주소로 매핑하는 공개적인 DNS 레코드를 재공해야 함

### 로컬 DNS

앞서 본 DNS 서버들과 다른 형태의 DNS 서버인 로컬 DNS 서버는 ISP에서 운용되어 대체로 클라이언트와 가까이 위치해있다. 호스트가 DNS 질의를 보내면, 프록시로 동작하고 있는 로컬 DNS 서버가 가장 먼저 받게 되고, 그후 DNS 서버 계층으로 질의를 전달한다.

#### DNS 캐싱

DNS는 성능 향상과 네트워크 상에서의 DNS 메시지 수를 줄이기 위해 캐싱을 사용한다.

`www.naver.com`에 대한 IP 주소를 DNS를 통해 얻었다면 로컬 메모리에 정보를 저장하고, 이후 동일 호스트 이름에 요청에 대해서는 로컬 DNS 서버에서 곧바로 IP를 응답한다. (TTL은 보통 2일)

## DNS 레코드

DNS 서버들은 호스트 이름을 IP 주소로 매핑하기 위한 **자원 레코드(resource record, RR)**를 저장한다.

자원 레코드는 다음과 같은 필드를 포함하는 4개의 튜플(tuple)로 구성되어 있다.

`(Name, Value, Type, TTL)`

TTL(Time-To-Live)는 해당 레코드의 생존 시간을 의미한다. Name과 Value의 의미는 Type에 따른다.

- Type = A
    - Name: 호스트 이름(hostname), Value: 호스트 이름에 대한 IP 주소
        - `(relay1.bar.foo.com, 147.35.12.125, A)`
- Type = NS
    - Name: 도메인(foo.com), Value: 책임 DNS 서버의 호스트 이름
        - `(foo.com, dns.foo.com, NS)`

- Type = CNAME
    - Name: 별칭 호스트 이름, Value: 정식 호스트 이름
        - `(foo.com, relay1.bar.foo.com, CNAME)`
- Type = MX
    - Name: 별칭 호스트 이름, Value: 메일 서버의 정식 호스트 이름
        - `(foo.com, mail.bar.foo.com, CNAME)`

## 정리

![DNS 조회 및 웹 페이지 쿼리 완료 - 10단계](https://cf-assets.www.cloudflare.com/slt3lc6tev37/1NzaAqpEFGjqTZPAS02oNv/bf7b3f305d9c35bde5c5b93a519ba6d5/what_is_a_dns_server_dns_lookup.png)

Example.com를 접속하는 과정에서 DNS 질의 흐름 예시

1. 브라우저에 example.com 검색
2. 로컬 DNS 서버에 example.com 질의
    - 로컬 DNS 서버에 example.com 레코드가 캐싱되어 있다면 IP 주소 반환
3. TLD com 서버에 접속, 서버는 example.com에 해당하는 책임 서버 반환
    - TLD com 서버 주소가 로컬 DNS 서버에 캐싱되어 있지 않으면 루트 DNS 서버로 접속
4. 책임 서버에 접속, 서버는 example.com에 해당하는 IP 주소를 응답받음
5. IP 주소를 통해 TCP 연결을 맺고 HTTP 요청 전송

# 소켓 프로그래밍

인터넷에서 네트워킹은 종단 시스템에 존재하는 클라이언트 프로그램과 서버 프로그램으로 구성된다. 두 프로세스는 서로의 소켓을 통해 읽고(read) 쓰며(write) 서로 통신한다. 프로세스가 집이라면 소켓은 문으로 비유할 수 있다.

애플리케이션 개발자는 소켓의 애플리케이션 계층에 대한 제어권을 갖지만 트랜스포트 계층에 대한 제어권은 거의 없다.

## UDP/TCP 소켓

소켓의 종류로는 UDP 소켓과 TCP 소켓이 있다. 이름을 보면 알 수 있듯이 UDP 소켓은 UDP 프로토콜, TCP 소켓은 TCP 프로토콜을 이용한다. 따라서 개발자는 필요에 따라 UDP/TCP 중 어느 프로토콜을 사용할지 결정할 수 있다.

- TCP 소켓
    - 연결 지향형
        - 메시지를 전송하기 이전에 우선 TCP 연결을 맺어야 함
    - 신뢰적 바이트 스트림 채널 제공
        - 데이터는 채널을 통해 흐르며 순서를 보장함
- UDP 소켓
    - 비연결형
        - 연결을 맺지 않고 패킷을 바로 전송
        - 목적지까지 패킷이 도착할지 확신할 수 없음

### UDP 소켓 통신

![img](https://img.brainkart.com/imagebk10/jwSx0C4.jpg)

클라이언트가 서버에 메시지를 보내려면 서버 프로세스가 우선 실행되고 있어야 하며, 클라이언트 프로세스가 패킷을 소켓으로 보내기 전에, UDP를 사용 시에는 우선 패킷에 서버의 IP 주소와 Port를 붙여야 한다. 이를 통해 소켓 밖 인터넷에서 수신 호스트와 프로세스로 찾아갈 수 있다.

#### 클라이언트

- Socket(주소군, 소켓 종류): UDP 소켓 생성
    - IPv4, SOCK_DGRAM
- Sendto(메시지, IP Addr, Port): 보낼 패킷에 서버의 IP 주소와 Port를 붙여 보냄
- Recvfrom(): 서버로부터 메시지, 서버의 IP 주소, Port를 전달받음
- Close(): 소켓을 닫고 프로세스 종료

#### 서버

- Socket(주소군, 소켓 종류): UDP 소켓 생성
- Bind(Port): 포트 번호를 서버의 소켓에 할당
- Recvfrom(): 클라이언트로부터 메시지(데이터그램) 받기를 기다리는 상태 (Blocking)
- Sendto(): 메시지와 클라이언트 주소를 보냄

### TCP 소켓 통신

![Fundamentals of TCP Socket Programming in Java | by Pavindu Lakshan |  Javarevisited | Medium](https://miro.medium.com/v2/resize:fit:1070/1*_mOKz1BDse7Vm9eqcfKY9A.png)

앞서 소개했듯이 UDP와 달리 **TCP는 연결지향 프로토콜**로, 서로 메시지를 보내기 전에 먼저 TCP 연결을 맺어야 한다. TCP 연결의 한쪽은 클라이언트 소켓, 다른쪽은 서버 소켓에 연결된다.

서버 프로세스 생성 시 welcome 소켓을 만들고, 이후 클라이언트 요청이 들어오고 TCP 연결까지 맺어지면 요청 클라이언트에게 지정되는 새로운 TCP 소켓을 생성하며 해당 소켓과 클라이언트 소켓은 파이프로 연결된다. 그리고 서로 바이트를 송수신할 수 있고, 보낸 순서대로 받을 수 있도록 TCP가 보장한다.

#### 클라이언트

- Socket(주소군, 소켓 종류): TCP 소켓 생성
    - IPv4, SOCK_STREAM
- Connect(): 서버로 TCP 연결을 시작
    - 3-way handshake 수행 후 클라이언트와 서버 간에 TCP 연결이 설정됨
- Write(): TCP 연결을 통해 메시지를 보냄
    - UDP와 달리 이미 연결된 상태이므로 목적지를 붙이지 않아도 됨
- Read(): 서버로부터 메시지를 받음
- Close(): 소켓을 닫고 클라이언트와 서버 간에 TCP 연결을 닫음
    - Close 메시지를 보내 연결 닫음을 알림

#### 서버 측

- Socket(주소 체계, 소켓 타입): 소켓 API를 통해 TCP 소켓 생성
- Bind(포트): 지정할 포트에 소켓을 연결
- Listen(커넥션 수): 클라이언트로부터 연결 요청을 받도록 하고, 파라미터로 연결의 최대 수 지정
- Accept(): 클라이언트 요청을 대기(blocking)하고, 연결이 들어오면 아래 동작을 수행
    1. 클라이언트에게 지정된 `connectionSocket`이란 새로운 소켓을 생성
    2. 클라이언트와 서버가 핸드쉐이킹을 완료하면 클라이언트 소켓과 `connectionSocket` 간에 TCP 연결 생성
    3. 서로의 연결(파이프)를 통해 바이트를 보낼 수 있고, TCP의 특성상 한쪽에서 보낸 모든 바이트가 다른쪽에 모두 순서대로 도착함을 보장
- Read(): 클라이언트가 보낸 메시지를 읽음
- Write(): 클라이언트에게 보낼 메시지를 씀
- Close(): 메시지를 보낸 후 connectionSocket을 닫음. 하지만 서버 소켓은 생성되어 있는 상태이므로 또다른 클라이언트에게 요청이 오기 전까지 blocking 상태로 되돌아감