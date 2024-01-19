## MAC 주소 (Media Access Control Address)란
- MAC 부계층 상에서, 노드 또는 장치의 식별을 위해,
  - 48 비트(6 바이트) 크기를 갖는, 하드웨어 상에 구현된 물리적 주소
  - 첫 24비트는 OUI(Organizational Unique Identifier) 제조업체의 식별코드
  - 다음 24비트는 해당 업체의 랜 카드의 정보를 담고 있다
- 네트워크 세그먼트의 데이터 링크 계층에서 통신을 위해 *네트워크 인터페이스*에 할당된다
- MAC 주소는 이더넷과 와이파이를 포함한 대부분의 [IEEE 802](https://ko.wikipedia.org/wiki/IEEE_802) 네트워크 기술에 네트워크 주소로 사용된다

***네트워크 인터페이스 카드 (NIC)***
- 일반적으로 회로 기판이나 칩과 같은 하드웨어 구성 요소로, 네트워크에 연결할 수 있도록 컴퓨터에 설치
- 이더넷이나 Wi-Fi와 같은 데이터 링크 계층 표준과 통신하는 데 필요한 물리 계층 회로를 구현

## IP, MAC 두 주소가 모두 필요한 이유
### IP Address 로만 통신하면 안되는 이유
- 라우팅 기법은 패킷에 포함된 IP Address를 추적해서 최단 경로를 선택
- IP Address는 논리적인 주소이기 떄문에,
  - 이 IP에서 IP Address로 목표된 네트워크로 추적해서 패킷을 보낸 뒤
  - 그곳에서 그 IP에 등록된 MAC Address로 주소가 변환되어 그 컴퓨터로 패킷을 전송하게 된다.
  - IP는 라우팅하는 주소, 실질적인 통신은 x

### MAC Address 로만 통신하면 안되는 이유
- 특정한 웹 서버를 찾는다고 가정하면,
  - ISP내의 모든 라우터들은 전세계 모든 서버들의 MAC 주소를 다 가지고 있어야 한다.
  - 즉, 하나하나 MAC 주소에 대해 라우팅 해줘야 한다.
- 하지만 IP 주소 체계는 변경 가능하고, 라우팅하는 데 효과적이다
- MAC 주소는 하드웨어 고유 주소, 대부분 변경 x, 라우팅 힘듬

### 결론
- 실질적인 통신은 바꿀 수 없는 하드웨어 주소인 MAC Address를 통해서 하고
- 논리적인 IP Address는 라우팅을 하기 위한 주소이기 때문에 둘 다 사용한다.

## ARP (Address Resolution Protocol)
- 논리적인 IP 주소를 (네트워크 계층), 물리적인 MAC 주소로 (데이터링크 계층)
  - 바꾸어주는 역할을 하는 주소 해석 프로토콜

### 동작
- 라우팅 테이블에서 통신하고자 하는 상대방 IP에 대한 Next Hop IP를 확인한다
  - Next Hop IP는,
    - 다른 LAN에 있는 상대방인 경우는 라우터의 IP
    - 동일 LAN에 있는 상대인 경우는 상대방의 IP
- Next Hop IP가 ARP Cache Table(이하 ARP 테이블)에 등록 되어 있는지를 검사
  - 이미 ARP 테이블에 상대방 IP에 대한 MAC 주소가 등록 되어 있으면,
  - 그 MAC 주소를 이용하여 이더넷 프레임의 목적지 MAC 주소를 채우고, 프레임을 전송한다
- Next Hop IP가 ARP 테이블에 없으면,
  - Next Hop IP에 대한 MAC 주소를 알아 오기 위해 *ARP 요청* 메시지를 전송
- *ARP 응답*을 수신하여 자신의 ARP 테이블을 업데이트 하고,
  - 이더넷 프레임의 목적지 MAC 주소 부분을 ARP Reply로 알게 된 상대 MAC 주소를 채워서 프레임을 전송 한다

**ARP 요청**
- 만일 이전에 전혀 통신한 경험이 없는 LAN의 라우터에 외부로부터 데이터 패킷이 전달되어 목적지 호스트를 찾을때
- 라우터가 최초로 하는 일은 ARP Request packet(ARP 요청 패킷)을 LAN의 전체 노드에 송출함   (브로드캐스트)  
- 이때, APP 요청 메세지에는,
  - 송신자 자신의 MAC 주소 및 IP 주소, 목적지 IP 주소를 채우지만, 
  - 목적지의 MAC 주소는 0 으로 채워넣음

**ARP 응답**
- ARP 요청 패킷에 포함된 IP 주소와 일치하는 Host는,
  - 자신의 IP 주소 및 물리주소를 채워놓은 ARP Reply packet(ARP 응답패킷)을 해당 라우터에게 송출함 (유니캐스트)
- 이로써 물리 주소 및 IP 주소 상호간의 관련 정보를 얻게됨 

## 참고
- [BIA   MAC Address, Burned-in address   MAC 주소, MAC 어드레스, 맥 어드레스, Ethernet 주소, 이더넷 주소, 맥 주소 - 정보통신기술용어해설](http://www.ktword.co.kr/test/view/view.php?nav=2&no=2294&sh=MAC+%EC%A3%BC%EC%86%8C)
- [MAC 주소 - wikipedia](https://ko.wikipedia.org/wiki/MAC_%EC%A3%BC%EC%86%8C)
- [[Network]IP address와 MAC address - 베스핀글로벌 테크센터 블로그](https://btcd.tistory.com/74)
- [ARP   Address Resolution Protocol - 정보통신기술용어해설](http://www.ktword.co.kr/test/view/view.php?nav=2&no=10&sh=ARP)
- [[네트워크] 라우터 ARP(Address Resolution Protocol) 동작절차에 대해 알아보자. - 시스코킹](https://ciscoking.tistory.com/7)