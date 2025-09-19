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
  `RM0008 Reference Manual 참고`
  사용 보드: STM32F103RB
  ### bxCAN
  <img width="616" height="257" alt="image" src="https://github.com/user-attachments/assets/4107ce77-4256-4a45-abd6-2493a19c4fd4" />
  
  - STm32에서 bxCAN 통신은 Transceiver가 필수적.
    -> MCU pin에서 나오는 TX, RX는 3.3V 디지털 신호이고, CAN 버스에서는 차동신호(CANH/CANL)와 60Ω 부하를 운전하는 물리계층이 필요하기 때문에 전기적인 변환을 위해 **CAN 트랜시버**필요!
    
    <img width="677" height="737" alt="image" src="https://github.com/user-attachments/assets/854705b3-7e3a-4980-ba26-850c4841f432" />  

  - `Tx mailbox` : 유저가 CAN 메세지를 구성해 담을 수 있는 3개의 메일박스가 존재하며, 셋 중 어떤 메일 박스를 사용할지는 Transmission Scheduler가 ID 우선순위를 확인하여 결정
  - `Acceptance filters`: 사용자 정의로 수신 메세지를 필터링할 수 있는 28개의 Acceptance Filter banks가 존재. 필요한 메세지만 수신하고 나머지는 무시.
  - `Receive FIFO`: 하드웨어 단에서 수신 메세지를 저장하는 목적의 2개의 FIFO가 존재. 각 FIFO는 3개의 CAN 메세지 저장 가능
  - CAN2가 Slave인 이유는 SRAM memory에 직접 전근이 안되기 때문 -> 따라서, CAN1의 도움을 받아야함.
    하지만,, 내가 쓰는 STM32F103RB는 CAN 컨트롤러가 하나뿐 !!

**Test mode**  
1. Normal
  - 실제 버스 사용 (LSIM = 0, LBKM = 0)
  - ACK 필요
  - `CANTX` 실제 토글, `CANRX` 핀 사용
  
2. Silent(listen-only)
   - 버스 영향 없음 (항상 recessive만 출력)
   - 수신만 가능
   - 송신/ack/오버로드/에러플래그 내보내지 않음
   - `CANTX` 항상 recessive, `CANRX`로 버스만 청취

3. Loopback
   - 내부 루프백이므로 외부 합의 불필요
   - 내가 보낸 프레임을 내가 수신 (필터 통과 시 RX FIFO로 들어옴)
   - ACK 에러 무시
   - `CANTX`는 토글됨, `CANRX` 입력은 무시
  
    
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
* sample point: Tseg1과 Tseg2 사이에 안정적인 샘플링 위치를 의미하는 것으로 75% ~ 87.5% 사이정도로 설정하면 됨
  
$$
t_q = \frac{\text{BRP}}{f_{\text{CANclk}}}
$$  

  * APB1(=PCLK1): STM32내부에 CAN, USART2/3, I2C, SPI2같은 주변장치들은 APB1 클록을 공급받음. 
    <img width="722" height="522" alt="image" src="https://github.com/user-attachments/assets/10ece4ab-4f30-4991-bf2c-1b79ace04a55" />   
    <RM0008 Reference manual 참고>
    -> RCC_APB1ENR(APB1 Enable Register) 안에 bit25 = CANEN로 CAN 클록 사용한다는 것을 알 수 있음.  

### 실습
1. STM32 보드 설정
**STM32 보드 설정**
```
- APB1 = 36MHz, 목표 = 500kbps
- 선택: BRP = 4, TSEG1 = 13, TSEG2 = 2, SJW = 1
HSE 8MHz → PLL×9 = SYSCLK 72MHz
→ APB1 Prescaler /2 = PCLK1(APB1) 32MHz  ← CAN 모듈 입력
→ BRP(=Prescaler) 4로 분주 → tq = 4/32MHz = 0.125µs
→ Tbit = (1 + TSEG1 + TSEG2) × tq = (1+13+2)×0.125 = 2.0µs
→ Bitrate = 1/Tbit = 500 kbps
→ sample point = (1 + 13) / 16 = 87.5%
```
*Connectivity → CAN 설정*
<img width="652" height="610" alt="image" src="https://github.com/user-attachments/assets/15ee8a5e-314e-49b9-8493-f9456e6c597f" />  
-> 위와 같이 bitrate 설정 및 loopback 모드 설정   
-> NVIC: USB low priority or CAN RX0 interrupts Enable

<img width="658" height="290" alt="image" src="https://github.com/user-attachments/assets/707d3a3f-ecf6-4391-9c84-c553eabf7174" />  
- FIFO0에 수신이 생기면 콜백이 뜨는 라인

  *Connectivity -> USART2*
<img width="686" height="761" alt="image" src="https://github.com/user-attachments/assets/ad14f82c-ffcd-461d-ae11-0fd85f06b7db" />

2. 코드 작성

**CAN 초기화 코드**
-> 자동으로 생성
```C
static void MX_CAN_Init(void)
{

  /* USER CODE BEGIN CAN_Init 0 */

  /* USER CODE END CAN_Init 0 */

  /* USER CODE BEGIN CAN_Init 1 */

  /* USER CODE END CAN_Init 1 */
  hcan.Instance = CAN1;
  hcan.Init.Prescaler = 4;
  hcan.Init.Mode = CAN_MODE_LOOPBACK; // LOOPBACK 모드 생성
  hcan.Init.SyncJumpWidth = CAN_SJW_1TQ;
  hcan.Init.TimeSeg1 = CAN_BS1_13TQ;
  hcan.Init.TimeSeg2 = CAN_BS2_2TQ;
  hcan.Init.TimeTriggeredMode = DISABLE;
  hcan.Init.AutoBusOff = DISABLE;
  hcan.Init.AutoWakeUp = DISABLE;
  hcan.Init.AutoRetransmission = DISABLE;
  hcan.Init.ReceiveFifoLocked = DISABLE;
  hcan.Init.TransmitFifoPriority = DISABLE;
  // HAL_CAN_Init() 함수 호출 -> 1. CAN 초기화 
  if (HAL_CAN_Init(&hcan) != HAL_OK)
  {
    Error_Handler();
  }
```
<img width="741" height="400" alt="image" src="https://github.com/user-attachments/assets/da22bb66-2840-4b83-957f-c66629f21ce4" />
- **Init (INAK=1, SLAK=0)**: BTR/LBKM/SILM/필터 설정
- **Normal (INAK=0, SLAK=0)**: 송수신/중재/ACK
- **Sleep (INAK=0, SLAK=1)**: 저전력, 버스 청취
  
**실제 코드 흐름**
1. `HAL_CAN_Init()` : 드라이버가 Init 모드 진입 후 BRT/필터 반영  
2. 필터 설정: `HAL_CAN_ConfigFilter`  
3. 시작: `HAL_CAN_Start()` -> `INRQ=0` -> Normal 모드(INAK = 0)
4. 인터럽트 켜기: `HAL_CAN_ActivateNotification(,,, CAN_IT_RX_FIFO0_MSG_PENDING)`
5. 송신: `HAL_CAN_AddTxMessage()`
6. Loopback 모드 작동: 내가 보낸 프레임이 필터 통과 -> Rx0 콜백 호출(`HAL_CAN_RxFifo0MsgPendingCallback`)



