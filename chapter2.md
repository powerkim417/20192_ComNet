# Chapter 2. Application Layer

## 2.1 Principles of network applications (2~17)

- 다양한 network app 존재(e-mail, web, 문자, 원격로그인, P2P, 다인용 네트워크 게임, 스트리밍 비디오, 음성통화, 실시간 화상회의, SNS, 검색, ...)

### 2.1.1 네트워크 애플리케이션 구조

- 네트워크 어플리케이션을 만들 때 갖춰야 할 점
  - 다양한 end system상에서 실행될 수 있어야 함
  - 네트워크 상에서 통신할 수 있어야 함
    - Ex) 웹 서버 소프트웨어가 브라우저 소프트웨어와 통신함
- network-core device를 위한 소프트웨어를 따로 작성할 필요가 없다
  - network-core device는 유저 어플리케이션을 실행하지 않음
  - end system상의 어플리케이션은 빠른 앱 개발과 전파를 할 수 있게 함

#### Client-server architecture

- 서버
  - 항상 host가 가능한 상태
  - 영구적인 IP주소
  - 네트워크의 확장(scaling)을 위한 데이터 센터
- 클라이언트
  - 서버와 통신
  - 간헐적으로 연결되어 있음
  - 동적 IP 주소
  - 클라이언트끼리 직접 통신하지는 않음

#### P2P architecture

- 항상 서버에 의존하지는 않음!
- 임의의 end system(=peer)끼리 직접 통신 가능
- 한 peer는 다른 peer에게 서비스를 요청하거나, 제공한다.
  - self scalability(자가 확장성): peer가 다른 peer에게 서비스를 제공함으로서 그 peer에게 서비스 제공 능력도 추가한다.
- Peer 간에는 간헐적으로 연결되어 있고, IP 주소가 변경됨
  - 복잡한 관리???

### 2.1.2 프로세스 간 통신

- 프로세스(Process): 호스트에서 실행되고 있는 프로그램
  - 같은 호스트의 두 프로세스는 inter-process communication(OS에 의해 정의됨)을 통해 서로 통신한다.
  - 다른 호스트의 두 프로세스는 message를 교환하며 통신한다.
  - 클라이언트 프로세스: 통신을 시작하는 프로세스
  - 서버 프로세스: 연결되기를 기다리는 프로세스
    - P2P 구조의 어플리케이션이 클라이언트/서버 프로세스를 가짐

#### Sockets

- 프로세스는 자신의 소켓에서 메시지를 송/수신한다. (집에서의 '문' 역할)
- 송신 프로세스는 수신 프로세스의 소켓에 메시지를 전달하기 위해 두 소켓 사이의 transport infrastructure에 의존한다.
- 소켓은 호스트의 application layer와 transport layer 간의 인터페이스(=API)

#### 프로세스 주소 배정

프로세스가 메시지를 수신하기 위해서는 식별자가 있어야 함 -> 주소

- 프로세스의 identifier: **호스트의 IP주소**와 **포트 번호**
  - 한 호스트에서 여러 프로세스가 실행될 수 있으므로 IP주소 하나만으로는 식별 불가
  - Ex) HTTP server: 80, mail server: 25
    - gaia.cs.umass.edu 웹 서버에 HTTP 메시지를 보내려면 128.119.245.12에 포트 번호 80으로 보내야 한다. 

### 2.1.3 애플리케이션에게 제공되는 Transport service

Transport 계층 프로토콜이 그것을 이용하는 애플리케이션에게 제공할 수 있는 서비스

여러 모드중에 한가지를 선택하는 것!

- <u>Data Integrity(신뢰적 데이터 전송)</u>
  - 이용: 파일 전송/웹 트랜잭션 등의 애플리케이션은 100% 신뢰 가능한 데이터 전송이 필요하므로
  - 이용하지 않음: 오디오와 같이 조금의 손실이 일어나도 되는 애플리케이션(loss-tolerant application)
- <u>Timing(시간)</u>
  - 이용: 지연이 최대한 없어야 의미가 있는 인터넷 전화, 상호작용 게임 등의 애플리케이션
- <u>Throughput(처리량)</u>
  - 이용: 최소한의 throughput이 보장되어야 하는 멀티미디어, 인터넷 전화 등의 애플리케이션(bandwidth-sensitive application)
  - 이용하지 않음: 전자메일, 파일/웹 전송과 같이 사용 가능한 throughput이 많아도 적어도 문제없이 이용할 수 있는 융통성 있는 application(elastic application)
- <u>Security(보안)</u>
  - 두 프로세스 사이에서 암호화/복호화 및 데이터 무결성을 제공

|    애플리케이션    |   Data Integrity   | Time sensitive |               Throughput                |
| :----------------: | :----------------: | :------------: | :-------------------------------------: |
|     파일 전송      | **O(비손실 필요)** |       X        |               X(elastic)                |
|       이메일       | **O(비손실 필요)** |       X        |               X(elastic)                |
|      웹 문서       | **O(비손실 필요)** |       X        |               X(elastic)                |
| 실시간 Audio/Video |  X(loss-tolerant)  |  **수백 ms**   | **A: 5kbps~1Mbps<br />V: 10kbps~5Mbps** |
| 저장된 Audio/Video |  X(loss-tolerant)  |   **수 초**    | **A: 5kbps~1Mbps<br />V: 10kbps~5Mbps** |
|   상호작용 게임    |  X(loss-tolerant)  |  **수백 ms**   |               **수 kbps**               |
|   텍스트 메시지    | **O(비손실 필요)** |   **O / X**    |               X(elastic)                |

### 2.1.4 인터넷 전송 프로토콜이 제공하는 서비스

- TCP 서비스
  - 연결 지향형 서비스: 클라이언트/서버 프로세스 간 통신 전 사전 설정(handshake) 필요(=full-duplex 연결)
  - 제공하는 것
    - Reliable Transport: 송/수신 프로세스 간 신뢰할 수 있는 전송
    - Flow control(흐름 제어): 데이터 처리 속도를 조절하여 수신자의 버퍼 오버플로우를 방지
      - 송신기->수신기로의 흐름을 제어함
    - Congestion Control(혼잡 제어): 네트워크 과부하시 송신자를 제어한다
      - 전송 계층에서 네트워크 계층으로의 진입을 제어함
  - 제공하지 않는 것: 시간, 최소 처리량 보장, 보안
- UDP 서비스
  - 송/수신 프로세스 간 메시지의 도착을 보장하지 않음!(Unreliable data transfer)
  - 제공하지 않는 것: 신뢰성, 흐름 제어, 혼잡 제어, 시간, 최소 처리량, 보안, 연결 설정(handshake를 하지 않음)
  - **그럼에도 불구하고 사용하는 이유**
    - congestion control 방식을 사용하지 않으므로 송신자는 데이터를 원하는 속도로 하위 계층(네트워크 계층)으로 보낼 수 있다.

|    애플리케이션     |      애플리케이션 계층 프로토콜       | 하위 전송 프로토콜 |
| :-----------------: | :-----------------------------------: | :----------------: |
|       이메일        |                 SMTP                  |        TCP         |
|  원격 터미널 접속   |                Telnet                 |        TCP         |
|         웹          |                 HTTP                  |        TCP         |
|      파일 전송      |                  FTP                  |        TCP         |
| 스트리밍 멀티미디어 |         HTTP(Ex. 유튜브), RTP         |     TCP or UDP     |
|     인터넷 전화     | SIP, RTP, 사설 프로토콜(Ex. 스카이프) |        TCP         |

#### 보안 TCP

- TCP와 UDP는 암호화가 없어 평문 비밀번호를 소켓에 전송한다. (매우 위험!)

- 이를 보완하기 위해 TCP를 보완한 SSL(Secure Socket Layer)를 개발!
  - TCP 연결에 암호화, 데이터 무결성 및 end-point authentication을 제공
  - <u>SSL은 인터넷 전송 프로토콜(Transport layer)이 아니고, application layer에서 구현되어 TCP를 도와주는 것!</u>
  - 애플리케이션이 SSL을 사용할 때
    1. 송신 프로세스가 평문 데이터를 SSL 소켓에 전달
    2. SSL은 데이터를 암호화하고 암호화된 데이터를 TCP 소켓에 전달
    3. 암호화된 데이터가 송신 TCP 소켓에서 수신 TCP 소켓으로 전달됨
    4. 수신 TCP 소켓은 암호화된 데이터를 SSL로 전달
    5. SSL이 암호화된 데이터를 복호화하고 SSL 소켓을 통해 평문 데이터를 수신 프로세스에 전달

## 2.2 Web and HTTP (18~43)

### 2.2.1 HTTP 개요

#### 웹 개요

- 웹 페이지는 object(HTML 파일, JPEG 이미지, Java 애플릿, ...)들의 집합으로 이루어져 있음
  - 기본 HTML 파일에 여러 참조 객체를 포함하는 형태
  - 각 object는 URL에 의해 참조 가능(host name + path name)

#### HTTP

- HypterText Transfer Protocol ; 웹의 application layer 프로토콜
- Client/server model
  - Client: HTTP 프로토콜을 사용하여 요청, 수신하고 웹 오브젝트를 표시하는 "브라우저"
  - Server: 웹 서버는 HTTP 프로토콜을 사용하여 request에 대한 response로 오브젝트를 보냄
- HTTP는 TCP를 전송 프로토콜로 사용함!
  1. 클라이언트가 서버로의 TCP 연결을 개시(소켓 생성), 포트 번호는 80ㄴ
  2. 서버는 클라이언트로부터 TCP 연결을 수락
  3. 브라우저(HTTP client)와 웹서버(HTTP server)간 HTTP message(application layer 프로토콜 메시지) 교환
  4. TCP 연결 종료
- HTTP는 클라이언트에 대한 정보를 유지하지 않으므로 stateless protocol 이라고 부름
  - 클라이언트 정보를 유지하는 프로토콜은 복잡하다!
    - 과거 기록을 다 저장해야 하고, 서버/클라이언트 간 충돌이 일어나면 state가 일관되지 않을 수 있어 조정을 해야함???

### 2.2.2 HTTP Connections

- Non-persistent HTTP

  - 1개의 TCP 연결 당 최대 1개의 object만 전송될 수 있다.

    - 전송 후 연결은 닫힘

  - 여러 개의 object를 다운로드하려면 여러 연결이 필요

    - Ex) 10개의 이미지가 있는 웹 페이지를 전송하는 경우

      - 기본 HTML 파일 + 10개의 이미지 => 총 11회의 TCP 연결이 일어남
      - 실제로는 10개의 이미지를 동시 연결하여 응답 시간을 줄인다.
      - RTT(Round Trip Time): 작은 패킷이 클라이언트로부터 서버까지 가고, 다시 클라이언트로 되돌아오는데 걸리는 시간
      - 총 소요 시간: 2RTT + 파일 전송 시간

      <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571652988177.png" alt="1571652988177" style="zoom:67%;" />

  - Non-persistent HTTP의 단점

    - 오브젝트 당 2RTT 이상의 시간 소요
    - 각 TCP 연결에 대한 OS 오버헤드 발생
    - 참조된 객체를 가져오기 위해 브라우저는 종종 병렬 TCP 연결을 한다???

- Persistent HTTP???

  - 클라이언트-서버 간 1개의 TCP 연결로 여러 object가 전송될 수 있음
  - Response 후에도 서버가 연결을 유지
  - 클라이언트/서버 간 후속 HTTP 메시지들은 열린 연결을 통해 보내진다
  - 클라이언트가 참조 오브젝트를 발견하면 바로 요청을 보냄
  - 참조된 모든 오브젝트에 대해 1RTT만큼 필요

### 2.2.3 HTTP 메시지 포맷

- 2종류의 HTTP 메시지: Request, Response
  - ASCII(human-readable format

#### HTTP Request Message

- 예시

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571653479305.png" alt="1571653479305" style="zoom:67%;" />

- 일반적 구조

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571653523793.png" alt="1571653523793" style="zoom:67%;" />

- Form 양식 전송 방식(method)
  - HTTP/1.0
    - **POST: input이 entity body에 담겨 서버로 전송됨**
    - **GET: input이 URL에 담겨 서버로 전송됨(www.asdf.com/search?age=25)**
    - HEAD: GET과 유사, HTTP 메시지로 응답 시 response에서 request object를 빼고 보냄
      - 애플리케이션 개발자가 주로 디버깅 시 사용
  - HTTP/1.1에서 추가된 method
    - PUT: URL 필드에서 명시된 entity body 내의 파일을 업로드 
    - DELETE: URL 필드에서 명시된 파일을 삭제

#### HTTP Response Message

- 예시

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571654190668.png" alt="1571654190668" style="zoom:67%;" />

- HTTP response status code
  - 200 OK: 요청이 성공되었고, 정보가 응답으로 보내졌다.
  - 301 Moved Permanently: 요청 객체가 영원히 이동되었다. 새로운 URL은 응답 메시지의 Location: 헤더에 나와있고, 클라이언트 소프트웨어는 자동으로 이 새로운 URL을 추출한다.
  - 400 Bad Request: 서버가 요청을 이해할 수 없다는 일반 오류 코드
  - 404 Not Found: 요청 문서가 서버에 존재하지 않는다.
  - 505 HTTP Version Not Supported: 요청 HTTP 프로토콜 버전을 서버가 지원하지 않음

### 2.2.4 사용자-서버 간 상호작용: Cookies

- 현재 웹 사이트에서 쿠키 기술은 4가지 요소에서 사용되고 있다.
  - HTTP response message의 쿠키 헤더 라인
  - HTTP request message의 쿠키 헤더 라인
  - 유저의 브라우저에 의해 관리되고 유저의 호스트에 저장되는 쿠키 파일
  - 웹사이트의 백엔드 데이터베이스
- 쿠키를 이용한 사용자 상태 유지
  1. 유저가 서버에 접속하면 서버가 사용자에 대한 ID를 생성한다.
  2. 그리고 Response로 Set-cookie: 헤더가 포함되어 온다.
  3. 그 이후의 접속에 대해서는 Request에 cookie: 헤더가 포함되어 간다.
- 쿠키가 사용되는 곳
  - 인증
  - 쇼핑에서의 장바구니
  - 추천
  - 유저 세션 상태(웹 이메일)
- "State"를 유지하기 위해
  - 프로토콜 종단점: 여러 트랜잭션에 대해 송/수신자의 상태를 유지
  - 쿠키: HTTP 메시지가 상태를 저장함
- Cookie와 개인 정보
  - 쿠키는 사이트가 유저의 정보를 얻을 수 있게 함
  - 유저는 사이트에게 보통 이름과 이메일을 제공함

### 2.2.5 Web Caches (Proxy Server)

- 웹 캐싱의 목적: Origin server를 대신하여 client의 HTTP request를 충족시켜줌
- 과정
  1. 브라우저가 웹 캐시와 TCP 연결을 설정하고 웹 캐시에 있는 object에 대한 HTTP request를 보냄
  2. 웹 캐시는 object의 사본이 자기에게 저장되어 있는지 확인. 만약 있다면 클라이언트 브라우저에 HTTP response 메시지와 함께 object를 전송
  3. 만약 없다면 웹 캐시는 origin server에 TCP 연결을 설정하고 object에 대한 HTTP request를 보냄. 요청을 받은 서버는 웹 캐시로 HTTP response와 함께 object를 보낸다.
  4. 웹 캐시가 object를 수신할 때, object를 local storage에 저장하고 클라이언트 브라우저에 HTTP response 메시지와 함께 object의 사본을 보낸다. 이 때, 이미 연결되어 있던 TCP 연결을 사용한다.
- 특징
  - 캐시는 서버에게는 클라이언트 역할을 하고, 클라이언트에서는 서버 역할을 한다.
  - 일반적으로 웹 캐시는 ISP가 구입하고 설치함
- 웹 캐싱을 사용하는 이유
  - 클라이언트의 request에 대한 response time 감소
  - 기관과 같은 곳의 접속 링크의 트래픽을 대폭 줄일 수 있음
  - poor content provider로 하여금 컨텐츠를 효과적으로 전달할 수 있게 함(P2P 파일 분배처럼)???
- 캐싱 예시
  - 평균 object size: 1000K bit (1MB/요청)
  - 오리진 서버에서 브라우저로의 평균 요청횟수: 15회/초
  - ~~서버가 브라우저로부터 줄 수 있는 데이터의 평균: 1.50Mbps (=1500Kbps)~~
  - 라우터-오리진 서버 간 RTT(=인터넷 지연): 2s
  - access link rate = 15Mbps / LAN link rate = 100Mbps
  - utilization = 평균 요청 rate * 요청 당 크기 / link rate
    - LAN utilization: (15요청/초) * (1MB/요청) / (100MB/초) = 15%
    - access utilization: (15요청/초) * (1MB/요청) / (15MB/초) = 100%
      - 여유가 없으므로 지연 시간이 거의 무한이 되므로 문제!
  - <u>total delay = internet delay + access delay + LAN delay</u>
  - = 2sec + 분(무한히 긴 시간) + usecs
  - Fatter access link를 사용하는 경우
    - access link rate를 100Mbps로 바꾸면 utilization이 15%로 감소하나,
    - access link rate를 증가하는 방법은 매우 큰 비용이 발생
  - Local cache를 사용하는 경우
    - cache의 hit rate가 0.4일 때
      - 40%는 cache에서, 60%는 오리진 서버에서 찾음
      - 60%만이 access link를 사용함(100% * 60% = 60%)
      - 이는 80% 미만의 트래픽 강도이므로 access link의 지연은 너무 작아 무시된다.
        - msec로 계산
      - <u>total delay = 오리진 서버 사용 딜레이 + 캐시 사용 딜레이</u>
      - 0.6\*(2초 + msec) + 0.4\*msec = **1.2초**

#### Conditional GET

- 목적: object가 최신일 경우 새로 보내지 않는다!
  - Object transmission delay가 없고, link utilization을 감소시켜줌
- Cache: HTTP request에 If-Modified-Since: 헤더를 추가해서 사용
- Server: Cached copy가 최신 버전이면 response에 object를 담지 않고 보냄(304 Not Modified)

## 2.3 Electronic mail (44~55)

- 3가지 구성 요소: User agent, Mail server, SMTP(Simple Mail Transfer Protocol)
  - User agent: 사용자가 메시지를 읽고, 응답하고, 전달하고, 저장하고, 구성하게 해줌
    - Ex) MS Outlook, Apple mail
    - 메시지 작성이 끝나면 User agent는 메시지를 mail server로 보내고, 메시지는 mail server의 출력 메시지 큐에 들어간다. 
    - 수신자가 메시지를 읽고 싶을 때 수신자의 user agent가 mail server에 있는 메일박스에서 메시지를 가져옴
  - Mail server: 전자메일 기반구조의 중심 역할
    - 각 수신자는 mail server 안에 mailbox를 갖고 있음
    - Message queue: 메일을 전송하기 위해 대기하는 곳
    - 메일의 송/수신에는 SMTP 프로토콜이 사용된다.

### 2.3.1 SMTP

- 메일 송/수신 과정
- <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571660396701.png" alt="1571660396701" style="zoom:67%;" />
  - 두 메일 서버가 멀리 떨어져 있어도 중간 서버를 사용하지 않고 SMTP 사용!
- 두 메일 서버가 같으면 아마 SMTP를 안 쓰겠지만.. SMTP는 두 메일 서버 간의 통신을 하는데 사용한다는 것이 포인트!
- RFC 2821
- TCP를 사용하여 전송(포트번호 25)
- 송신 서버에서 수신 서버로 direct transfer
- 전송의 3가지 단계
  1. Handshaking
  2. Transfer of messages
  3. Closure
- HTTP와 같이 command-response간 상호작용으로 구성됨
  - command: ASCII text
  - response: status code and phrase
- 메시지(header, body)는 7비트 아스키코드로 작성되어야 한다.
  - SMTP 서버는 CRLF.CRLF를 통해 메시지의 끝을 판별
- Persistent connection 사용

### 2.3.2 HTTP와의 비교

- 공통점: ASCII command/response 상호작용, status code
- 차이점
  - HTTP: pull, SMTP: push
  - HTTP: 각 object는 자신의 response message에 캡슐화되어있다 / SMTP: 여러 object가 mutipart message를 통해 보내짐

### 2.3.4 Mail Access Protocols

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571664529216.png" alt="1571664529216" style="zoom:67%;" />

- SMTP: 수신자의 서버에 전달 및 저장
- 메일 접근 프로토콜: 서버로부터 받아옴
  - POP(Post Office Protocol): 인증, 다운로드
  - IMAP(Internet Mail Access Protocol): 서버에 저장된 메시지 조작 기능 등 추가
  - HTTP: gmail, hotmail, yahoo mail 등

## 2.4 DNS (2~16)

호스트는 IP 주소로 식별됨

- Domain Name System
  - Distributed database: 여러 name server의 계층 구조로 구현됨
  - Application layer protocol: 호스트와 name server는 이름을 확인받기 위해 통신한다.

### 2.4.1 DNS가 제공하는 서비스

- 호스트 네임을 IP주소로 변환해줌
- 호스트 엘리어싱
  - 복잡한 호스트 네임을 가진 호스트는 별명을 가질 수 있는데, 제시한 별칭 호스트 네임에 대한 정식 호스트 네임을 제공해줌
- 메일 서버 엘리어싱
- 부하 분산
  - 인기 있는 사이트는 여러 서버에 중복되어 있는데, 이 경우 여러 IP 주소가 하나의 정식 호스트 네임과 연관되어 있음
  - DNS 데이터베이스는 이 IP주소 집합을 갖고 응답하는 식으로 여러 중복 서버들 사이에서의 트래픽 분산 효과를 냄

### 2.4.2 DNS 동작 원리 개요

#### 분산 계층 데이터베이스

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571667347041.png" alt="1571667347041" style="zoom:67%;" />

www.amazon.com 에 대한 IP를 찾는다면 Root DNS -> com DNS -> amazon.com 을 거쳐 얻게 된다.

- Root name server
  - 이름을 확인할 수 없는 local name server에 의해 연결됨
  - 아래 name server를 통해 매핑을 찾고 local name server에 매핑을 반환
- TLD(Top-level domain) server
  - com, or, net, edu 등과 국가도메인(kr, uk, fr, ca, jp, ...) 등에 해당
  - authoritative server들에 대한 ip주소 제공
- Authoritative server
  - 단체 소유의 DNS 서버
  - 단체의 네임드 호스트에 IP를 매핑시켜줌
  - 그림에서 맨 아래 리프 노드들!
  - 단체 또는 service provider에 의해 유지됨
- Local DNS name server
  - 계층 구조에 속하지는 않음
  - 각 ISP마다 하나씩 있음
    - Default name server라고도 불림
  - 호스트가 DNS 쿼리를 생성하면 쿼리가 자신의 local DNS server로 보내진다

#### DNS name resolution

- Iterated query

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571668010333.png" alt="1571668010333" style="zoom:67%;" />
  - 연결된 서버가 다음으로 연결할 서버를 알려주고, 호스트는 다음 서버에 계속 연결을 시도
    - "나는 잘 모르겠는데, 이 서버한테 물어봐"

- Recursive query

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571668182482.png" alt="1571668182482" style="zoom:67%;" />

  - 연결된 name server에게 짐을 넘기는 식

#### DNS caching

- query chain에서 DNS 서버가 DNS 응답을 받았을 때 그 매핑을 캐싱할 수 있다.
  - TTL 만큼의 시간이 지나면 사라짐
  - 일반적으로 TLD 서버는 local name server에 캐싱되어 있다
    - 그래서 root name server는 실제로 자주 방문되지 않음
  - 네임 호스트가 IP주소를 바꾸면 TTL이 다 만료되기 전까지 변경을 알 수 없다..?

### 2.4.3 DNS 레코드와 메시지

#### DNS records

- Resource records(RR)를 distributed DB에 저장한다.
- RR format: (name, value, type, ttl)
  - type = A
    - name: 호스트 네임, value: IP주소
  - type = NS
    - name: 도메인, value: 도메인의 authoritative name server의 호스트 네임
  - type = CNAME
    - name: 별칭 호스트 네임
    - vale: 정식 호스트 네임
  - type = MX
    - value: name과 연관된 메일서버의 이름

#### DNS message

쿼리와 답변 모두 같은 메시지 포맷을 가지고 있음

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571673610846.png" alt="1571673610846" style="zoom:67%;" />

#### DNS에 레코드 삽입

1. DNS registrar(등록 기관)에 이름을 등록
   - authoritative name server의 이름과 IP 주소를 제공받음
   - TLD server에 2개의 RR을 삽입한다
     - Ex) (asdfasdf.com, dns1.asdfasdf.com, NS) / (dns1.asdfasdf.com, 212.212.212.1, A)
2. www.asdfasdf.com의 type A 레코드와 asdfasdf.com의 type MX 레코드를 위해 authoritative server를 생성한다.

#### DNS 공격

- DDoS 공격
  - Root server 트래픽 공격
    - 다량의 패킷을 각 DNS 루트 서버에 보내 플러딩을 시도
    - 그러나, 패킷 필터로 인해 보호되어서 별로 영향이 없었고 
    - 사람들은 캐싱을 이용하므로 root server에 잘 가지 않아서 타격이 적었다.  
  - TLD server 트래픽 공격
    - 잠재적으로 더 위험함
- Redirect 공격
  - Man in middle: query 가로채기
  - DNS poisoning: DNS 서버에 가짜 응답을 보내서 그 서버가 자신의 캐시에 가짜 레코드를 받아들이도록 함
- DDoS에 노출시키기
  - 목표 호스트의 위장 출발지 주소를 가진 dns query를 많은 authoritative 서버 보낸다

## 2.5 P2P applications (17~26)

### 순수 P2P 구조

- 서버에 항상 의존하지 않음
- 임의의 end system(=peer)끼리 직접 통신 가능
- 한 peer는 다른 peer에게 서비스를 요청하거나, 제공한다.
- Ex) 파일 분배(BitTorrent), 스트리밍(KanKan), VoIP(Skype)

### File Distribution

#### File Distribution Time: Client-Server VS P2P

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571676576933.png" alt="1571676576933" style="zoom:67%;" />

- Q. 총 크기 F의 파일을 1개의 서버에서 N개의 peer에 분배하는데 걸리는 시간?
  - Client server
    - 서버에서 전송: 순차적으로 N개의 파일 복사본을 업로드 해야 하므로 N * F/u_s
    - 클라이언트: 각 클라이언트는 파일 복사본을 다운로드 해야 하므로 F*d_min
      - d_min은 가장 느린 클라이언트의 다운로드 속도
    - 즉 분배 시간은 min(NF/u_s, Fd_min)
      - NF/u_s 일 경우 N에 따라 linear하게 증가함
  - P2P
    - 서버에서 전송: 1개의 복사만 올리면 되므로 F/u_s
    - 클라이언트: 각 클라이언트는 파일 복사본을 다운로드해야 하므로 F*d_min
    - 클라이언트들: 총 NF bit를 다운받아야 하는데 이를 한꺼번에 받으려면 모든 업로드 속도를 다 더해야 하므로 NF / (u_s + Σu_i)
    - 즉 분배 시간은 min(F/u_s, Fd_min, NF / (u_s + Σu_i))
      - NF / (u_s + Σu_i)일 경우 N에 따라 분모 분자 모두 증가.. 즉 N이 커질 수록 c-s 보다 더 작은 시간이 걸리게 됨

#### BitTorrent

- 파일이 256kb의 chunk로 나누어짐

- 토렌트의 peer들은 이 file chunk를 송/수신 한다.

  - Torrent: file chunk를 교환하는 peer의 그룹
  - Tracker: 토렌트에 속한 peer들을 track한다.

- Peer joining torrent(피어가 토렌트에 들어갈 때)

  - 트랙커가 새 피어에게 피어 리스트를 제공하면, 새 피어는 모든 피어들에게 동시에 TCP 연결을 설정
  - 현재 연결이 되어있는 피어들을 neighbor peer라고 부름
    - 피어들끼리의 연결은 수시로 바뀜(끊길 수도 있음)
  - 한 피어가 파일을 전부 수신하면, 떠날 수도 있고 토렌트에 남을 수도 있음

- Requesting chunks

  1. 특정 시간에 다른 피어들은 다른 file chunk subset을 갖고 있음
  2. 주기적으로, 사용자는 각 peer에게 그들이 갖고 있는 chunk를 요구한다.
  3. 요청하는 순서는 "rarest first"
     - 가지고 있지 않은 chunk 중 이웃 가운데 가장 적은 복사본을 가진 chunk를 결정하고 이를 요구하는 방식

- Sending chunks: Tit-for-tat

  - 가장 효율적인 trade를 하면서 더 효율적인 trade 상대를 계속 찾는 방식!

  1. 유저가 가장 chunk를 빠르게 전달받는 4개의 피어를 선정하고 보낸다.
     - 다른 피어들은 유저에 의해 choke되어 있다(유저로부터 chunk를 못 받음)
     - 10초마다 4개의 피어를 새로 결정한다.
  2. 30초마다 새로운 피어를 임의로 선정하여 chunk를 보낸다
     - 이 피어는 optimistically unchoke된 것이다
     - 이 피어가 속도가 충분히 빠르면 top 4 리스트에 들어갈 수 있음

## 2.6 Video streaming and content distribution networks (27~38)

- Video traffic이 인터넷 대역폭의 주 소비자.
- 10억에 가까운 유저를 수용해야 하는데, 이질성(유선/모바일, 대역폭 풍족/부족)을 극복해야 함!
- 이를 해결하기 위해 분산된 애플리케이션 단계 인프라 구조를 마련해야 한다.

### 2.6.1 Video

- 비디오: 특정 속도로 표시되는 이미지의 연속(Ex. 24 images/sec)
- 디지털 이미지: 픽셀의 배열
- 코딩: 이미지 또는 이미지 사이를 조정하여 인코딩할 비트 수를 줄이는 것
  - Spatial(공간적), Temporal(시간적)

### 2.6.2 HTTP 스트리밍 & DASH

- DASH(Dynamic, Adaptive Streaming over HTTP)
  - 서버
    - 비디오 파일을 여러 개의 청크로 나눔
    - 각 청크는 각기 다른 비트율로 저장되고 인코딩된다.
    - manifest file: 서로 다른 청크의 URL을 제공하는 파일
  - 클라이언트
    - 주기적으로 s-c 대역폭을 측정
    - manifest file을 요청한 뒤, 한번에 한 청크만을 요청한다.
      - 현재 대역폭에 따라 지속 가능한 최대 coding rate를 선택
      - 다른 시점에는 다른 coding rate를 고를 수 있음(가용 대역폭 고려해서)
    - 효율적인 처리를 위해 클라이언트가 결정해야 할 것
      - 언제 청크를 요청할 지(버퍼 starvation이나 오버플로우가 발생하지 않게)
      - 어떤 encoding rate를 요청할 지(더 큰 대역폭이 가능하면 더 높은 품질을 요청)
      - 어디서 요청할 지(더 큰 대역폭이 가능한 곳 또는 클라이언트와 가까운 URL 서버)
    - 즉, 비디오 조각을 다운받으면서
      - 버퍼링이 충분히 되었고 대역폭이 충분하면 더 고품질로 바꿔 받고
      - 그 반대라면 저품질로 바꿔 받아서 이어나가는 것?

### 2.6.3 Content Distribution Networks(CDN)

- Q. 어떻게 해야 수백만개의 비디오중 하나를 골라 동시에 수천명의 사용자에게 스트리밍할 수 있을까?

  - 옵션 1: 하나의 큰 메가-서버 구축?

    - 단일 실패 지점: 진행 중 한 곳에서라도 실패하면 전체가 다 실패
    - 네트워크 혼잡 지점
    - 멀리 있는 클라이언트에게 잘 전달되기 힘듦
    - 같은 비디오가 같은 링크를 통해 여러번 반복 전송됨

    따라서, 이 방법은 적절하지 않다.

  - 옵션 2: 다수의 지점에 분산된 서버를 운영하여 비디오 등의 컨텐츠를 저장하고 제공(CDN)

    - enter deep: CDN 서버를 세계 곳곳의 접속 네트워크에 구축함으로써 ISP이 접속네트워크로 깊숙이 들어가는 개념
    - bring home: 보다 적은 수의 핵심 지점에 큰 규모의 CDN 서버 클러스터를 구축하여 ISP를 home으로 가져오는 개념

- CDN은 CDN 노드의 컨텐츠의 복사본을 저장한다

- 구독자는 CDN으로부터 컨텐츠를 요청

- OTT(Over the top) Challenges: 혼잡한 인터넷에서의 처리

  - 어느 CDN 노드로부터 컨텐츠를 받을까?
  - Congestion 속에서 취할 행동?
  - 어떤 CDN 노드에 어떤 컨텐츠를 넣을까?

#### CDN 컨텐츠 접근

- Bob이 http://netcinema.com/6Y7B23V 라는 비디오를 요청한다.
  - 비디오는 CDN의 http://KingCDN.com/NetC6y&B23V 에 저장되어있음

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571684076000.png" alt="1571684076000" style="zoom:67%;" />

1. 웹 페이지에서 URL을 받는다
2. 밥의 local DNS를 통해 URL 확인
3. netcinema의 DNS가 CDN의 URL을 반환
4. KingCDN의 authoritative DNS를 통해 CDN의 URL 확인
5. 비디오가 있는 KingCDN 서버의 IP주소를 사용자에게 알려줌
6. KingCDN 서버에게 비디오를 요청, HTTP를 통해 스트리밍

#### Case study: Netflix(distribute CDN, DASH streaming)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571684578986.png" alt="1571684578986" style="zoom:67%;" />

(그림 순서와 다름)

1. 사용자가 재생할 컨텐츠를 선택
2. 클라우드에서 실행중인 소프트웨어가 먼저 해당 컨텐츠 사본을 갖고 있는 CDN 서버를 결정
   - 컨텐츠 사본을 갖고 있는 CDN 서버중 최적의 서버를 선택!
3. CDN 서버가 결정되면 클라이언트는 요청된 컨텐츠의 다른 버전에 대한 URL을 가진 매니페스트 파일과 CDN 서버의 IP 주소를 보냄
4. 클라이언트와 해당 CDN 서버가 매니페스트 파일로 독점 버전의 DASH를 이용하여 직접 상호작용

## 2.7 Socket programming with UDP and TCP (39~43)

- 목표: 소켓을 이용하여 통신하는 클라이언트/서버 애플리케이션을 잘 만드는 것!
- UDP: unreliable datagram / TCP: reliable, byte stream-oriented

### 2.7.1 UDP 소켓 프로그래밍

- client & server 간의 연결이 없음!
  - 데이터 전송 전 handshaking 없음
  - 송신자는 각 패킷에 목적지의 IP주소와 포트번호를 첨부해서 보냄
  - 수신자는 받은 패킷에서 발신지의 IP주소와 포트번호를 첨부해서 보냄
- UDP의 경우 전송되는 데이터가 손실되거나 순서에 맞지 않게 도착할 수 있다.

### 2.7.2 TCP 소켓 프로그래밍

- 클라이언트와 서버는 연결되어야 함!
  - 서버 프로세스가 먼저 실행되어야 한다
  - 서버는 클라이언트의 연결을 받기 위해 socket을 생성해야 한다
- 클라이언트는 서버와 연락하기 위해
  - TCP 소켓을 만들고, 서버 프로세스의 IP주소와 포트번호를 준비해야 한다
  - 소켓이 만들어지면 클라이언트 TCP는 서버 TCP와의 연결을 시작한다
  - 클라이언트에 의해 연결이 되면, 서버 TCP는 서버 프로세스가 다른 특정 클라이언트와 통신하기 위한 새로운 소켓을 만든다.
    - 여러 클라이언트와 대화하기 위해
    - 클라이언트를 구분하기 위해 포트 번호 사용