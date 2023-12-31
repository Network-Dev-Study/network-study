# 비연결형 트랜스포트: UDP

TCP 대신에 UDP를 사용하여 애플리케이션을 개발한다면 애플리케이셔은 거의 IP와 직접 통신하는 셈이다.

DNS가 대표적인 UDP를 사용하는 애플리케이션 계층 프로토콜 인데, DNS외에도 여러 경우에 UDP가 TCP보다 사용하는데 적합할 수 있다.

- 무슨 데이터를 언제 보낼지에 대해 애플리케이션 레벨에서 더 정교한 제어: UDP는 TCP와 다르게 직접 바로 보낼 수 있다. 반면 TCP는 혼잡제어 때문에 속도가 느려질 수 있고, 만약 조금의 데이터 손실을 허용하는 애플리케이션이라면 신뢰적인 전달이 필요없다.
- 연결 설정이 없음: UDP는 연결을 설정하기 위한 어떤 지연도 없다. 만약 DNS가 TCP에서 동작한다면 많이 느려질 것이다. 그리고 HTTP에서 TCP설정 연결로 인해 문서를 다운받으려면 지연이 걸린다.(실제로 크롬은 QUIC를 사용한다.)
- 연결 상태가 없음: TCP는 종단 시스템에서 연결 상태를 유지한다. 하지만 UDP는 연결 상태를 유지하지 않는데, 이것 때문에 일반적으로 좀 더 많은 액티브 클라이언트를 수용할 수 있다.
- 작은 패킷 헤더 오버헤드: TCP는 세그먼트마다 20바이트의 헤더 오버헤드를 갖지만, UDP는 단지 8바이트의 오버헤드를 가진다.

인기있는 애플리케이션들 중 UDP를 쓰는 애플리케이션들이 있는데,

HTTP/3, 네트워크 관리(SNMP, 5.7절), DNS, 인터넷 폰(통상 독점 프로토콜), 원격 파일 서버(NFS) 등이 있다.

하지만 단점들도 많은데,

혼잡 제어를 사용하지 못해 라우터에 많은 패킷 오버플로가 발생하여 소수의 UDP패킷만 통과하거나

연결 상태를 확인하지 않으므로 발생하는 손실률이 발생할 것이다.

만약 애플리케이션에서 신뢰성을 제공한다면, UDP를 사용해도 신뢰적인 데이터 전송을 할 수 있다.

### UDP 세그먼트 구조

![Untitled](https://user-images.githubusercontent.com/86337233/211334784-c3a87f6f-85ca-414b-9c72-9b40e90bb5e1.png)

UDP 헤더는 2바이트씩 구성된 단 4개의 필드만을 갖는다.

체크섬은 UDP 세그먼트 이외에 IP헤더 일부 필드도 계산하진만, 전체 그림을 보기위해 이런 세부사항은 무시한다.

길이 필드는 헤더를 포함한 UDP세그먼트의 길이(바이트 단위)를 나타낸다.

### UDP 체크섬

오류 검출을 위한 것이다. 즉, 세그먼트가 출발지로부터 목적지로 이동했을 때, UDP 세그먼트 안의 비트에 대한 변경사항이 있는지 검사하는 것이다.

송신자 측에서 UDP안에 있는 모든 16비트 워드의 합산에 대해 다시 1의 보수를 수행하며, 합산 과정에서 발생하는 오버플로는 윤회식 자리올림(wrap around)를 한다.

이 결과값이 UDP 세그먼트의 체크섬 필드에 삽입된다.

예시를 보면,

`1110011001100110`

`1101010101010101`

이 두 16비트를 더하면

`11011101110111011`이 나온다. 1이 자리가 넘치므로 윤회식 자리올림을 한다.

그러면 값은 `1011101110111100` 값이 나온다.

여기서 1의 보수를 수행하면 `0100010001000011` 이 체크썸이 된다.

그래서 두 16비트 + 체크섬을 더하면 모든 비트가 1이 나와야 한다.

하나라도 0이 나오면 패킷에 오류가 발생한 것이다.

왜 UDP는 체크섬을 제공 할까?

그 이유는 출발지와 목적지 사이의 모든 링크가 오류검사를 제공한다는 보장이 없기 때문이다.

즉, 중간에 하나 이상의 링크가 오류검사를 제공하지 않는 프로토콜을 사용할 수 있기 때문이다.

그래서 세그먼트들이 정확하게 링크로 전달되어도, 세그먼트가 라우터의 메모리에 저장될 때, 비트 오류가 발생할 수 있다.

하지만 UDP는 오류 회복과 관련한 어떤 일도 하지 않는다.(심지어 버리거나 그냥 애플리케이션에 넘겨주기도 한다.)

다시 돌아와서, 체크섬을 통해 종단 간의 데이터 전송 서비스가 오류 검사를 제공해야 한다면, UDP는 종단 기반으로 트랜스포트 계층에서 오류 검사를 제공해야 한다.

이것이 종단과 종단의 원칙의 한 예시이다.

종단과 종단의 원칙은 하위 레벨에 있는 기능들은 상위 레벨에서 이들을 제공하는 비용과 비교했을 때 중복되거나 거의 가치가 없을 수 있다.
