## 자율시스템 (AS)
- 동일한 관리자에 의해,
  - 여러 내부 라우팅 프로토콜(IGP)을 동일한 정책으로 관리하는,
  - 인터넷 상의 라우터들과 서브 네트워크(sub-network)들의 집합
- 통상, 학교,기업 사내전산망, 크게 보면 ISP 등을 말함
  - 하나의 관리 통제하에 있는 개념적인 영역
    - 보통, 영역(Domain)과 같은 의미로 사용되곤 함

## AS에 의해 네트워크를 분리(분담)하는 이유
- 라우팅 정책의 독립성
- 보안 유지
- 고장 및 오류의 국지화
- 라우팅 트래픽량의 최소화 등을 위함

## 라우팅 프로토콜의 구분
- IGP (Interior Gateway Protocol) : (*Intra-AS*, 자율시스템 내) 
  - 자율시스템 내의 라우터끼리의 라우팅
    - e.g. RIP, OSPF, IS-IS 등
- EGP (Exterior Gateway Protocol) : (*Inter-AS*, 자율시스템 간) 
  - 자율시스템 밖의 외곽 라우터끼리의 라우팅
    - e.g. BGP, EGP, IDRP 등

***Intra-AS 라우팅 프로토콜***
- AS 내부에 적용가능한 라우팅 프로토콜(Interior Gateway Protocol)
- RIP(Routing Information Protocol), OSPF(Open Shortest Path First), ...

***Inter-AS 라우팅 프로토콜***
- AS 간에 적용 가능한 라우팅 프로토콜(Exterior Gateway Protocol)
- BGP(Border Gateway Protocol)

## AS 번호 (ASN) 
- AS를 구분하는 유일한 ID
- 번호부여범위 : 1 ~ 65535
  - 16 비트 길이의 번호로써, 약 65,000개(2의 16승) 정도의 번호 부여가 가능
  - e.g. KT 코넷의 경우 AS 번호는 4766 등이 할당
- Private Use (사설 사용) 가능 : 64512 ~ 65534
  - 이 사설용은 *BGP Confederation*의 적용 등에서 많이 사용됨

![AS(Autonomous_System)](./images/AS(Autonomous_System).jpeg)

***BGP Confederation***
- 하나의 AS가 규모가 큰 라우터 집단일 때, 여러 개의 Sub AS로 나누는 것이 관리차원이나 정보교환량 면에서 유리할 때가 많음
- 이때 라우터들을 여러 개의 그룹(Sub AS)으로 묶고
- 외부 AS에서 볼 때는 마치 하나의 AS로 보이게 하여 결국 다루기쉽게 묶어주는 것을 Confederation 이라고 한다
- 이때의 AS 번호는 Private Use (사설용, 64512~65530)를 적용한다.

## 참고
- [AS   Autonomous System   자율시스템, 자율시스템 - 정보통신기술용어해설](http://www.ktword.co.kr/test/view/view.php?nav=2&no=366&sh=AS)
- [BGP Confederation, AS Confederation - 정보통신기술용어해설](http://www.ktword.co.kr/test/view/view.php?no=2476)
- [컴퓨터네트워크 제28강 라우팅프로토콜 BGP - 한국기술교육대학교박승철교수](https://www.youtube.com/watch?v=0h7hKi_j1GE&list=LL&index=1)