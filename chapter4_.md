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
- offset은 데이터에 해당하는 1480바이트를 8로 나눈 값씩 증가한다???(1480/8 = 185)
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

#### Dynamic Host Configuration Protocol(DHCP)

- Host가 IP 주소를 얻는 방법

### 4.3.4 Network Address Translation(NAT)

### 4.3.5 IPv6

보충자료

## 4.4 Generalized Forward and SDN (66~74)

보충자료

### 4.4.1 Match

### 4.4.2 Action

### 4.4.3 match-plus-action 작업의 OpenFlow 예시