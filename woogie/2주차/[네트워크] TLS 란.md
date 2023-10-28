## TLS 란
- Transport Layer Security
- 인터넷 상의 커뮤니케이션을 위한 개인 정보와 데이터 보안을 용이하게 하기 위해 설계된 프로토콜
- 주요 사용 사례는 웹 응용 프로그램과 서버 간의 커뮤니케이션을 암호화하는 것
- TLS는 전송계층의 암호화 방식이기 때문에 HTTP뿐만 아니라 FTP, XMPP등 응용 계층(Application Layer)프로토콜의 종류에 상관없이 사용할 수 있다
- TLS는 또한 이메일, 메시지, 보이스 오버 IP(VoIP) 등 다른 커뮤니케이션을 암호화하기 위해 사용된다.

***VoIP***
- Voice over Internet Protocol
- 인터넷을 통해 전화를 걸 수 있는 방법
- 기존 유선 전화 시스템과 달리 인터넷은 연결된 사람 간에 실시간으로 오디오 신호를 전달하도록 설계되지 않았다.
- 이를 가능하게 하기 설계된 것이 VoIP, 오늘날 VoIP는 오디오 및 비디오 실시간 통신 모두에 아주 효율적인 방법이다

## TLS와 SSL의 차이점
- TLS는 Netscape가 개발한 SSL(Secure Sockets Layer)이라고 불리는 이전의 암호화 프로토콜에서 발전한 것
- TLS 버전 1.0은 SSL 버전 3.1로서 개발을 시작했지만 Netscape와 더 이상 연관이 없음을 명시하기 위해 발표 전에 프로토콜의 이름이 변경되다

## TLS와 HTTPS
- HTTPS는 HTTP 프로토콜에서 TLS 암호화를 구현한 것

## TLS의 역할
TLS 프로토콜은 암호화, 인증, 무결성이라는 세 가지 주요 요소를 달성합니다.
- 암호화: 제3자로부터 전송되는 데이터를 숨긴다
- 인증: 정보를 교환하는 당사자가 요청된 당사자임을 보장한다
- 무결성: 데이터가 위조되거나 변조되지 않았는지 확인한다

## TLS 인증서란
- 웹 사이트나 응용 프로그램이 TLS를 사용하기 위해서는 원본 서버에 TLS 인증서가 설치되어 있어야 한다.
- 인증 기관이 도메인을 소유한 사람 혹은 비즈니스에게 TLS 인증서를 발행한다.
- 인증서는 서버의 공개 키와 더불어 누가 도메인 소유자인지에 대한 중요한 정보를 포함하며, 이 두 가지는 모두 서버의 신원을 확인하는 데 중요

## TLS Hand Shake (1.2)
![tls_hand_shake(1.2)](./images/tls_hand_shake(1.2).png)

- 클라이언트와 서버는 헬로 메시지로 기본적인 정보를 송수신 (1, 2)
- 서버는 서버가 사용하는 TLS 인증서를 전달 (3, 4, 5)
- 클라이언트는 암호화 통신에 사용할 대칭키를 생성하고 사이를 서버에 전달(6)
- 클라이언트는 암호화 통신에 사용 가능한 암호 알고리즘과 해시 알고리즘 목록을 서버에 전달. (7, 8)
- 서버도 알고리즘 목록을 교환후 핸드셰이크가 종료되며 이제 클라이언트와 서버는 암호화 통신에 필요한 대칭키를 서로 보유.(9, 10)

## TLS와 웹 애플리케이션에 성능
- LS의 최신 버전은 웹 응용 프로그램 성능에 거의 영향을 미치지 않는다.
- TLS 1.3 2018년에 발표된 가장 최신 버전
- TLS Handshake는 몇 밀리세컨드로 프로세스를 단축하며 2회 왕복 대신 1회 왕복만을 요구
- 사용자가 전에 웹 사이트에 연결한 적이 있으면, TLS Handshake를 생략하는 것도 가능하다

## TLS Hand Shake (1.3)
![tls_hand_shake(1.3)](./images/tls_hand_shake(1.3).jpeg)

- 클라이언트는 헬로 메시지와 함께 지원되는 암호세트 목록을 보내고, 서버가 선택할 가능성이 큰 계약 프로토콜을 추측하며, 특정 키 프로토콜에 대한 키 공유를 전송 (1)
- 서버 헬로 메시지와 함께 주요 계약 프로토콜로 응답, 키 공유
- 클라이언트는 서버 인증서를 확인하고 서버의 키 공유를 가지고 있기 때문에 키를 생성한 후 "Client Finished" 메시지를 보낸다 (3)

## 참고
- [TLS(Transport Layer Security)는 무엇입니까? - cloudflare](https://www.cloudflare.com/ko-kr/learning/ssl/transport-layer-security-tls/)
- [보이스 오버 인터넷 프로토콜이란? - cloudflare](https://www.cloudflare.com/ko-kr/learning/video/what-is-voip/)
- [SSL/TLS 와 HTTPS - lesstif](https://www.lesstif.com/ws/ssl-tls-https-43843962.html)
- [TLS 1.3 handshake - 한국기업보안](https://m.blog.naver.com/PostView.naver?blogId=ucert&logNo=221234082080&categoryNo=37&proxyReferer=)