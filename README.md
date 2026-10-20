# 26_HC124

### The BD : Be Deep

BD-LiDAR 융합 기반 자율주행차량 사각지대 해소 및 능동형 AI 객체인식 시스템 개발

---

## 💡1. 프로젝트 개요

**1-1. 프로젝트 소개**

- **프로젝트 정의** : 액정 Beam Deflector(BD)를 단일 포인트 LiDAR의 송신부(Tx)와 수신부(Rx) 앞에 붙여 기계적 회전 없이 탐지 방향을 넓히고, 측정한 거리로 RC 차량의 정지와 재출발을 제어하는 프로젝트
- **구현 결과** : BD 구성에 따라 **검출 각도 2° → 12° → 20° 확장**, LIDAR-Lite v3 기반 RC 차량의 **장애물 감지와 정지 및 재출발 구현**
- **확장 방향** : YOLO 객체 인식과 Unity 시각화를 LiDAR 거리와 결합한 능동형 인지

<img width="1500" height="829" alt="Image" src="https://github.com/user-attachments/assets/7f0f99b4-0a0d-42ed-9d4c-b4eb831cd1de" />

</p>
<p align="center"><sub>LiDAR만 쓸 때와 BD를 붙였을 때의 검출 범위 비교 개념도</sub></p>

**1-2. 개발 배경 및 필요성**

| 개발 배경 | 해결하고자 하는 문제 |
| :--- | :--- |
| **자율주행 인지** | 카메라 영상만으로는 장애물까지의 거리를<br>정확히 알기 어려워 거리 정보가 함께 필요 |
| **회전형 LiDAR의 부담** | 본체를 모터로 돌려 시야를 넓히는 대신<br>가격, 부피, 차체 돌출, 유지보수 부담이 큼 |
| **단일 포인트 LiDAR의 한계** | 탐지 방향이 고정되어 설치 방향 밖의 주변을 보기 어렵고<br>여러 대를 달면 배선과 보정 부담이 늘어남 |
| **조향과 검출은 별개** | 빛을 원하는 방향으로 보내도 반사광이 수신부에<br>충분히 들어오지 않으면 거리가 출력되지 않음 |

**1-3. 프로젝트 특장점**

| 구분 | 핵심 내용 |
| :--- | :--- |
| **비기계식 조향** | 액정 BD의 전압 패턴만 바꿔 빛의 진행 방향 제어 |
| **송수신 동시 조향** | Tx 조향으로 생긴 수신 한계를 Rx BD로 보완해 검출 각도 확장 |
| **실물 차량 연결** | 측정 거리를 RC 차량의 정지와 재출발 제어까지 연결 |

<details>
<summary><b>동작 원리 자세히 보기</b></summary>

- **BD 조향 원리** : BD 하부의 패턴 전극에 서로 다른 전압을 걸면 액정 배향이 위치마다 달라져 유효 굴절률 차 Δn이 생기고, 위상 지연 Δφ = 2πΔnd/λ (d : cell gap)가 톱니 모양의 위상 격자를 이룬다. 빛은 격자 주기 p에 따라 θ = sin⁻¹(λ/p) 방향으로 회절하므로, 전압 패턴만 바꿔 조사 방향을 바꾼다.
- **Rx BD가 필요한 이유** : 조향된 beam의 반사광은 Rx 광축에서 기울어진 방향으로 돌아오는데, Rx 렌즈는 수용각 안으로 들어온 빛만 검출기에 모은다. Rx 앞의 BD가 이 빛을 Rx 광축 쪽으로 다시 꺾어 수신부에 모은다.
- **v3를 적용한 이유** : 레이저 광원인 v3는 beam 발산각이 작아 조향한 방향으로 에너지가 집중되므로, 그 방향에 있는 타깃의 반사광이 커져 검출이 개선된다.
- **다른 비기계식 조향 방식** : MEMS 미러는 진동 보상이, OPA는 크로스토크와 grating lobe 억제가 과제로 남아 있다. 액정 BD는 기존 LiDAR 앞에 붙여 쓸 수 있고, 응답 속도와 회절 효율이 개선 과제다.

</details>

<img width="833" height="362" alt="Image" src="https://github.com/user-attachments/assets/b3416481-c3d2-4b21-9938-5c7d3f6e6716" />
</p>
<p align="center"><sub>Tx와 Rx 앞에 BD를 하나씩 배치한 실험 구성</sub></p>

**1-4. 주요 기능**

| 기능 | 구현 내용 | 진행 상태 |
| :--- | :--- | :---: |
| **송수신 조향** | Tx BD로 송신 방향, Rx BD로 수신 방향 조절 | 구현 |
| **거리 취득** | LIDAR-Lite v3와 Arduino 간 I²C 통신 | 구현 |
| **거리값 처리** | 필터로 변동을 줄이고 무효 측정값 제외 | 구현 |
| **차량 제어** | 70 cm 이내 접근 시 정지, 90 cm 이상 확보 시 재출발 | 구현 |
| **AI 객체 인식** | YOLO 기반 영상 내 객체 검출 | 독립 구현 |
| **주행 시각화** | Unity 가상 LiDAR 감지 시 HUD 경고 표시 | 독립 구현 |

**1-5. 기대 효과 및 활용 분야**

- **사각지대 보완** : 고정된 단일 탐지 방향을 전기적으로 넓혀 주변 장애물 감지에 활용
- **구성 단순화** : LiDAR 한 대로 여러 방향을 살펴 LiDAR 수량과 배선을 줄이고, 본체를 기계적으로 돌리지 않아 회전 구동부의 마모 부담을 줄일 수 있음
- **적용 분야** : 소형 자율주행 플랫폼, 이동 로봇, 차량의 측면과 후방 근거리 장애물 감지

**1-6. 기술 스택**

| 구분 | 사용 기술 및 장비 | 활용 |
| :--- | :--- | :--- |
| 광학 조향 | 액정 BD (Tx용, Rx용), BD Driving Board | 송수신 방향 조절 |
| 거리 측정 | Garmin LIDAR-Lite v3 | 장애물 거리 측정 |
| 제어 보드 | Arduino UNO R4 WiFi | 거리 취득과 차량 제어 |
| 구동 장치 | 모터 드라이버 (TB6612FNG), DC 모터, RC 차량 | 주행과 정지 |
| 펌웨어 | Arduino IDE, C/C++ | LiDAR 통신과 제어 로직 |
| 데이터 분석 | MATLAB | 실험 데이터 정리와 비교 |
| 광학 설계 | Zemax | 수신 광학계 검토 |
| 객체 인식 | Python, YOLOv8 (Ultralytics), OpenCV | 영상 기반 객체 검출 |
| 시각화 | Unity, C# | 가상 LiDAR와 HUD 구현 |
| 광학 계측 | IR 카메라, Photodiode, Oscilloscope | beam 관찰과 세기 측정 |

---

## 💡2. 팀원 소개

| 구분 | 주요 역할 | 담당 내용 |
| :--- | :--- | :--- |
| **팀원 1** | 프로젝트 총괄 | 시스템 구성, 광학 실험 및 결과 종합 |
| **팀원 2** | 실험 및 분석 | 측정 데이터 정리, 성능 분석 및 개발보고서 |
| **팀원 3** | 실험 및 검증 | 조향 실험, 결과 비교 및 개발보고서 |
| **팀원 4** | 제어 및 시각화 | Arduino 차량 제어, Unity 및 제작설계서 |
| **팀원 5** | 자료 및 영상 | 발표 자료, 시연 영상 및 제작설계서 |
| **멘토** | 기술 자문 | 구현 방향 검토 및 기술 피드백 |

---

## 💡3. 시스템 구성도

**3-1. 하드웨어 구성**

<p align="center">
  <img width="1019" height="631" alt="Image" src="https://github.com/user-attachments/assets/f6461d4c-2244-4c54-b60c-7d08a25cc884" />
</p>

**3-2. 제작한 RC 차량**

<p align="center">
  <img width="1592" height="1278" alt="Image" src="https://github.com/user-attachments/assets/b0f9b5e3-82b7-4990-a3ea-3dc356a139ea" />
</p>

**3-3. 동작 흐름**

```mermaid
flowchart TD
    subgraph S1["① 광학 조향"]
        PC["PC : BD 구동 조건 설정"] -->|"USB"| DRV["BD Driving Board"]
        DRV --> TX["Tx BD : 송신 방향 조향"]
        DRV --> RX["Rx BD : 수신 방향 정합"]
    end

    subgraph S2["② LIDAR-Lite v3 거리 측정"]
        L1["조향된 방향으로<br/>레이저 송신"] --> L2["물체 반사광 수신"]
        L2 --> L3{"반사 신호가<br/>검출 임계값 초과?"}
        L3 -->|"예"| L4["거리값 출력"]
        L3 -->|"아니오"| L5["유효 신호 없음"]
    end

    subgraph S3["③ Arduino 거리 처리"]
        P1["I²C로 거리 취득"] --> P2["필터로 변동 감소"]
        P2 --> P3{"유효한 측정값?"}
        P3 -->|"아니오"| P4["제어 판단에서 제외<br/>직전 상태 유지"]
    end

    subgraph S4["④ 차량 제어"]
        E{"현재 차량 상태"}
        F{"70 cm 이내?"}
        G{"90 cm 이상?"}
        H["모터 정지"]
        I["모터 구동"]
        E -->|"주행 중"| F
        E -->|"정지 중"| G
        F -->|"예"| H
        F -->|"아니오"| I
        G -->|"예"| I
        G -->|"아니오"| H
    end

    subgraph S5["단독 동작 기능"]
        CAM["카메라 영상"] --> YOLO["YOLO 객체 인식"]
        VL["Unity 가상 LiDAR"] --> HUD["HUD 경고 표시"]
    end

    TX --> L1
    RX --> L2
    L4 --> P1
    L5 --> P1
    P3 -->|"예"| E
    H --> MD["모터 드라이버 → DC 모터"]
    I --> MD
    P1 -.->|"실험 데이터"| MAT["MATLAB<br/>데이터 정리와 비교"]
    YOLO -.->|"후속 연동"| E
    P2 -.->|"후속 연동"| VL

    classDef process fill:#EEEAFE,stroke:#9981D8,color:#222;
    classDef decision fill:#FFF3D6,stroke:#D8AE55,color:#222;
    classDef stop fill:#FCE8E6,stroke:#D98A82,color:#222;
    classDef drive fill:#E6F4EA,stroke:#7BAD8A,color:#222;
    classDef ext fill:#F4F4F4,stroke:#9A9A9A,stroke-dasharray:4 3,color:#222;

    class PC,DRV,TX,RX,L1,L2,L4,L5,P1,P2,P4,MD process;
    class L3,P3,E,F,G decision;
    class H stop;
    class I drive;
    class CAM,YOLO,VL,HUD,MAT ext;

    style S1 fill:#F7F5FF,stroke:#C2B4E2
    style S2 fill:#F3F7FC,stroke:#A9BED8
    style S3 fill:#F3F9F4,stroke:#A9CDB2
    style S4 fill:#FFFEED,stroke:#D5CD93
    style S5 fill:#FAFAFA,stroke:#BDBDBD,stroke-dasharray:4 3
```

- **정지와 재출발 기준을 나눈 이유** : 기준이 하나뿐이면 정지 직후 측정값이 잡음과 진동 때문에 기준 위아래를 오가며 정지와 출발이 번갈아 실행된다. 재출발 기준을 90 cm로 두면 측정값이 70 cm와 90 cm 사이에서 흔들리는 동안 직전 상태가 유지된다.

**3-4. 개발 과정 및 문제 해결**

| 개발 단계 | 확인한 문제 | 원인과 개선 |
| :--- | :--- | :--- |
| **Tx 단독 조향** | 빛의 방향은 바뀌었지만<br>일정 각도부터 검출이 늘지 않음 | 반사광이 Rx 수용각 밖으로<br>돌아오는 것으로 판단 |
| **수신 구조 검토** | 기울어져 돌아오는 반사광을<br>수신부로 모을 방법 필요 | Kepler, Cassegrain 광학계 검토<br>부피가 커지고 방향마다 맞추기 어려움 |
| **Rx BD 추가** | 고정 렌즈로는<br>조향 방향 변화에 대응하기 어려움 | Rx 앞 BD로 수광 방향을 맞춰<br>거리 검출 회복 |
| **v3 적용** | 차량 적용에 맞는<br>beam 특성과 검출 동작 필요 | 퍼짐이 작은 레이저 beam으로<br>조향과 타깃 검출 개선 |
| **제동거리 대응** | 속도를 높이자 50 cm 기준으로는<br>물체에 더 가까이 가서 멈춤 | 지연과 감속 중 이동 거리 증가<br>정지 기준을 70 cm로 조정 |

**핵심 확인 방법** : 타깃 위치와 Tx 조건을 유지한 상태에서 Rx BD만 조절해 거리 검출이 회복되는지 확인했다.

**3-5. 실험 결과**

**① BD 적용에 따른 검출 각도 확장**

| 비교 항목 | BD 미적용 | Tx에만 BD 적용 | Tx와 Rx에 BD 적용 |
| :--- | :---: | :---: | :---: |
| **검출 각도** | **2°** | **12°** | **20°** |

> **측정 조건** : LIDAR-Lite v3, 타깃 거리 70 cm, 검출 각도는 좌우를 합한 전체 범위, Arduino에 거리 값이 출력되는 각도를 검출로 판단

**② LIDAR-Lite v3 기반 차량 동작** : 70 cm 이내 접근 시 정지, 근접 상태에서 정지 유지, 90 cm 이상 거리 확보 후 재출발을 확인했다.

**3-6. AI 객체 인식 및 시각화**

<p align="center">
  <img width="2353" height="1217" alt="Image" src="https://github.com/user-attachments/assets/21634779-9265-4289-8e61-d798d84fdf08" />
</p>
<p align="center"><sub>Unity 기반 가상 LiDAR 및 HUD 시각화 화면 ,Unity 안의 가상 LiDAR(Ray)가 보행자를 감지하면 로그가 '감지됨'으로 바뀌고 윈드실드 HUD에 경고가 표시된다.</sub></p>

YOLO 객체 인식과 Unity 시각화는 각각 단독으로 동작한다. 후속 개발에서는 YOLO 검출 결과와 LiDAR 거리의 시간과 좌표를 맞추고 Unity에 실제 측정 데이터를 표시해, **어떤 객체가 어느 방향에 얼마나 가까이 있는지** 판단하도록 확장한다.

**3-7. 원격 제어 및 시스템 경량화**

본선 진출 후의 예산을 활용해 **LattePanda v1**을 시스템에 결합했다. 기존의 Arduino Uno R4 WiFi를 대체하였으며, 소형 컴퓨터의 역할을 할 수 있다는 점을 활용해 BD의 driving board와 연결하여 전압을 걸어주는 역할 또한 수행한다. 기존에 BD를 구동하기 위해서는 노트북 두 대로 각각의 BD를 구동하였다. 원격 제어를 통해 필요한 시스템 규모를 소형화하였다. 한편 Arduino는 LattePanda v1에 내장되어 있는 ATmega32u4(Arduino Leonardo)를 통해 구동하도록 한다. 원격 제어는 chrome 원격 데스크톱을 통해 제어가 가능하다.

---

## 💡4. 작품 소개영상

- **시연 내용** : BD-LiDAR 장애물 감지, RC 차량 자동 정지와 안전거리 확보 후 재출발

https://www.youtube.com/watch?v=qh-QOeuo1m0&feature=youtu.be

---

## 💡5. 핵심 소스코드

**5-1. LiDAR 거리 측정**

- 소스코드 설명 : LiDAR-Lite v3를 I2C로 트리거하고, busyflag가 풀리면 2byte 거리값(cm)을 읽는다.

```cpp
int distanceFast(bool biasCorrection)
{
  Wire.beginTransmission(LIDARLITE_ADDR_DEFAULT);
  Wire.write(0x00);
  Wire.write(biasCorrection ? 0x04 : 0x03);
  if (Wire.endTransmission() != 0) return -1;
  while (true)
  {
    Wire.beginTransmission(LIDARLITE_ADDR_DEFAULT);
    Wire.write(0x01);
    if (Wire.endTransmission() != 0) return -1;

    if (Wire.requestFrom((uint8_t)LIDARLITE_ADDR_DEFAULT, (uint8_t)1) >= 1)
    {
      if ((Wire.read() & 0x01) == 0) break;   // not busy
    }

    if (millis() - startMs > 50) return -1;   // timeout
  }
  Wire.beginTransmission(LIDARLITE_ADDR_DEFAULT);
  Wire.write(0x8f);
  if (Wire.endTransmission() != 0) return -1;
  if (Wire.requestFrom((uint8_t)LIDARLITE_ADDR_DEFAULT, (uint8_t)2) < 2) return -1;
  int d = Wire.read();
  d <<= 8;
  d |= Wire.read();
  return d;
}
```

**5-2. 노이즈 필터**

- 소스코드 설명 : 5~4000cm 밖의 값은 버리고 최근 5개 값의 이동평균을 내며, 5회 연속 실패하면 -1을 반환한다.

```cpp
#define FILTER_SIZE 5
#define MIN_VALID_CM 5
#define MAX_VALID_CM 4000
#define MAX_CONSECUTIVE_FAILS 5   
int readFilteredDistance(bool biasCorrection)
{
  int raw = distanceFast(biasCorrection);
  if (raw < MIN_VALID_CM || raw > MAX_VALID_CM)
  {
    failCount++;
    // Tolerate a few isolated glitches, but report failure if it persists
    // so the car does not keep driving on stale data.
    if (failCount >= MAX_CONSECUTIVE_FAILS) {
      return -1;
    }
    return lastGoodDistance;
  }
  failCount = 0;
  lastGoodDistance = raw;
  distBuffer[distBufferIndex] = raw;
  distBufferIndex = (distBufferIndex + 1) % FILTER_SIZE;
  if (distBufferIndex == 0) bufferFilled = true;
  int count = bufferFilled ? FILTER_SIZE : distBufferIndex;
  if (count == 0) return raw;
  long sum = 0;
  for (int i = 0; i < count; i++) sum += distBuffer[i];
  return (int)(sum / count);
}
```

```cpp
// 거리값 필터링 및 예외 처리 코드
```

**5-3. 자동 정지/재출발**

- 소스코드 설명 : 70cm 미만이면 정지하고 90cm 이상이면 다시 출발한다. 두 기준값의 간격(히스테리시스)으로 정지와 출발이 떨리는 걸 막는다.

```cpp
const int THRESHOLD = 70;        
const int START_THRESHOLD = 90;   
if (distance > 0 && distance < THRESHOLD && isDriving) {
  stopMotor();
}
else if (distance >= START_THRESHOLD && !isDriving && !isBraking) {
  goForward();
}
```

**5-4. 페일세이프 및 단락 브레이크**

- 소스코드 설명 : 주행 중 유효한 측정값이 없으면 즉시 브레이크를 건다. 정지할 때 모터를 단락 제동하고, 400ms 유지한 뒤 드라이버를 끈다.

```cpp
if (distance < 0 && isDriving) {
  stopMotor();
}
const uint32_t BRAKE_HOLD_MS = 400;
void stopMotor() {
  enableDriver();
  digitalWrite(AIN1, HIGH);
  digitalWrite(AIN2, HIGH);
  analogWrite(PWMA, 255);
  setSteerMotor(0);
  isDriving = false;
  isBraking = true;
  brakeStartMs = millis();
}
void releaseBrake() {
  analogWrite(PWMA, 0);
  digitalWrite(AIN1, LOW);
  digitalWrite(AIN2, LOW);
  setSteerMotor(0);
  disableDriver();
  isBraking = false;
}
if (isBraking && (now - brakeStartMs >= BRAKE_HOLD_MS)) {
  releaseBrake();
}
```

**5-5. 조향 트림 보정**

- 소스코드 설명 : a/d(±5), q/e(±1), 0(해제)으로 직진 편향을 보정하고, 주행 중이면 바로 반영된다.

```cpp
int steerTrim = 105;
const int STEER_TRIM_STEP = 5;
const int STEER_TRIM_FINE = 1;
const int STEER_TRIM_MAX = 200;
void adjustTrim(int delta) {
  steerTrim += delta;
  if (steerTrim > STEER_TRIM_MAX)  steerTrim = STEER_TRIM_MAX;
  if (steerTrim < -STEER_TRIM_MAX) steerTrim = -STEER_TRIM_MAX;
  if (isDriving) {
    setSteerMotor(steerTrim);
  }
  printTrim();
}
if (cmd == 'a') adjustTrim(+STEER_TRIM_STEP);
if (cmd == 'd') adjustTrim(-STEER_TRIM_STEP);
if (cmd == 'q') adjustTrim(+STEER_TRIM_FINE);
if (cmd == 'e') adjustTrim(-STEER_TRIM_FINE);
if (cmd == '0') {
  steerTrim = 0;
  setSteerMotor(0);
  printTrim();
}
```

**5-6. 시리얼 모터 제어**

- 소스코드 설명 : Space로 자동주행을 켜고 s로 정지한다. 정지 상태에서도 LiDAR는 계속 동작한다.

```cpp
bool motorControlEnabled = false;
while (Serial.available() > 0) {
  char cmd = Serial.read();
  if (cmd == ' ') {
    motorControlEnabled = true;
    Serial.println("MOTOR CONTROL ON");
  }
  if (cmd == 's') {
    motorControlEnabled = false;
    if (!isBraking) {
      stopMotor();
    }
    Serial.println("MOTOR STOP - LiDAR still running");
  }
}
```

**5-7. 거리 시리얼 출력**

- 소스코드 설명 : 모터 상태와 관계없이 50ms마다 현재 거리를 출력하고, 측정이 실패하면 에러 메시지를 출력한다.

```cpp
static const uint32_t PRINT_PERIOD_MS = 50;
distance = readFilteredDistance(true);
uint32_t now = millis();
static uint32_t lastPrint = 0;
if (now - lastPrint >= PRINT_PERIOD_MS) {
  lastPrint = now;
  if (distance >= 0) {
    Serial.println(distance);
  } else {
    Serial.println("Error: -1 (LiDAR read failed)");
  }
}
```
