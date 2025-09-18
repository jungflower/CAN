# CAN 통신 프로젝트
## 이론
### 1. CAN 통신이란?
차량 내 여러 ECU들이 별도의 호스트 컴퓨터 없이 병렬로 연결되어 직접 데이터를 주고 받도록 설계된 표준 통신 프로토콜.
<img width="617" height="267" alt="image" src="https://github.com/user-attachments/assets/38ebc4b4-8e5e-4f9f-af70-187b45f35989" />
- 장점: 복잡한 배선 없이 다수의 장치가 효율적으로 통신 가능 !
- 배경: 비동기 직렬 통신인 UART를 사용했을 때 연결선이 너무 많이 필요해서 자동차의 무게 증가와 제조 원가 상승으로 이어짐 -> 구조적인 문제를 해결하기 위해 CAN 통신을 개발(여러개의 장치가 하나의 CAN 인터페이스를 통신할 수 있도록 !)

### 2. CAN 통신 특징
- CAN 버스를 통해 공용 통신 선 상에 데이터를 띄워두고, 필요한 장치만 해당 데이터를 필터링하여 수신하는 구조
- 신호 반사를 억제하기 위한 케이블 양끝단 `종단 저항 120[ohm]` 사용 권장
  <img width="653" height="397" alt="image" src="https://github.com/user-attachments/assets/3a666072-6513-44cb-b6e6-9254d625cd49" />

  1. 다중 주인 구조(Multi-Master) 구조 : CAN 버스는 여러 노드가 동시에 통신 버스를 공유
  2. 배선 단순화 : 두 가닥 신호선(CAN_H, CAN_L)만으로 통신 가능
  3. 노이즈에 강한 구조: 신호잡음과 간선을 줄이는데 도움이되는 `꼬임쌍선 케이블` 사용 -> CAN 드라이버가 데이터 전송 시 케이블 주위에 앙페르 오른 나사 법칙으로 인해 자기장을 만드는데 이 자기장들이 같은 방향이 되어 배가 되는 자기장이 생성되고 이는 다시 전기장을 일으켜 노이즈를 일으키는 상황을 만들기 때문,,,! 따라서, **꼬임선**을 활용하여 자기장의 방향을 반대로 만들어 자기장을 상쇄하여 노이즈 감소!
  4. ID기반 우선순위 처리: 각 메세지는 `고유한 ID값`을 가져 주소기반이 아닌 ID 기반 필터링을 통해 필요한 메세지만 수신 !! -> 메세지는 ID가 낮을수록 우선순위 높
  5. 고속 및 장거리 통신 지원: 최대 1Mbps 통신 속도 지원, 최대 1km 거리까지 통신 가능
 
  ### 3. CAN 통신 프레임 구조(메세지)  
  <img width="666" height="380" alt="image" src="https://github.com/user-attachments/assets/3716878a-d45b-4432-8387-f27b71c130a8" />
  
  1. SOF(Start of Frame): 메세지 시작을 알림   
  2. identifier(ID): 메세지 식별자 / 우선순위부여 -> 표준 CAN 11bit ID / 확장 CAN: 29bit ID   
  3. Control: 데이터 길이 정보(DLC)    
  4. Data: 실제 전송할 데이터  
  5. CRC: 오류 검출을 위한 체크 값  
  6. ACK: 수신 확인용 응답  
  7. EOF: 프레임 종료 알림  

  ### 4. CAN 통신 메세지 교환 방식 원리
  1. CAN 통신은 멀티 마스터 네트워크 기반 통신으로 CSMA/CD + AMP 방식으로 브로드캐스트로 메시지 전송
  2. CAN 버스에 메세지를 보내기 전에 CAN 버스 라인이 사용중인지를 파악하고 메시지간 충돌 검출 수행
  3. CAN 노드로부터 보내진 메세지는 송신 및 수신측 주소를 포함하고 있지 않음으로 주소 지정방식이 아닌 메세지의 처음 부분에 CAN 버스상에서 각 노드를 식별할 수 있도록 각 노드마다 유일한 ID(11bit or 29bit) 보유
  4. CAN 버스상 모든 전자장치는 CAN 버스상에 브로드 캐스드된 메세지를 수신한 후, 자신이 필요한 ID 메세지인 경우에만 받아들이고 그렇지 않은 경우는 무시
  5. CAN 버스상에 여러 메세지 존재 시 우선 순위가 높은 메시지가 CAN 버스의 사용 권한을 보장받으며 낮은 순위의 메세지는 자동으로 다음 CAN 버스 사이클에 재전송 되도록함.
  6. 각 CAN 메세지는 11bit의 ID(CAN 2.0A) 또는 29bit의 ID(CAN 2.0B)를 가지면 CAN 메시지의 처음 시작 부분에 위치하며 ID는 메시지의 형태를 식별시켜주는 역할과 메세지에 우선 순위를 부여하는 역할을 함
    -> 여기서 CAN2.0A와 CAN2.0B 끼리 서로 통신 불가
  <img width="630" height="218" alt="image" src="https://github.com/user-attachments/assets/5a5547d0-bb36-427b-9e95-e88767de040b" />

  ### 5. CAN 통신 활용
  1. 전기차, 자율주행 차 핵심 통신 수단
  2. 산업 자동화 및 다양한 임베디드 시스템 적용
     - 의료 기기 , 로봇 및 드론, 엘리베이터 및 지능형 빌딩 시스템, 농업기계 및 중장비

  ---
  ## CAN 통신 Loopback으로 구현 - STM32
### CAN 타임 퀀텀  
**CAN 시스템 클럭**: MCU 내부 버스에 연결된 CAN 컨트롤러에 공급되는 클럭을 적당한 분주한(Prescale) 클럭  
-> CAN 시스템 클럭의 주기 = `타임 퀀텀`  
<img width="651" height="351" alt="image" src="https://github.com/user-attachments/assets/a896f146-0d54-4680-b251-faceefe370a8" />  

**CAN 비트 세그먼트**
<img width="643" height="178" alt="image" src="https://github.com/user-attachments/assets/d3a96dd4-8aed-436e-b952-5214057f9a88" />    
- Normal Bit Time 구성은 4개의 독립적인 타임 세그먼트로 구성
  
<img width="548" height="52" alt="image" src="https://github.com/user-attachments/assets/37946e23-9667-409b-8bdc-c7ef1dc34acd" />

-> 각 세그먼트 듀레이션들은 타임퀀텀의 정수배다 !  
  <img width="468" height="197" alt="image" src="https://github.com/user-attachments/assets/00a2d837-d8d9-4b7e-9a36-6ce84ba3e531" />
  
- 1bit 시간(Tbit): `SYNC(항상 1tq) + TSEG1 + TSEG2` -> `Tbit​=(1+TSEG1+TSEG2)×tq​`
- bitrate: `1 / Tbit`
- `TSeg1 = PROP_SEG + PHASE_SEG1` / `TSeg2 = PHASE_SEG2`
  - STM32(bxCAN)에서는 TSEG1 , TSeg2로 뭉쳐서 설정
* `BRP(Baud Rate Prescaler)`: CAN 주변 장치는 APB1 클록(ex: 36MHz)를 받아서 먼저 BRP로 나눠 아주 작은 타임퀀텀 tq만드는 숫자
  
$$
t_q = \frac{\text{BRP}}{f_{\text{CANclk}}}
$$  

  * APB1(=PCLK1): STM32내부에 CAN, USART2/3, I2C, SPI2같은 주변장치들은 APB1 클록을 공급받음. 
    <img width="722" height="522" alt="image" src="https://github.com/user-attachments/assets/10ece4ab-4f30-4991-bf2c-1b79ace04a55" />   
    <RM0008 Reference manual 참고>
    -> RCC_APB1ENR(APB1 Enable Register) 안에 bit25 = CANEN로 CAN 클록 사용한다는 것을 알 수 있음.  

**STM32 보드 설정**
```
- APB1 = 36MHz, 목표 = 500kbps
- 선택: BRP = 9, TSEG1 = 6, TSEG2 = 1, SJW = 1
HSE 8MHz → PLL×9 = SYSCLK 72MHz
→ APB1 Prescaler /2 = PCLK1(APB1) 36MHz  ← CAN 모듈 입력
→ BRP(=Prescaler) 9로 분주 → tq = 9/36MHz = 0.25µs
→ Tbit = (1 + TSEG1 + TSEG2) × tq = (1+6+1)×0.25 = 2.0µs
→ Bitrate = 1/Tbit = 500 kbps
→ sample point = (1 + 6) / 8 = 87.5%
```
