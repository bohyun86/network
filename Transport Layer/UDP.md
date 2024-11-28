
### UDP란 무엇인가요?
**UDP(User Datagram Protocol)**는 네트워크 전송 계층의 프로토콜 중 하나로, **IP(Internet Protocol)** 위에서 동작합니다. UDP는 RFC 768에 의해 정의되어 있으며, **연결 없는(connectionless)** 프로토콜로 분류됩니다. 이는 TCP와 달리 데이터 전송 전에 연결 설정을 하지 않는다는 의미입니다.

UDP는 빠르고 간단하게 데이터를 전송하기 위해 설계되었으며, **신뢰성을 보장하지 않습니다**. 즉, 데이터를 수신자가 받을지, 손실이 발생하지 않을지, 도착 순서가 올바를지 보장하지 않는 특성을 가지고 있습니다.

#### UDP의 주요 특성
- **연결 없음**: TCP처럼 핸드셰이킹(handshaking) 과정 없이 데이터를 전송합니다. 송신자와 수신자 사이에 **연결 상태를 유지하지 않습니다**.
- **빠른 전송**: TCP의 신뢰성 보장 메커니즘(재전송 등)이 없기 때문에 더 빠른 데이터 전송이 가능합니다.
- **신뢰성 부족**: 데이터 손실, 오류, 순서 뒤바뀜 등에 대해 UDP는 대응하지 않습니다. 이러한 신뢰성은 응용 계층에서 직접 구현해야 합니다.

### UDP 데이터그램 구조
UDP 데이터그램은 두 부분으로 나눌 수 있습니다: **헤더(Header)**와 **데이터(Data)**입니다.

#### 헤더(Header)
UDP 헤더는 총 **8바이트(64비트)**로 구성되어 있으며, 각 필드는 **2바이트(16비트)**입니다. 필드 구성은 다음과 같습니다:
1. **송신 포트 번호 (Source Port)**: 데이터를 송신한 포트 번호.
2. **수신 포트 번호 (Destination Port)**: 데이터를 수신할 포트 번호.
3. **길이 (Length)**: 데이터그램 전체의 길이(헤더 + 데이터).
4. **체크섬 (Checksum)**: 오류 검출을 위해 사용됩니다.

#### 데이터(Data)
UDP 데이터그램의 나머지 부분은 **데이터 영역**입니다. 데이터의 최대 크기는 **65,528바이트**이며, 전체 데이터그램의 크기는 **65,536바이트**입니다. 헤더는 항상 8바이트이므로, 데이터 영역에는 최대 65,528바이트를 담을 수 있습니다.

### UDP 체크섬 계산
**UDP 체크섬**은 데이터가 전송 도중에 손상되었는지 확인하기 위해 사용됩니다. 체크섬을 계산하는 과정은 다음과 같습니다:

1. **데이터와 헤더를 16비트 단위로 분할**합니다. 만약 나눠떨어지지 않는다면 마지막 부분을 **0으로 패딩**합니다.
2. **모든 16비트 단어들을 더합니다**. 이때 오버플로우가 발생하면 초과된 비트를 다시 더해줍니다(감싸기 방식).
3. 최종 결과에 대해 **1의 보수(one's complement)**를 취합니다. 이 값이 체크섬으로 사용됩니다.

수신 측에서는 받은 메시지와 체크섬을 포함하여 모든 16비트 단어를 더한 결과가 **1111 1111 1111 1111**(모든 비트가 1)인지 확인합니다. 이 경우 데이터가 손상되지 않았다고 판단합니다. 만약 결과가 그렇지 않다면, 데이터에 손상이 발생한 것으로 간주합니다.

### UDP 사용 사례
UDP는 다음과 같은 경우에 주로 사용됩니다:

1. **빠른 응답이 필요한 경우**: TCP보다 UDP가 더 빠르기 때문에 **실시간 스트리밍**, **온라인 게임**, **VoIP(음성 통화)**와 같은 서비스에 적합합니다.
2. **신뢰성보다 속도가 중요한 경우**: 데이터 손실을 허용하면서 빠른 전송이 중요한 상황에서 사용됩니다. 예를 들어 **DNS**(Domain Name System)는 UDP를 사용하여 빠른 응답 속도를 보장합니다.

UDP를 사용하는 잘 알려진 애플리케이션:
- **Xbox Live**: 온라인 게임에서는 빠른 응답이 중요하며, 신뢰성보다는 **지연 시간**을 줄이는 것이 우선시됩니다.
- **DNS**: 도메인 이름을 IP 주소로 변환할 때 UDP를 사용합니다. 실패 시에는 다시 요청하거나 다른 서버로 요청을 보냅니다.
- **SNMP(Simple Network Management Protocol)**: 네트워크 관리 및 모니터링 프로토콜로, UDP를 사용하여 네트워크 상태를 모니터링합니다.

### tcpdump를 이용한 UDP 패킷 캡처 및 분석
**tcpdump**는 네트워크 인터페이스에서 송수신되는 **패킷을 캡처**할 수 있는 명령줄 도구입니다. 이를 통해 UDP 패킷을 캡처하고 분석할 수 있습니다.

#### tcpdump 기본 명령어
- `tcpdump`를 터미널에서 실행하면 네트워크에서 오가는 모든 패킷을 실시간으로 출력합니다.
- UDP 패킷을 보기 위해서는 필터링 옵션을 사용합니다.

#### 유용한 플래그
1. **출력 파일로 저장하기 (-w 플래그)**:
   - 캡처된 패킷을 나중에 분석할 수 있도록 파일로 저장할 수 있습니다.
   ```bash
   tcpdump -w output.pcap # 'output.pcap' 파일에 출력 저장
   ```
   - 이 파일은 **Wireshark**와 같은 프로그램으로 분석할 수 있습니다.

2. **캡처할 패킷 개수 지정하기 (-c 플래그)**:
   - 캡처할 패킷의 개수를 제한하여 필요 이상으로 많은 데이터를 캡처하지 않도록 할 수 있습니다.
   ```bash
   tcpdump -w output.pcap -c 10 # 10개의 패킷만 캡처
   ```

3. **저장된 파일 읽기 (-r 플래그)**:
   - 저장된 `.pcap` 파일을 나중에 읽어서 분석할 수 있습니다.
   ```bash
   tcpdump -r output.pcap # 'output.pcap' 파일의 패킷 출력
   ```

#### UDP 패킷 캡처 예제
특정 **UDP 패킷**을 캡처하고 그 내용을 확인하고 싶다면 다음과 같이 실행할 수 있습니다:
```bash
tcpdump udp -X -c 1 # 하나의 UDP 패킷 캡처
```
- **-X 플래그**: 페이로드(데이터)를 16진수와 ASCII 형식으로 동시에 출력합니다.
- **-c 1**: 하나의 패킷만 캡처하고 종료합니다.

### 1의 보수 (One's Complement)
**1의 보수**는 컴퓨터 시스템에서 데이터 무결성을 확인하거나 음수 표현에 사용됩니다. **1의 보수**를 취한다는 것은 **모든 비트를 반전**시킨다는 의미입니다:
- 예를 들어, 2진수 **0101**의 1의 보수는 **1010**입니다.
- 네트워크 체크섬 검증 시, 수신 측에서 **체크섬과 데이터**를 더한 결과가 모든 비트가 1이 되는지 확인하여 데이터 손상을 검증합니다.

### 요약
- **UDP**는 빠르고 신뢰성 없는 데이터 전송을 위한 프로토콜로, 연결 설정 없이 데이터를 전송합니다.
- **UDP 헤더**는 8바이트로 구성되며, 송신/수신 포트, 길이, 체크섬 필드를 포함합니다.
- **체크섬**은 데이터 손상을 검증하기 위한 값으로, 데이터의 16비트 단위 합산 후 1의 보수를 취해 계산됩니다.
- UDP는 신속한 응답이 필요한 경우나 신뢰성보다 속도가 중요한 상황에서 사용되며, 온라인 게임, DNS, 네트워크 관리 등의 응용 프로그램에 사용됩니다.
- **tcpdump**를 이용해 UDP 패킷을 실시간으로 캡처하고 분석할 수 있으며, 다양한 플래그를 통해 원하는 조건으로 패킷을 필터링할 수 있습니다.