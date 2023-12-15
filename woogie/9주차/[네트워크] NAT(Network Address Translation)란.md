## NAT (Network Address Translation)
- 1개의 실제 공인 IP 주소에, 다량의 가상 사설 IP 주소를 할당 및 매핑하는,
  - 1:1 또는 1:多 주소 변환(Address Translation) 방식
- NAT 및 DHCP 비교
  - NAT: 사설 IP 주소공간을 별도로 정하고는, IP 주소변환에 중점을 둠
    - 기본적으로, 인터넷 상에 동일 IP 주소를 재사용할 수 있게 하는 것
  - DHCP : 기확보된 공인 IP 주소 Pool를 집단 공유하며, 사용후 반납
    - 기본적으로, 제한된 IP 주소를 공유케 하여 절약하려는 것

## NAT의 사용 이유 및 유의점
- IPv4 주소 고갈 및 라우팅 테이블 대형화(Routing Scalability)에 대한 해소책
- 공인 IP 주소의 효율적 공유 및 절약
  - 공인 IP 주소 사용시 ISP社를 바꿀 때 마다, 모든 컴퓨터 주소를 바꿔야하는 등의 단점 해소
- 또한, 주소변환을 통해 내부 사설망 보안 및 Load Balancing 등도 가능
  - 통상, 방화벽 등과 결합되어 함께 기능 수행
  - 주소변환 규칙을 외부에서 알 수 없으므로 내부 망에 대한 정보가 외부에 노출 안됨
  - 주로, 외부망과 사설망 간에 연결점에 있는 라우터에서 수행됨
- 그러나, NAT는 IP 주소 변환 뿐만 아니라 IP 주소, TCP Checksum, UDP Checksum 등 서로 관련된 많은 부분도 함께 변경해야 하므로,
  - 매 연결 단위로 연결 상태 정보를 필요로 하고, 
  - 다수의 프로토콜 계층에 걸쳐 동작해야 할 필요가 있는 등 *FTP*, *SIP* 등에서 문제 발생

***FTP, SIP***
- FTP: File Transfer Protocol, 파일 전송 프로토콜
  - FTP는 데이터 전송에 두 가지 연결을 사용
    1. 제어 연결(control connection)
    1. 데이터 연결(data connection)
  - 일반적인 FTP는 제어 연결에서 명령을 주고받고 데이터 연결에서 파일을 전송
  - NAT는 주로 TCP 연결을 관리하므로, FTP의 데이터 연결에서 문제가 발생할 수 있다.
- SIP: Session Initiation Protocol, 세션 개시 프로토콜
  - SIP은 VoIP(Voice over IP)와 같은 멀티미디어 통신을 위한 프로토콜
  - SIP은 두 호스트 간에 세션을 설정하고 해제하기 위해 여러 TCP 및 UDP 포트를 사용할 수 있다
  - NAT는 IP 주소 및 포트 번호를 변환하기 때문에 SIP의 동적인 포트 사용에 대해 문제를 일으킬 수 있다

## NAT의 주요 기능
- IP Masquerading (IP 위장, IP 마스커레이드)
  - 외부망에서 볼 때 내부망 여러 호스트들이 단일 NAT 서버 하나로만 보임
    - (외부) ↔ (NAT 1대) : (多 호스트들)
  - 단 하나의 공인 IP로 내부 모든 PC가 대표 IP 주소를 공유 사용
  - IP 계층,전송 계층 모두에서 변환(맵핑)으로, 마치 가면(위장,Masquerading) 효과 있음
- Port Forwarding (포트 포워드)
  - 트랜스포트계층에서, 내부망 여러 호스트들이 외부망과 각각 별도로 연결짓게 할 수 있음
    - 서비스 포트 번호 별로 내부의 사설 IP로 지정된 호스트로 전달
- Load Balancing
  - 각 포트별로 트래픽을 균형있게 함
- *패킷 필터링*

***패킷 필터링(packet filtering)***
- 특정 송신원 주소나 발신원 주소 등을 가진 패킷의 통과를 제한하는 것
- 라우터나 브리지 또는 칩입 차단 시스템(firewall) 등에서 사용하는 기술이다
- 송신자/수신자의 IP 주소나 TCP/UDP 포트 번호를 근거로 패킷 필터링하는데 고속으로 필터링되지만 안전성은 비교적 취약한 편이다

## NAT의 방식 구분
- Basic NAT 방식 (IP 주소변환 만 수행)
  - 정적 NAT 방식
    - 수동으로 외부 공인 IP와 사설 IP를 매핑 (1:1)
  - 동적 NAT 방식
    - 사설 IP 주소를 풀(Pool)화하여 공인 주소로 자동 매핑 (1:多)
- NAPT 또는 PAT 방식
  - IP 주소 뿐만 아니라 포트 번호 등까지도 포함시켜 내부 호스트를 구분

## NAPT (Network Address Port Translation) 또는 PAT (Port Address Translation), 그리고 문제점
- 원래는, (Basic NAT)
  - IP 주소 변환(공인 IP 주소 → 사설 IP 주소) 만을 이용하는 기본적인 NAT 방식 이었음
    - 공인 IP 주소: 인터넷이 사용자를 찾을 수 있도록 사용자를 식별하는 역할
    - 사설 IP 주소: 사설 네트워크에서 다른 장치와 안전하게 연결하기 위해 사용, 
      - 동일한 네트워크의 각 장치에는 고유한 사설 IP 주소가 할당
  - 즉, IP 헤더의 목적지와 출발지 주소만 변경하고, IP 패킷 내부에 다른 어떤 처리도 안함
- 그러다가, (NAPT 또는 PAT)
  - TCP, UDP 헤더 내 포트 번호 변환을 통해 (IP 주소 및 포트 번호를 모두 함께 결합시킴),
  - 하나의 IP 주소 및 여러 포트 번호에 의해, 
  - 여러 내부 호스트 주소 및 포트를 연결이 가능해짐 (IP Masquerading)
- 이에따라, 구현이 복잡해지는 등 문제점 발생
  - 프로토콜,응용 마다 NAT 구현이 달라짐
  - NAT 자신이 연결 마다 내부 상태 정보(내부 호스트 IP 주소,포트 번호 등)를 보존 필요 
  - e.g. SIP를 이용한 어플리케이션 등

## Twice NAT 방식
- 출발지 주소,목적지 주소 모두를 변환하는 방식
  - e.g. 동일 회사 내 멀리 떨어진 두 지사 간에 인터넷 경유 연결 등

## 참고
- [NAT, NAPT, PAT   Network Address Translation, Network Address and Port Translation   NAT 방식, NAPT 방식 - 정보통신기술용어해설](http://www.ktword.co.kr/test/view/view.php?nav=2&no=1676&sh=Network+Address+Translation)
- [공인 IP, 사설 IP… 다양한 IP 유형의 차이는? - NordVPN](https://nordvpn.com/ko/blog/public-ip-and-private-ip/)
- (패킷 필터링 - 한국정보통신기술협회)[http://word.tta.or.kr/dictionary/dictionaryView.do?subject=%ED%8C%A8%ED%82%B7+%ED%95%84%ED%84%B0%EB%A7%81]