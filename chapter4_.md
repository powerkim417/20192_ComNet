# Chapter 4. Network Layer: The Data Plane

2가지 함수 중 **Forwarding** 대해 배움

## 4.1 Overview of Network layer (2~10)

- Network layer의 역할
  - 송신자에서 순신자로 세그먼트를 전달한다.
  - 송신자는 세그먼트를 데이터그램으로 캡슐화한다.
  - 수신자는 세그먼트를 transport layer에 전달한다.
  - 네트워크 계층 프로토콜은 모든 호스트와 라우터에서 적용된다.
  - 라우터는 자신을 통과하는 모든 IP 데이터그램의 헤더 필드를 확인한다.

### 4.1.1 2 Key network-layer functions

- Forwarding: 패킷을 라우터의 입력 포트로부터 적절한 출력 포트로 보냄
- Routing: 패킷의 source로부터 destination까지의 경로를 결정
  - 라우팅 알고리즘 사용

#### Data Plane

- 

#### Control Plane

### 4.1.2 Network Service Model

## 4.2 What's inside a router (11~29)

### 4.2.1 Input port functions & Destination-based forwarding

### 4.2.2 Switching

### 4.2.3-4 Output port queueing

### 4.2.5 Packet Scheduling

## 4.3 IP: Internet Protocol (30~65)

- Network Layer의 프로토콜들
  - Routing protocol
    - 경로 설정
    - RIP, OSPF, BGP
    - 포워딩 테이블 참조
  - IP(Internet) protocol
    - 주소 지정 방식
    - 데이터그램 양식
    - 패킷 처리 방식
  - ICMP(Internet Control Message Protocol) protocol
    - 오류 보고
    - 라우터 신호를 받음???

### 4.3.1 Datagram format

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191028100946481.png" alt="image-20191028100946481" style="zoom:80%;" />

### 4.3.2 Fragmentation

- 네트워크 링크는 MTU(최대 전송 크기)가 있음
  - 서로 다른 종류의 링크는 다른 MTU를 가짐
  - 이론적으로 65535바이트가 최대지만 이보다 훨씬 적게 사용함
  - Ex) Ethernet: 1500, FDDI: 4352, 802.3: 1492
- 큰 크기의 IP 데이터그램이 나눠져서 전송
  - 하나의 데이터그램이 여러개의 데이터그램으로 되는 것
  - 최종 목적지에서만 다시 합쳐짐
  - IP 헤더 비트는 fragment를 식별하고 순서를 나타내는데 사용됨

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191028102547488.png" alt="image-20191028102547488" style="zoom:80%;" />

- 4000바이트 크기의 데이터그램을 1500바이트씩 나누게 된다
- 여기서 1500바이트가 각각 1480바이트의 데이터와 20바이트의 헤더로 채워질텐데
- 각 Fragment는 8의 배수의 크기를 가져야 하므로 
- offset(데이터의 시작점?)은 데이터에 해당하는 1480바이트를 8로 나눈 값씩 증가한다???(1480/8 = 185)
  - 만약 데이터에 해당하는 크기가 8의 배수가 아니라면
    - MTU가 1000인 곳에서 980/8 = 122...4 이므로 한번에 옮겨지는 데이터는 980이 아니고 980-4=976 이다!!
- 원래 데이터그램의 총 데이터 크기가 3980 이므로 각각 1480, 1480, 1020 으로 들어가게 된다

### 4.3.3 IPv4 addressing

- IP 주소: 32비트의 호스트/라우터 인터페이스 식별자
  - 인터페이스: 호스트/라우터와 물리적 링크 사이의 연결
    - 라우터는 일반적으로 여러 개의 인터페이스를 가지고 있음
    - 호스트는 1~2개의 인터페이스를 가지고 있음(Ex. 이더넷, 무선 802.11)
  - IP주소는 이 각각의 인터페이스와 관련이 있는 것!

#### 구조

- IP 주소는 Network prefix 와 host number로 나뉨!

- 이 때 Network prefix는 주소의 class에 따라 그 길이가 달라지는데,

  - 1번째 비트가 0이면 class A
  - 1번째 비트가 1이고 2번째 비트가 0이면 class B
  - 2번째 비트까지 1이고 3번째 비트가 0이면 class C
  - 3번째 비트까지 1이고 4번째 비트가 0이면 class D
  - 4번째 비트까지 1이면 class E

  ![image-20191028121059383](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191028121059383.png)

  문제점: class A, B의 주소는 너무 많이 할당되어서 쓸데없이 많이 남고, class C의 주소는 부족!

  - Network prefix의 길이는 일반적으로 class에 따라 아래와 같이 정해진다.

  ![image-20191028121135715](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191028121135715.png)

  - Ex) 128.143.137.144 IP주소만으로는 데이터그램을 라우팅/포워딩 할 수 없다.
    - network prefix를 명시해줘야 함! -> 128.143.137.144/16
      - 뒤의 숫자(16)는 마스크에서 1의 갯수를 의미
        - <u>1111 1111 1111 1111</u> 0000 ... = 255.255.0.0  =  ffff0000
      - Network prefix (= ID = address): IP주소와 netmask를 AND연산 -> 128.143.0.0
      - Host number: IP주소와 inverse netmask(=0000ffff)를 AND연산 -> 0.0.137.144

#### Subnet

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191028104934665.png" alt="image-20191028104934665" style="zoom:80%;" />

링크 하나하나가 IP주소(인터페이스)와 연관이 있고, 파란 범위가 각 서브넷을 의미함!

- IP 주소
  
  - 서브넷 부분(상위 순서 비트)-호스트 부분(하위 순서 비트) 로 구성
  
- 서브넷이란?
  - IP주소의 서브넷 부분이 동일한 디바이스 인터페이스들
  - 라우터를 방해하지 않고 물리적으로 서로 닿을 수 있음
  
- 서브넷을 사용하는 이유
  - 조직은 여러 부서를 가질 수 있는데, 각각의 부서만의 네트워크를 관리하기 위해!
    - 각 네트워크에 대해 network prefix를 할당해야 하나?
    - 하지만 조직에는 1개의 network prefix만 주어짐
    - 해결책: 조직 내에서만 볼 수 있는 IP주소 구조에 계층 도입!(Subnetting)
  
- 서브넷의 기본 아이디어

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191104102941423.png" alt="image-20191104102941423" style="zoom:80%;" />

  - **network prefix**는 전역으로 할당되어 있으므로 건드릴 수 없다
  - 대신, **host number**는 지역적으로 할당되어 있음
  - 이 host number를 subnet number와 (smaller) host number로 나누어 사용하고, 기존의 network prefix와 subnet number를 합쳐서 **extended network prefix (= subnet prefix)**로 사용한다!

- 서브넷의 특징

  - 서브넷은 단체 내에서 자유롭게 할당 가능
  - 내부적으로 서브넷은 별도의 네트워크로 취급
  - 단체의 외부에서는 서브넷 구조를 알 수 없음

- 서브넷의 예시

  - 128.143.137.144 의 IP주소가 있고, 3번째 바이트가 subnet number일 때 
    - network address는128.143.0.0/16
      - network prefix는 /16
    - 서브넷의 subnet prefix는 128.143.<u>137</u>.0/24
    - 호스트의 IP주소는 128.143.137.144/32
    - 서브넷의 subnet mask는 255.255.255.0
      - prefix는 /24

- 서브넷의 장점

  - 서브넷을 이용하면 IP주소는 3계층 구조(network, subnet, host)를 가지게 된다.
  - 각 물리 네트워크의 주소공간 전체를 다 사용하는 것이 아니기 때문에 IP 주소의 효용성을 높일 수 있다.
  - 라우터의 복잡도를 줄여준다.
    - 외부 라우터는 서브넷에 대해 알 수 없기 때문에 외부 라우터에서의 라우팅 테이블 복잡도가 줄어든다

- 참고: 캠퍼스 네트워크에서 모든 서브넷 마스크의 길이가 동일할 필요는 없음!

- Subnetting의 예시: 125.36.0.0/16 의 캠퍼스 network prefix가 주어졌을 때

  - 각각 <u>최대</u> 500개의 host를 수용할 수 있는 서브넷들을 만들어야 할 때
    - 125.36은 이미 할당되었고, 0.0(16비트) 을 subnet과 host bit로 나눠야 한다
    - 500개의 host를 구분하기 위한 host addressing에 x 비트가 필요하다고 할 때
      - 2^x - 2 >= 500 이어야 하므로(첫 주소 네트워크주소, 마지막 주소 브로드캐스트주소 제외)??? x의 최소값은 9
    - 그럼 서브넷팅에 사용할 수 있는 비트 수는 16 - 9 = 7. 총 2^7 = 128개의 서브넷이 생성됨
      - 서브넷 주소: {0, 1, ..., 127} (127의 경우 서브넷 + 호스트가 1111 111h hhhh hhhh 의 형태로 들어가는듯)

  - 각각 <u>최소</u> 250개의 host를 수용하는 5개의 서브넷을 만들어야 할 때

    - 125.36은 이미 할당되었고, 0.0(16비트) 을 subnet과 host bit로 나눠야 한다
    - 5개의 서브넷만 있으면 되므로 3개의 비트를 서브넷에 사용
      - 서브넷 주소: {0, 1, 2, 3, 4, 5, 6, 7}
    - 그러면 host address 공간은 13비트가 된다.
      - 2^13 - 2 >= 250 이므로 가능!
    - 125.36.0.0 campus network에서 가능한 subnet은
      - 125.36.<u>sss</u>hhhhh.hhhhhhhh 에서 sss가 {0, 1, 2, 3, 4, 5, 6, 7}인 경우 총 8가지!
    - Ex)
      - 125.36.<u>001</u>00000.00000000 → 125.36.32.0/**19**
      - 125.36.<u>010</u>00000.00000000 → 125.36.64.0/**19**
      - 125.36.<u>011</u>00000.00000000 → 125.36.96.0/**19**
      - 125.36.<u>100</u>00000.00000000 → 125.36.128.0/**19**
      - 125.36.<u>101</u>00000.00000000 → 125.36.160.0/**19**
      - 000 부터 시작해도 됨. 이건 그냥 8가지 중 5가지 경우

  - 심화 예제: prefix가 18개로 주어지고, 서브넷 주소 길이가 4로 정해진 2개의 서브넷이 각각 2개씩 또 서브넷으로 나눠지고, 최대 수용 호스트 수가 정해져있을 때

    ![image-20191106134913160](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191106134913160.png)

    - 이 경우 subnet prefix가 22비트이고, 호스트 주소는 9비트 이므로 가운데에 1개의 비트가 남는데, 이 1개의 비트로 2개의 서브넷을 또 나눌 수 있다!
    - 이 경우 subnet prefix는 22 + 1 = 23비트가 된다.

  - 마지막 예제: 4개의 부서를 가진 단체가 10.2.22.0/23 의 IP 주소공간을 가지고 있고, 각 부서가 각각 200개, 71개, 55대, 41개의 host를 수용할 수 있어야 한다면 각 subnet network number는?

    - 200 <= 256-2 이므로 host address 길이는 8비트가 되고, 서브넷 길이는 8비트가 된다.
      - 10.2.22.0/24
    - 71 <= 128-2 이므로 host address 길이는 7비트가 되고, 서브넷 길이는 9비트가 된다.
      - 10.2.23.0/25
    - 55 <= 64-2 이므로 host address 길이는 6비트가 되고, 서브넷 길이는 10비트가 된다.
      - 10.2.23.<u>128</u>/26
    - 41 <= 64-2 이므로 host address 길이는 6비트가 되고, 서브넷 길이는 10비트가 된다.
      - 10.2.23.<u>192</u>/26

- 서브넷과 네트워크

  - 서브넷이 없는 네트워크

    ![image-20191106135727163](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191106135727163.png)

  - 서브넷이 있는 네트워크

    ![image-20191106135747644](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191106135747644.png)

  - 서브넷 마스크가 여러개 존재하는 네트워크

    ![image-20191106135821842](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191106135821842.png)

    - 128.143.137.0/24의 서브넷을 128.143.137.0/26, 128.143.137.128/26 의 두 서브넷으로 더 나누어서 적용

#### Classless InterDomain Routing(CIDR)

- 주소에서 서브넷 부분이 임의의 길이를 가지게 됨
- a.d.c.d/x 의 형태로 주소를 가지며, x는 주소에서 서브넷 부분의 비트의 갯수

##### Hierarchical Addressing(계층적 주소)

- 경로 집약: 여러 네트워크를 알리기 위해 하나의 network prefix를 사용하는 것

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191112194434619.png" alt="image-20191112194434619" style="zoom:80%;" />

  - 그림에서는 <u>200.23."0001xxx</u>0".0/23의 네트워크 8개를 알리기 위해 network prefix 200.23."00010000".0/20을 사용함
  - 그림의 Fly-By-Night-ISP와 ISPs-R-Us는 각각의 회사의 ISP를 의미

- 다른 경우

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191112194504935.png" alt="image-20191112194504935" style="zoom:80%;" />

  - 그림과 같이 Fly-By-Night-ISP가 ISPs-R-Us를 인수하고, Organization 1을 자회사인 ISP-R-Us의 네트워크에 연결했을 때
  - Organization 1의 모든 라우터와 호스트의 IP 주소를 기존 ISPs-R-Us의 주소 블록 내의 주소로 변경해도 되지만, 이는 비용도 많이 들 뿐만 아니라 후에 다른 ISP로 또 이동하게 되면 또 다시 바꿔야 한다는 점에서 비효율적이다.
  - 해결책) ISPs-R-Us에서 기존의 199.31.0.0/16 뿐만 아니라 Organization 1의 주소인 200.23.18.0/23도 광고하도록 추가한다.
  - 그러면 200.23.18.0/23 블록 안의 주소로 라우팅할 때 "Longest prefix matching"에 의해 20비트가 일치하는 Fly-By-Night-ISP 대신 23비트가 일치하는 ISPs-R-Us로 라우팅할 수 있게 됨!

#### How to get IP address?

- **<u>네트워크는 어떻게 IP주소의 서브넷 부분을 얻을 수 있을까?</u>**

  - 네트워크의 공급자 ISP의 주소 공간에서 할당된 <u>부분</u>을 받아옴

  - Ex) 소비자에게 네트워크를 제공하기 위해 3비트가 할당된 경우

    ![image-20191111111207320](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191111111207320.png)

- **<u>그러면 ISP는 어떻게 자신의 주소 블록을 얻을 수 있을까?</u>**

  - ICANN: Internet Corporation for Assigned Names and Numbers
    - 주소 할당
    - DNS 관리
    - 도메인 이름을 할당하여 분쟁을 해결

- <u>**Host는 어떻게 IP 주소를 얻을 수 있을까?**</u>
  - 시스템 관리자가 파일에 직접 하드코딩...
  - Dynamic Host Configuration Protocol(DHCP)!!
    - 서버에서 동적으로 주소를 가져옴(plug-and-play)

##### DHCP의 목표

- 호스트가 네트워크에 접속할 때 네트워크 서버로부터 자신의 IP주소를 동적으로 받을 수 있게 하는 것
  - 자신의 주소의 lease 기간을 갱신할 수 있다
  - 주소 재사용이 가능하다(접속되어있을 때만 주소를 소유하므로)
  - 모바일 유저도 지원

##### DHCP 개요

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191111102809776.png" alt="image-20191111102809776" style="zoom:80%;" />

- (optional) 호스트는 "**DHCP discover**" 메시지를 브로드캐스트한다.
  - DHCP 서버를 찾을 때
- (optional) DHCP 서버는 "**DHCP offer**" 메시지로 응답한다.
  - DHCP 서버를 찾는 호스트의 메시지에 대답함
- 호스트는 "**DHCP request**" 메시지로 IP주소를 요청한다.
- DHCP 서버는 "**DHCP ack**" 메시지로 주소를 보낸다.

##### DHCP의 의의

- DHCP는 서브넷에서의 IP주소 뿐만 아니라 다음의 값들도 반환한다!
  - 클라이언트로 가는 첫번째 라우터(first-hop router)의 주소
  - DNS 서버의 이름과 IP 주소
  - 네트워크 마스크(주소의 network/host 부분을 나타냄)

##### 예시

- 클라이언트 → DHCP 서버

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191111104213810.png" alt="image-20191111104213810" style="zoom:80%;" />

  - 컴퓨터를 연결하기 위해서는 IP주소, first-hop router, DNS 서버의 주소가 필요 → DHCP 이용!
  - DHCP request → UDP → IP → 802.1 이더넷으로 캡슐화됨
  -  LAN에서 브로드캐스트되는 이더넷 프레임(dest: FFFFFFFFFFFF???)이 DHCP 서버가 돌아가는 라우터에서 수신됨
  - 이더넷 → IP → UDP → DHCP로 demux됨

- DHCP 서버 → 클라이언트

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191111104104302.png" alt="image-20191111104104302" style="zoom:80%;" />
  - DHCP 서버는 클라이언트의 IP주소, first-hop router의 IP주소, DNS서버의 이름 및 IP주소를 담은 DHCP ACK를 만듦
  - 클라이언트에게 포워딩된 DHCP 서버에서 캡슐화한 프레임은 DHCP 서버에서 클라이언트로 demux됨
  - 클라이언트는 이제 자신의 IP주소, DNS서버의 이름 및 IP주소, 자신의 first-hop router의 IP 주소를 알 수 있음

#### IP 주소의 문제점

1990년에 2가지 문제 대두

- IP address Exhaustion (IP 주소 고갈)
  - Class A, B, C의 주소 구조가 비효율적!
    - Class B의 경우 대부분 단체에서 쓰기 충분하나, 미래를 대비해야 함!
    - Class C는 너무 작음(256?)
    - 추세 상 Class B의 할당이 1994년에 고갈될 것으로 예측
- IP routing table size (IP 라우팅 테이블 크기의 계속적인 증가)
  - 테이블 엔트리 수에 반영되는 인터넷의 네트워크 수가 증가
    - 1991~1995년 사이 10개월동안 라우팅 테이블의 크기가 2배씩 증가함
    - 이는 라우터 처리능력과 메모리 할당에 부담을 가함
- 해결책
  - 단기적 해결책: CIDR, NAT
  - 장기적 해결책: IPv6

### 4.3.4 Network Address Translation(NAT)



### 4.3.5 IPv6

보충자료

## 4.4 Generalized Forward and SDN (66~74)

보충자료

### 4.4.1 Match

### 4.4.2 Action

### 4.4.3 match-plus-action 작업의 OpenFlow 예시