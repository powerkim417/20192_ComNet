# Chapter 4. Network Layer: The Data Plane

2가지 함수 중 **Forwarding** 대해 배움

## 4.1 Overview of Network layer (2~10)

- Network layer의 역할
  - 송신자에서 수신자로 세그먼트를 전달한다.
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

#### Processing of IP datagram at a router

0. IP 데이터그램 수신
1. IP 헤더 유효성 검사(들어온 데이터그램에 대한 체크섬?)
2. IP 헤더의 옵션 처리
3. 목적지 IP 주소 파싱
4. 라우팅 테이블 확인
5. TTL 감소
6. (필요한 경우) Fragmentation 실시
7. Checksum 계산(Fragmentation에 대한 유효성?)
8. 다음 hop으로 전송
9. (필요한 경우) ICMP 패킷 전송

### 4.3.2 Fragmentation

- 네트워크 링크는 MTU(최대 전송 크기)가 있음
  - 서로 다른 종류의 링크는 다른 MTU를 가짐
  - 이론적으로 65535바이트가 최대지만 이보다 훨씬 적게 사용함
  - Ex) Ethernet: 1500, FDDI: 4352, 802.3: 1492
- 큰 크기의 IP 데이터그램이 나눠져서 전송
  - 하나의 데이터그램이 여러개의 fragment로 되는 것
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

- 네트워크 주소 변환

- SOHO(Small office, home office)가 확산됨에 따라 ISP가 SOHO 네트워크의 주소범위에 인접한 부분을 이미 할당해버렸다는 문제가 발생할 수 있음

- 이를 해결하기 위해 네트워크 주소들이 그 네트워크 내부 장비에서만 의미가 있는 네트워크를 만듦! = private network

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113002153356.png" alt="image-20191113002153356" style="zoom:80%;" />
  - 그림에서 10.0.0/24의 주소들은 local network 내부에서만 의미를 가진다.
    - local network 내에서의 데이터그램들의 송/수신지는 모두 10.0.0/24 블럭의 주소를 가짐
  - 해당 local network로부터 나온 모든 데이터그램은 모두 동일한 NAT IP주소 138.76.29.7에 서로 다른 포트 번호를 가진다.

- 외부-내부 간 주소 변환은 NAT translation table 이용

  - NAT 라우터 내부에 존재
  - 데이터그램 도착 시 변환

#### NAT의 의의

- local network는 외부와 연관된 하나의 ip 주소만을 사용함. 즉, 외부에서 보았을 때는 local network 전체가 하나의 장비처럼 동작함
  - ISP를 통해 여러 개의 주소를 할당받을 필요 없음: 모든 장치에 대해서 1개의 주소만!
  - 외부에 알릴 필요 없이 local network에서 장치의 주소를 변경할 수 있음
  - local network에서 장치의 주소를 변경하지 않고 ISP를 변경할 수 있음
  - 외부에서 내부의 장치에 대해 명시적으로 주소를 지정할 수도, 볼 수도 없음(보안 강화) 

#### 구현

- NAT 라우터가 갖춰야 할 것들
  - 모든 나가는 데이터그램의 {송신지 IP주소, 포트 번호}를 {NAT IP주소, 새 포트 번호}로 치환해야 한다.
    - 송신지 IP 주소가 local이고, NAT IP주소가 외부 기준
    - 외부의 클라이언트/서버는 해당 데이터그램에 대한 응답으로 {NAT IP주소, 새 포트 번호}를 수신지 주소로 사용할 것임
  - NAT translation table에서 모든 {송신지 IP주소, 포트 번호} - {NAT IP주소, 새 포트 번호} 변환 쌍을 기억할 수 있어야 한다.
    - 변환되는 주소 정보는 데이터그램이 처음 나갈 때 저장됨
  - 모든 들어오는 데이터그램의 도착지 정보에서 {NAT IP주소, 새 포트 번호}를 NAT table에 저장된 해당하는 {송신지 IP주소, 포트 번호}로 치환해야 한다.

#### 송/수신 과정

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113005515095.png" alt="image-20191113005515095" style="zoom:80%;" />

1. 호스트 10.0.0.1이 3345 포트에서 데이터그램을 128.119.40.186, 80 에 보낸다.
2. NAT 라우터가 데이터그램의 출발지 주소를 10.0.0.1, 3345에서 138.76.29.7, 5001로 변환하고, **이 정보를 테이블에 저장한다.**
3. 답장 데이터그램이 NAT 라우터에 도착하였고, 수신지 주소는 138.76.29.7, 5001
4. NAT 라우터는 답장 데이터그램의 도착지 주소를 테이블을 참조하여 138.76.29.7, 5001 에서 10.0.0.1, 3345로 변환한다.

#### 특징

- 포트 번호의 필드가 16비트로 길기 때문에 WAN 쪽의 하나의 IP 주소에 대해 약 60000개의 동시 접속을 지원한다!
- 논쟁거리
  - 라우터는 계층 3(Network layer)까지만 처리해야 함
  - 주소 부족 문제는 IPv6으로 해결할 수 있음
  - end-to-end argument(호스트가 IP 주소와 포트번호를 수정하는 일 없이 직접 통신해야 함)를 위반
    - 애플리케이션 설계자가 해당 주소가 NAT 주소일 가능성까지 고려해야 함..(Ex. P2P 어플리케이션)
  - 해결책: NAT traversal
    - 클라이언트가 NAT을 거치지 않고 가로질러서 서버와 통신하는 방식

#### Relaying

- 공용 인터넷의 호스트는 종종 개인 네트워크의 호스트에 통신을 시도하는데 실패할 수 있다.
- 두 다른 개인 네트워크 소속의 호스트끼리의 통신에서도 같은 문제가 발생할 수 있음
- 해결책: Relaying(Skype에서 사용)
  - NAT 기술이 적용된 클라이언트가 Relay에 연결을 생성
  - 외부 클라이언트가 Relay에 연결
  - Relay가 두 연결 사이에서 패킷의 다리 역할을 함

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113015912631.png" alt="image-20191113015912631" style="zoom:80%;" />

### 4.3.5 IPv6

#### 등장 계기

- 32비트의 주소 공간이 곧 전부 할당되어 주소 부족 문제가 발생할 수 있음
- 헤더의 포맷이 처리 및 포워딩의 속도 향상에 영향을 미치므로 서비스의 퀄리티를 높이기 위해 새로운(간결한) 양식 고안???

#### 특징

- 더 긴 주소 필드
  - 128비트는 최대 3.4*10^38개의 호스트를 지원할 수 있음
- 간결해진 헤더 포맷
  - 포맷을 간결하게 하여 각 헤더에 대한 처리를 빠르게 한다.
  - 각 필드는 모두 고정 크기
  - IPv4와 IPv6 필드를 비교했을 때
    - 동일: version
    - IPv6에서 사라진 필드: header length, ID/flags/frag offset, header checksum
    - IPv6에서 대체된 필드
      - Datagram length → Payload length
      - Protocol type → Payload length
      - TTL(Time To Live) → Hop limit
      - TOS(Type Of Service) → Traffic class(priority)
    - IPv6에서 추가된 필드: Flow label

#### IPv6 주소 지정

주소 종류에 따라 분류

- Unicast: 단일 네트워크 인터페이스
- Multicast: 패킷이 서로 다른 위치에 있는 여러 네트워크 인터페이스에 모두 보내지는 경우
- Anycast: 패킷이 서로 다른 위치에 있는 여러 네트워크 인터페이스 중 한 곳에만 보내지는 경우
  - 가장 가까운 곳, ...

#### IPv6의 기능

- 옵션에 대한 유연한 지원
  - 선택적인 확장 헤더를 통해 더 효율적이고 유연한 옵션 사용 가능
- Flow label 기능
  - "Flow label"로 특정 QoS를 필요로 하는 패킷의 흐름을 식별
- 보안
  - 인증 및 기밀성이 내장되어 있음 
- 큰 패킷 지원
  - 64KB 이상의 "Jumbo payload" 에 대해서도 지원
- Fragmentation은 출발지에서만 가능
  - 출발지는 경로에서의 최소 MTU를 미리 확인해야 함
- Checksum 필드가 없음
  - 라우터에서의 패킷 처리 시간을 줄이기 위해 제거

#### IPv6 Datagram Format

- 40바이트 고정 길이 헤더
- Fragmentation은 허용되지 않음
  - fragmentation 및 재결합 작업은 시간이 많이 걸리므로 라우터가 아닌 출발지/도착지에서만 일어남
  - 라우터에서 데이터그램이 너무 커서 출력 링크로 전달될 수 없는 경우 데이터그램을 폐기하고 패킷이 너무 크다는 ICMP 오류 메시지를 송신자에게 보낸다. 그 후 송신자는 IP 데이터그램 크기를 줄여서 다시 보낸다.

![image-20191113020355426](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113020355426.png)

- Version field: IP 버전 번호 인식. IPv6의 경우 "6"
- Priority(=Traffic class): 데이터그램 간 우선순위 부여
- Flow label: 같은 "Flow"의 데이터그램을 식별(Flow의 정확한 의미는 아직 정의되지 않음..)
  - Flow마다 다른 처리를 해야 하는 경우 활용?
- Payload length: 헤더를 제외한 데이터의 길이. 최대 65536B
- Next header: 데이터의 상위 계층 프로토콜을 식별(Ex. TCP, UDP)
- Hop limit: 패킷이 라우터로부터 폐기될 때까지 이동할 수 있는 hop의 수
- ~~Checksum: 각 hop에서의 처리 시간을 줄이기 위해 데이터그램 포맷에서 삭제됨~~
- Options: 표준 IPv6 헤더 필드에서는 제외되었으나, 헤더 밖에서 사용될 수 있음.
  - 이 필드가 제외됨에 따라 헤더가 40바이트 고정 길이를 가질 수 있게 됨
- ICMPv6: ICMP의 새로운 버전
  - "Packet Too Big"와 같은 추가적인 메시지 종류 지원
  - 멀티캐스트 그룹 관리 기능

#### IPv4에서 IPv6로 전환

- 모든 라우터가 동시에 업그레이드될 수는 없음...

  - "flag day"와 같이 모든 장비가 업그레이드되는 날짜를 각각 정하는 방법은 안됨
  - 결국 IPv4 라우터와 IPv6 라우터가 혼용되어야 함!

- 해결책: Tunneling!

  - IPv6 데이터그램이 IPv4 데이터그램의 payload로 운반되어 IPv4 라우터를 지나가는 방식

    <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113024912193.png" alt="image-20191113024912193" style="zoom:80%;" />

#### Tunneling

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113024949090.png" alt="image-20191113024949090" style="zoom:80%;" />

- 터널의 송신(그림에서 라우터 B)에 도착하면, IPv6 데이터그램을 받고, IPv4 데이터그램의 데이터 필드에 이를 넣는다.
- 목적지 주소는 터널의 수신 측의 IPv6 노드(그림에서 라우터 E)로 적어서 터널의 첫번째 노드(그림에서 라우터 C)로 보낸다.
- 터널 내부의 라우터들은 IPv4 데이터그램이 IPv6 데이터그램을 가지고 있다는 것을 모른 채 처리
- 터널 수신 측 IPv6 노드(그림에서 라우터 E)는 IPv4 데이터그램을 받고 이 데이터그램이 IPv6 데이터그램인 것을 확인(IPv4 데이터그램 프로토콜 번호 필드가 41)하고, 이를 IPv6 데이터그램으로 만들어서 다음 IPv6 노드에 보낸다.

#### IPv6 채택 현황

- Google: 전체 클라이언트의 8%만이 IPv6으로 접속, 그러나 점점 늘어나고 있음
- NIST: 미국 정부 도메인의 1/3의 IPv6 지원
- 구축 및 사용까지 많은 시간이 걸릴 것

#### IPv6 관련 추가 자료

##### 주소 획득 방법

- 1993년부터는 DHCP 활용
  - IPv4 주소 및 기본 라우터/DNS 관련 정보
- IPv6 출현 이후
  - DHCPv6
  - Stateless autoconfiguration protocol! (IPv4에서는 없었던 개념)

##### IPv6 주소 범위

Global과 link-local의 2가지 type 존재

- Global type
  - 공용 IPv4 주소와 유사
  - 주소 지정은 ICANN에 의해 제어되며, 이러한 주소가 한번 할당되면 다른 인터넷에 연결된 글로벌 IPv6 호스트에 의해 사용될 수 있다.
- Link local
  - 개인 IPv4 주소와 유사
  - 같은 네트워크 세그먼트의 호스트끼리 통신하는데만 사용할 수 있다. 라우터는 link local 주소를 포워드하면 안됨

##### Auto-configuration(자동 구성)???

- 128비트 길이의 IPv6 주소를 수동으로 구성할 필요 없음
- Link-local address
  - FE80::/10의 link local prefix 및 64비트 인터페이스 식별자(48비트 MAC 주소 기반)를 사용하여 자동으로 생성 가능
- Global address
  - link-local 자동 구성에 의해 식별자 생성
  - 이렇게 생성된 global prefix 뒤에 붙음
    - 일반적으로, global prefix는 회사 또는 end user에게 분배된다.
- MAC 주소를 사용했을 때의 보안 문제
  - 웹사이트에서 노드가 자주 연결되는 것을 트래킹하여 개인정보를 수집할 수 있음
  - 해결책: 제한된 시간동안 작용하는 random identifier(RFC 3041)을 생성하여 사용

##### Anycast address

- 하나의 주소가 여러 인터페이스에 할당되었고, 전달은 이 중 하나의 인터페이스에만 이루어지는 경우
  - anycast address를 가진 여러 노드와 연결된 라우터
- 어느 인터페이스를 고르는지는 라우터의 구현에 따라 다름! (Ex. round-robin algorithm)
- CDN에 의해 사용됨
  - Anycast는 일반적으로 request를 효율적으로 처리할 수 있는 용량으로 들어오는 트래픽을 제일 가까운 데이터 센터로 라우트한다.
  - 높은 트래픽 부피와 네트워크 혼잡 문제를 해결하기 위해 선택적 라우팅 사용

## 4.4 Generalized Forward and SDN (66~74)

- 포워딩의 종류는 2가지가 있음
  - Destination-based forwarding: Forwarding table 이용
  - Generalized forwarding: Flow table 이용

- 각 라우터는 logically centralized routing controller에 의해 계산되고 분배되는 flow table을 가지고 있음

![image-20191113143429837](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113143429837.png)

- Flow: 헤더 필드에 의해 정의됨
- 일반화된 포워딩: 기본적인 패킷 처리 규칙
  - Pattern: 패킷 헤더 필드에 있는 값 매칭
  - Actions: 매치된 패킷에 대해 drop, forward, modify하거나 매치된 패킷을 컨트롤러로 전송
  - Priority: 중복 패턴 문제를 해결
  - Counter: 바이트 수와 패킷의 수
  - 라우터의 flow table이 라우터의 match, action 규칙을 정한다.

### OpenFlow 예시

##### Data plane abstraction

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113144902513.png" alt="image-20191113144902513" style="zoom:80%;" />

##### Flow Table Entries

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113145037461.png" alt="image-20191113145037461" style="zoom:80%;" />

- Rule
  - Switch port
  - Link layer field: VLAN ID, MAC 출발지 주소, MAC 도착지 주소, 이더넷 타입
  - Network layer field: IP 출발지 주소, IP 도착지 주소, IP 프로토콜 종류
  - Transport layer field: TCP 출발지 포트, TCP 도착지 포트
- Action 5가지
  - 패킷을 포트로 포워딩
  - 캡슐화한 뒤 컨트롤러로 포워딩
  - 패킷 드롭(= block = 포워딩하지 않음)
  - 일반 처리 파이프라인으로 보냄
  - 필드 수정
- Stats
  - 패킷 및 바이트 카운터

###### Flow Table Entry Examples

- Destination-based forwarding

  ![image-20191113150535145](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113150535145.png)

  - IP 데이터그램의 목적지 IP 주소가 51.6.0.8으로 정해졌고, 라우터의 6번 출력 포트로 포워딩되어야 한다.

- Destination-based layer 2(switch) forwarding

  ![image-20191113151015866](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113151015866.png)

  - MAC 주소 22:A7:23:11:E1:02 인 layer 2(링크 계층) 프레임은 3번 출력 포트로 포워딩되어야 한다.

- Firewall

  ![image-20191113150726694](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113150726694.png)

  - TCP 22번 포트가 목적지인 모든 데이터그램을 포워딩하지 않는다(block).

  ![image-20191113150848755](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113150848755.png)

  - Host의 IP주소가 128.119.1.1인 모든 데이터그램을 포워딩하지 않는다(block).

##### OpenFlow abstraction

Match + Action: 서로 다른 종류의 디바이스를 통일하여 처리

|          |            Match            |                  Action                   |
| :------: | :-------------------------: | :---------------------------------------: |
|  Router  |  가장 긴 도착지 IP prefix   |            링크를 통해 포워딩             |
|  Switch  |      도착지의 MAC 주소      |            포워딩 또는 플러딩             |
| Firewall | IP 주소와 TCP/UDP 포트 번호 |              허용 또는 거부               |
|   NAT    |       IP 주소와 포트        | 주소와 포트를 재작성(해당하는 NAT 주소로) |

##### OpenFlow example

호스트 h5, h6에서 나온 데이터그램이 s1과 s2를 거쳐 각각 h3, h4로 도착해야 하는 경우

(h5 → h3, h6 → h4)

![image-20191113152239201](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113152239201.png)

- s3
  - Match: IP src가 10.3.\*.\*이고(h5, h6), IP dst가 10.2.\*.\*이고(h3, h4)인 경우 / Action: s3의 3번 포트로 포워딩
  - s3의 3번 포트로 포워딩
- s1
  - Match: 진입 포트가 1번 포트이고, IP src가 10.3.\*.\*이고(h5, h6), IP dst가 10.2.\*.\*이고(h3, h4)인 경우 / Action: s1의 4번 포트로 포워딩
  - 다른 포트에서 오는 데이터그램은 모두 막힘!
- s2
  - Match: 진입 포트가 2번 포트이고, IP dst가 10.2.0.3(h3)인 경우 / Action: s2의 3번 포트로 포워딩
  - Match: 진입 포트가 2번 포트이고, IP dst가 10.2.0.4(h4)인 경우 / Action: s2의 4번 포트로 포워딩

### Software Defined Networking(SDN)

#### 등장 계기

- 트래픽 패턴의 변화
- Data center networks
- 초거대 크기의 네트워크
  - 수천 개의 서버
  - 수천 개의 스위치(테라 비트 네트워크 용량)
  - 3-4티어 아키텍쳐 → 50% 이상의 네트워크 용량이 스위치 연결에 사용됨 → 비효율적!
- 이에 따른 새로운 네트워킹 패러다임의 필요성 증대
- 벤더 종속성
  - 열린 인터페이스와 표준 API의 부족 → 운영자들이 네트워크를 조정할 수 없게 됨
  - 회사의 요구와 유저의 요구가 시장에 반영되기까지 오랜 시간이 걸림???
- IP 프로토콜의 기초적인 문제
  - 프로토콜이 독립적으로 정의되어 각각 특정 문제를 해결하지만 추상화의 이점을 받지 못함
    - 6776개의 RFC
  - 현재의 인터넷은 많은 새로운 미들박스 필요
    - IPv4 주소 부족 → NAT, IPv6
    - 보안 → IDS/IPS, VLAN, VPN
    - 관리 → 인증, Qos, ACL, ...
  - 오늘날의 인터넷
    - 디바이스의 추가/제거 시 여러 디바이스와 그 구성을 건드려야 하는데, 인간이 하는 일이다보니 오류가 생길 수 있음

#### SDN

- 네트워킹의 새로운 접근
  - IP, TCP와 같은 구조 측면이 아님
  - 라우팅, TE와 같은 네트워크 제어 체계를 새로 설계하는 것!

##### 전통적인 인터넷 → SDN

- 전통적인 인터넷에서 라우터는 3개의 plane으로 나뉨: Management(구성) / Control / Data
  - Control plane: 분배 알고리즘
    - 위상 변화 트래킹, 경로 계산, 포워딩 규칙 정립
  - Management plane: human time scale
    - 측정 결과 수집 후 장비 구성
- SDN에서는 이러한 작업을 "Logically-centralized control" 에서 수행한다!
  - 이 LCC는 똑똑하나 느리고, 스위치는 똑똑하지 않으나 빠르다.
  - LCC에서 switch로 연결할 때는 data plane으로 연결되는 API를 사용(Ex. OpenFlow)

##### Centralized/Distributed Control

Onix: 큰 크기의 production network를 위한 분배 제어 플랫폼

- Centralized control

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113163315861.png" alt="image-20191113163315861" style="zoom:67%;" />

- Distributed control

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113163328197.png" alt="image-20191113163328197" style="zoom:67%;" />

##### Control Plane에서의 SDN 개략

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113163424505.png" alt="image-20191113163424505" style="zoom:80%;" />

- NOS(Network Operating System)

  - 일관적이고 중앙화된 프로그램화된 인터페이스를 모든 네트워크에 제공

    - 네트워크 자체를 관리하는 것은 아니며, 인터페이스를 제공하는 것 뿐

    <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191113163539555.png" alt="image-20191113163539555" style="zoom:80%;" />



이 이후는 시험에 안나올듯..