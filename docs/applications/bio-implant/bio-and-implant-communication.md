# Bio/Implant Communication으로 연결하기

[TOC]

## Bio/Implant Communication으로 연결하기: 생체신호에서 무선 데이터 전송까지

지금까지 Digital Communication System을 공부하면서

- Information과 Signal
- Bit와 Symbol
- Digital Modulation
- Channel
- Noise와 BER
- Multiplexing / Multiple Access
- Spread Spectrum
- ADC / DAC
- RF Front-End
- Hardware Architecture

등을 살펴보았다.

이 개념들은 일반적인 Wi-Fi나 Cellular Communication에만 적용되는 것이 아니다.

신경신호를 측정하는 Neural Interface, Implantable Medical Device, Wearable Sensor와 같은 **Bioelectronic System**에서도 기본적인 Communication Structure는 동일하게 적용할 수 있다.

예를 들어 뇌 또는 신경계에서 발생하는 전기적 신호를 측정하여 외부 Computer로 전달한다고 생각해보자.



```text
Neural Activity
      ↓
Electrode
      ↓
Analog Front-End
      ↓
ADC
      ↓
Digital Processing
      ↓
Encoding
      ↓
Modulation
      ↓
Physical / Biological Channel
      ↓
External Receiver
      ↓
Signal Processing
      ↓
AI / Analysis
```



즉 지금까지 공부한 Digital Communication의 전체 구조가 그대로 등장한다.

다만 Bio/Implant System에서는 일반 통신 시스템보다

- Power
- Heat
- Size
- Safety
- Biological Compatibility
- Data Rate
- Long-term Reliability

등에 훨씬 강한 제약이 존재한다.

따라서 Bio/Implant Communication의 핵심은 단순히 데이터를 빠르게 보내는 것이 아니라

> **생체환경에서 제한된 전력과 Hardware Resource를 이용하여 필요한 정보를 안전하고 신뢰성 있게 전달하는 것**

이라고 볼 수 있다.

---

## 1. 일반적인 Digital Communication과 Bio/Implant Communication

일반적인 Digital Communication System은 다음과 같이 표현할 수 있다.



```text
Information Source
        ↓
Transmitter
        ↓
Channel
        ↓
Receiver
        ↓
Destination
```



Implant System 역시 같은 구조를 가진다.



```text
Biological Information Source
        ↓
Implant Transmitter
        ↓
Biological / Wireless Channel
        ↓
External Receiver
        ↓
Computer / Medical System
```



각 Block을 대응시키면 다음과 같다.

| 일반 Digital Communication | Bio/Implant Communication |
| --- | --- |
| Information Source | Neural Signal, ECG, EMG, Sensor Data |
| Input Device | Electrode, Biosensor |
| Transmitter | Implant IC |
| Channel | Tissue, Air, Inductive Link 등 |
| Receiver | External Reader / Wearable Device |
| Destination | Computer, Smartphone, Medical System |
| Processing | DSP, AI, Diagnostic Algorithm |

따라서 Bio/Implant Communication은 완전히 새로운 통신 이론이라기보다 **Digital Communication Theory가 매우 강한 Hardware·Power·Biological Constraint를 가진 환경에 적용되는 문제**라고 이해할 수 있다.

---



## 2. Bio Signal은 Information Source이다

Communication System의 시작은 항상 Information Source이다.

Bioelectronic System에서는 생체에서 발생하는 다양한 Signal이 Information Source가 될 수 있다.

대표적으로



```text
Neural Signal
ECG
EEG
EMG
ECoG
Temperature
Pressure
Glucose
Chemical Sensor Signal
```



등이 있다.

이 중 Neural Interface를 예로 들면



```text
Neuron
   ↓
Electrical Activity
   ↓
Electrode
```



의 구조를 생각할 수 있다.

즉 Neuron이 만드는 Electrical Activity가 전달하고자 하는 원본 Information이다.

---

## 3. Electrode는 생체와 전자회로의 Interface이다

Neuron의 Electrical Activity를 Digital Circuit이 직접 읽을 수는 없다.

생체 내부에서는



```text
Ion Movement
Electrochemical Potential
```



등을 기반으로 Signal이 형성된다.

반면 Electronic Circuit에서는



```text
Voltage
Current
Electron Movement
```



을 이용한다.

따라서 두 영역을 연결하는 Interface가 필요하다.

그 역할을 하는 대표적인 장치가 **Electrode**이다.



```text
Biological System
      ↓
Ion-based Signal
      ↓
Electrode Interface
      ↓
Electrical Voltage / Current
      ↓
Electronic Circuit
```



즉 Electrode는

> **Biological Signal을 Electronic Circuit이 처리할 수 있는 Electrical Signal과 연결하는 Interface**

라고 볼 수 있다.

---



## 4. Neural Signal은 매우 작다

Neural Recording에서 중요한 문제 중 하나는 Signal의 크기가 매우 작다는 것이다.

Electrode에서 측정되는 Signal은



```text
Noise
Electrode Impedance
Motion Artifact
Interference
```



등의 영향을 동시에 받을 수 있다.

따라서 Electrode에서 얻은 Signal을 바로 ADC에 입력하기보다는 일반적으로 먼저 **Analog Front-End(AFE)**를 거친다.



```text
Electrode
   ↓
Very Small Bio Signal
   ↓
Analog Front-End
   ↓
Amplified / Filtered Signal
```



---



## 5. Analog Front-End

Analog Front-End는 Sensor 또는 Electrode와 ADC 사이에 위치하는 Analog Circuit이다.

대표적인 구조는 다음과 같다.



```text
Electrode
   ↓
Low-Noise Amplifier
   ↓
Analog Filter
   ↓
ADC
```



AFE의 핵심 역할은



```text
Amplification
Filtering
Noise Reduction
Signal Conditioning
```



이다.

---



## 6. Low-Noise Amplifier

Neural Signal처럼 작은 Signal을 측정하려면 Amplifier가 필요하다.

하지만 Amplifier 자체도 Noise를 발생시킨다.

따라서



```text
Signal Gain ↑
+
Added Noise ↓
```



를 동시에 만족시키는 것이 중요하다.

이를 위해 **Low-Noise Amplifier(LNA)** 또는 저잡음 Analog Amplifier가 사용될 수 있다.



```text
Small Neural Signal
        ↓
Low-Noise Amplifier
        ↓
Larger Neural Signal
```



Receiver RF Front-End에서 배웠던 LNA와 목적은 비슷하다.

> **작은 Signal을 이후 Circuit이 처리할 수 있도록 증폭하면서 추가 Noise는 최대한 줄이는 것**

이다.

---



## 7. Analog Filtering

생체 Signal에는 원하는 Signal 외에도 다양한 Noise와 Interference가 존재할 수 있다.

예를 들어



```text
Electrode Signal
+
Power-line Interference
+
Motion Artifact
+
Electronic Noise
```



와 같은 형태이다.

따라서 Filter를 이용하여 원하는 Frequency Component를 선택한다.



```text
Raw Bio Signal
      ↓
Analog Filter
      ↓
Desired Signal Band
```



이 과정은 일반 Communication Receiver의



```text
Received Signal
      ↓
Frequency Selective Filter
```



와 같은 기본적인 개념이다.

---



## 8. ADC

Analog Front-End에서 처리된 Signal을 Digital Processing하기 위해서는 **ADC(Analog-to-Digital Converter)**가 필요하다.



```text
Analog Bio Signal
       ↓
Sampling
       ↓
Quantization
       ↓
Digital Samples
```



즉



```text
Continuous-time Signal
         ↓
Discrete-time / Digital Data
```



로 변환하는 과정이다.

---



## 9. Sampling Rate

ADC의 Sampling Rate는 측정하려는 Bio Signal의 Bandwidth에 따라 결정해야 한다.

기본적으로 Band-limited Signal을 Sampling하기 위해서는



$$
f_s > 2B
$$



의 조건을 고려한다.

여기서

- $f_s$ : Sampling Frequency
- $B$ : Signal Bandwidth

이다.

하지만 Sampling Rate를 무조건 높이는 것이 좋은 것은 아니다.



```text
Sampling Rate ↑
      ↓
ADC Power ↑
      ↓
Generated Data Rate ↑
      ↓
Storage ↑
      ↓
Wireless Transmission Load ↑
```



가 될 수 있기 때문이다.

따라서 Implant System에서는 필요한 Signal Information을 보존하면서 Sampling Rate를 최소화하는 것이 중요할 수 있다.

---



## 10. ADC Resolution

ADC Resolution 역시 중요한 설계 변수이다.

$n$ bit ADC는



$$
2^n
$$



개의 Quantization Level을 가진다.



```text
8 bit  → 256 Levels
10 bit → 1024 Levels
12 bit → 4096 Levels
16 bit → 65536 Levels
```



Resolution이 높으면 작은 Signal Difference를 더 세밀하게 표현할 수 있다.

하지만



```text
Resolution ↑
     ↓
Digital Data ↑
Power Consumption ↑
Circuit Complexity ↑
```



가 될 수 있다.

따라서 Implant System에서는

> **필요한 Signal Quality와 Power Consumption 사이의 Trade-off**

를 고려해야 한다.

---



## 11. 다채널 Neural Interface

Neural Interface는 하나의 Electrode만 사용하는 것이 아니라 여러 Electrode Channel을 사용할 수 있다.

예를 들어



```text
Electrode 1 ─→ AFE ─┐
Electrode 2 ─→ AFE ─┤
Electrode 3 ─→ AFE ─┼→ ADC / Digital Processing
Electrode 4 ─→ AFE ─┤
        ...          │
Electrode N ─→ AFE ─┘
```



와 같은 구조이다.

Channel 수가 많아지면 더 많은 Neural Information을 측정할 수 있다.

하지만 동시에



```text
Channel Count ↑
      ↓
AFE Circuit ↑
ADC Throughput ↑
Data Rate ↑
Memory ↑
Transmission Rate ↑
Power ↑
```



라는 문제가 발생한다.

---



## 12. Neural Interface에서 Data Rate 문제

예를 들어 단순화하여



```text
100 Channels
× 20,000 Samples/s
× 12 bits/sample
```



라고 하자.

Raw Data Rate는



$$
100 \times20,000 \times12
$$



이므로



$$
24,000,000\ bits/s
$$



즉



$$
24\ Mbps
$$



가 된다.

여기에는 Packet Overhead, Error Correction Bit 등도 포함되지 않았다.

Channel 수가 증가하면 Raw Data Rate는 매우 빠르게 증가한다.



```text
Electrode Count ↑
      ↓
Raw Data ↑
      ↓
Communication Load ↑
```



따라서 High-channel-count Neural Interface에서는 **어떤 데이터를 실제로 전송할 것인가**가 매우 중요한 문제이다.

---



## 13. Raw Data를 모두 보내야 하는가?

가장 단순한 방법은 측정한 모든 데이터를 외부로 보내는 것이다.



```text
Electrode
   ↓
ADC
   ↓
Raw Samples
   ↓
Wireless Transmission
```



장점은 Implant 내부 Processing이 단순하다는 것이다.

하지만 단점은 매우 큰 Data Rate가 필요할 수 있다는 점이다.

따라서 다른 방법으로 Implant 내부에서 일부 Signal Processing을 수행할 수 있다.



```text
Raw Signal
    ↓
Filtering
    ↓
Feature Extraction
    ↓
Event Detection
    ↓
Compression
    ↓
Reduced Data
```



---



## 14. Edge Processing

Sensor나 Implant 가까이에서 데이터를 처리하는 것을 넓은 의미에서 **Edge Processing** 관점으로 볼 수 있다.

예를 들어 Neural Signal 전체를 계속 보내는 대신 Spike Event만 검출한다고 하자.



```text
Raw Neural Signal
       ↓
Spike Detection
       ↓
Spike Event
       ↓
Timestamp / Feature
       ↓
Transmission
```



이렇게 하면 Transmission Data를 크게 줄일 가능성이 있다.

---



## 15. Communication Energy와 Computation Energy

하지만 Local Processing도 공짜가 아니다.

Signal Processing을 수행하려면 Digital Circuit에서 Energy를 소비한다.

따라서 다음 두 가지를 비교해야 한다.



```text
Option A

Raw Data
   ↓
Low Local Computation
   ↓
High Communication Data
```




```text
Option B

Raw Data
   ↓
Local Processing
   ↓
Compression / Feature Extraction
   ↓
Low Communication Data
```



즉

> **계산에 Energy를 사용할 것인가, 통신에 Energy를 사용할 것인가?**

라는 Trade-off가 발생한다.

이를 개념적으로



```text
Total Energy
=
Sensing Energy
+
ADC Energy
+
Processing Energy
+
Communication Energy
```



로 생각할 수 있다.

---



## 16. Source Coding과 Bio Signal

앞에서 배운 Source Coding의 목적은



```text
Redundancy 제거
→ Data Size 감소
```



였다.

이 개념은 Bio Signal에도 그대로 적용된다.

예를 들어 여러 Neural Channel 사이에 높은 Correlation이 존재하거나 Signal 중 실제 관심 Event가 드물다면 모든 Sample을 그대로 보낼 필요가 없을 수 있다.



```text
Raw Bio Data
      ↓
Source Coding / Compression
      ↓
Reduced Bit Stream
```



이를 통해

- Wireless Data Rate 감소
- Storage 감소
- Transmission Energy 감소

등을 얻을 수 있다.

---



## 17. Channel Coding은 왜 필요한가?

Implant Communication Channel 역시 Error가 없는 이상적인 Channel이 아니다.

전송 과정에서는



```text
Noise
Interference
Attenuation
Fading
Tissue Loss
```



등의 영향을 받을 수 있다.

따라서 필요한 경우 Channel Coding을 이용하여 Reliability를 높일 수 있다.



```text
Data
 ↓
Channel Encoder
 ↓
Redundant Bits
 ↓
Transmission
 ↓
Channel Decoder
 ↓
Error Detection / Correction
```



하지만 Channel Coding을 강하게 하면



```text
Redundant Bit ↑
Transmission Data ↑
Decoder Complexity ↑
Power ↑
```



가 될 수 있다.

따라서 Reliability와 Power 사이의 Trade-off를 고려해야 한다.

---



## 18. Implant Communication의 Physical Channel

Implant에서 외부 장치로 정보를 전달하는 방식은 하나만 존재하는 것이 아니다.

시스템 목적에 따라 여러 Physical Channel을 고려할 수 있다.

대표적인 예로



```text
RF Electromagnetic Communication
Inductive / Magnetic Coupling
Ultrasound
Optical Communication
Wired Communication
```



등을 생각할 수 있다.

각 방식은

- Penetration
- Data Rate
- Power
- Antenna / Transducer Size
- Alignment
- Tissue Interaction

등의 특성이 다르다.

---



## 19. RF Communication

RF Communication에서는 Electromagnetic Wave를 이용하여 정보를 전달한다.



```text
Implant
  ↓
RF Transmitter
  ↓
Tissue
  ↓
External Antenna
  ↓
Receiver
```



일반 Wireless Communication과 구조는 비슷하다.

하지만 Human Tissue가 Channel의 일부가 된다는 차이가 있다.

---



## 20. Tissue는 단순한 Free-space Channel이 아니다

일반적인 Wireless Communication에서는 종종 Air 또는 Free Space를 Channel로 생각한다.

Implant Communication에서는 Signal이 Tissue를 통과할 수 있다.



```text
Implant
   ↓
Muscle
Fat
Skin
   ↓
External Receiver
```



생체조직의 Electrical Property는 Frequency에 따라 달라질 수 있으므로 Signal의



```text
Attenuation
Propagation
Antenna Behavior
```



등에 영향을 줄 수 있다.

즉 Implant Communication에서는

> **Body 자체가 Communication Channel의 일부**

가 된다.

---



## 21. Inductive Coupling

가까운 거리에서 Magnetic Coupling을 이용하여 Power 또는 Data를 전달할 수도 있다.

기본 구조는 다음과 같다.



```text
External Coil
      ))))
 Magnetic Coupling
      ((((
Implant Coil
```



이를 이용하여



```text
Power Transfer
Data Communication
```



를 구현할 수 있다.

Near-field System에서는 Implant 내부에 큰 Battery를 넣지 않고 외부에서 Energy를 공급하는 구조를 고려할 수 있다.

---



## 22. Wireless Power Transfer

Implant Device에서는 Data Communication뿐 아니라 **Power Delivery**도 중요한 문제이다.



```text
External Power Source
       ↓
Wireless Power Link
       ↓
Implant
       ↓
AFE / ADC / Processor / Transmitter
```



Battery를 사용하지 않거나 Battery Size를 줄이기 위해 외부에서 Wireless Power를 공급하는 시스템을 고려할 수 있다.

따라서 Implant Design에서는



```text
Data Link
+
Power Link
```



를 함께 설계해야 할 수 있다.

---



## 23. Downlink와 Uplink

Implant Communication은 한 방향으로만 이루어질 필요가 없다.

#### Uplink

Implant에서 외부로 정보를 전송한다.



```text
Implant
   ↓
Neural / Sensor Data
   ↓
External Receiver
```



#### Downlink

외부 시스템이 Implant로 명령이나 설정값을 전달한다.



```text
External Controller
        ↓
Command / Configuration
        ↓
Implant
```



예를 들어

- Sampling Rate 변경
- Stimulation Parameter 설정
- Device Mode 변경

등이 Downlink를 통해 전달될 수 있다.

---



## 24. Closed-loop Bioelectronic System

Recording만 하는 시스템을 **Open-loop** 관점으로 볼 수 있다.



```text
Biological Signal
       ↓
Recording
       ↓
External Analysis
```



하지만 Recording 결과를 이용하여 다시 Biological System을 자극할 수도 있다.



```text
Neural Activity
      ↓
Recording
      ↓
Signal Processing
      ↓
Decision
      ↓
Stimulation
      ↓
Neural System
      ↓
Neural Activity
```



이를 **Closed-loop System**이라고 볼 수 있다.

---



## 25. Closed-loop에서 Communication이 중요한 이유

Closed-loop System에서는 단순히 Data를 전송하는 것 외에도 **Latency**가 중요해진다.



```text
Sensing
   ↓
Processing
   ↓
Decision
   ↓
Communication
   ↓
Stimulation
```



이 전체 과정이 너무 느리면 원하는 Feedback Control을 수행하기 어려울 수 있다.

따라서 Closed-loop Implant에서는



```text
Reliability
+
Low Latency
+
Low Power
```



를 동시에 고려해야 할 수 있다.

---



## 26. Stimulation Path

Neural Interface에서는 Recording과 반대로 외부 명령을 이용하여 Neural Tissue에 자극을 전달할 수도 있다.



```text
External Command
      ↓
Receiver
      ↓
Digital Controller
      ↓
DAC / Stimulator
      ↓
Electrode
      ↓
Neural Tissue
```



Recording Path와 비교하면 방향이 반대이다.

#### Recording



```text
Biological
→ Analog
→ Digital
```



#### Stimulation



```text
Digital
→ Analog / Electrical Pulse
→ Biological
```



따라서 ADC와 DAC의 역할도 서로 대응된다.

---



## 27. Bidirectional Neural Interface

Recording과 Stimulation을 모두 수행한다면 다음과 같은 Bidirectional System이 된다.



```text
                 Recording

Neuron
  ↓
Electrode
  ↓
AFE
  ↓
ADC
  ↓
Digital Processing
  ↓
Communication
  ↓
External System


                 Stimulation

External System
  ↓
Communication
  ↓
Digital Controller
  ↓
Stimulator / DAC
  ↓
Electrode
  ↓
Neuron
```



즉 **Communication + Sensing + Stimulation**이 하나의 시스템으로 결합된다.

---



## 28. Bio/Implant System에서 Synchronization

앞에서 Digital Communication에서 Synchronization이 중요하다는 것을 살펴보았다.

Bio/Implant System에서도 동일하다.

여러 Sensor Channel의 Data를 분석하려면



```text
Channel 1
Channel 2
Channel 3
...
```



사이의 Timing을 맞춰야 할 수 있다.

또한 Wireless Receiver에서도



```text
Carrier Synchronization
Symbol Synchronization
Frame Synchronization
```



이 필요할 수 있다.

다채널 Neural Recording에서는 추가로



```text
Sampling Time Alignment
```



도 중요한 문제가 될 수 있다.

---



## 29. Multiplexing

여러 Electrode Channel을 하나의 ADC 또는 Communication Link로 보내기 위해 Multiplexing을 사용할 수 있다.



```text
Channel 1 ─┐
Channel 2 ─┤
Channel 3 ─┼→ Multiplexer → Shared ADC / Link
Channel 4 ─┘
```



이를 통해 Hardware Resource를 공유할 수 있다.

대표적으로 Time Division 방식으로 여러 Channel을 순차적으로 Sampling할 수 있다.



```text
Time →

CH1 | CH2 | CH3 | CH4 | CH1 | CH2 | ...
```



하지만 Channel 수가 증가하면 필요한 Sampling Speed와 Switching Speed도 함께 증가할 수 있다.

---



## 30. Multiple Access

여러 Implant 또는 Sensor Node가 하나의 External Receiver와 통신해야 한다면 Multiple Access 문제도 발생한다.

예를 들어



```text
Implant A ─┐
Implant B ─┼→ External Receiver
Implant C ─┘
```



각 Device의 Signal을 구별하기 위해

- Time
- Frequency
- Code
- Space

등의 Resource를 나눌 수 있다.

즉 앞에서 공부한



```text
TDMA
FDMA
CDMA
```



등의 개념이 Bio Sensor Network에도 연결될 수 있다.

---



## 31. Spread Spectrum과 Implant Communication

Spread Spectrum 역시 특정 Bio/Medical Wireless System에서 고려할 수 있는 Communication Technique이다.

DSSS를 사용하면



```text
Data
 ↓
PN Code
 ↓
Wideband Spread Signal
```



로 만들어 Interference에 대한 Robustness를 얻을 수 있다.

FHSS는



```text
Frequency
f1 → f4 → f2 → f3 ...
```



처럼 Frequency를 이동하면서 특정 Narrowband Interference의 영향을 줄일 수 있다.

하지만 Spread Spectrum은 일반적으로



```text
Synchronization
Bandwidth
Circuit Complexity
```



등의 추가 Resource를 요구한다.

따라서 Implant System에서는 그 장점이 Power와 Hardware Cost를 정당화하는지를 함께 고려해야 한다.

---



## 32. Modulation 방식 선택

Implant Communication에서도 어떤 Modulation을 사용할 것인지 결정해야 한다.

예를 들어



```text
ASK / OOK
FSK
PSK
QPSK
QAM
```



등을 생각할 수 있다.

고차 Modulation을 사용하면



```text
M ↑
→ bits/symbol ↑
→ Spectral Efficiency ↑
```



의 장점이 있다.

하지만 일반적으로 QAM/PSK 계열에서는 더 높은 SNR과 정밀한 RF Circuit이 요구된다.



```text
M ↑
→ Detection Margin ↓
→ Required SNR ↑
→ Circuit Requirement ↑
```



따라서 초저전력 Implant에서는 항상 가장 높은 $M$이 좋은 선택은 아니다.

---



## 33. Coherent vs Non-coherent Receiver

Receiver Architecture 역시 중요하다.

#### Coherent Receiver

Carrier의 Frequency와 Phase Reference를 복원하여 Detection한다.



```text
Performance ↑
하지만
Synchronization / Circuit Complexity ↑
```



#### Non-coherent Receiver

절대적인 Carrier Phase 복원 없이 Signal을 Detection할 수 있다.



```text
Receiver Complexity ↓
Power ↓ 가능

하지만
Performance Penalty 가능
```



따라서 초저전력 Implant에서는 경우에 따라 **약간의 Communication Performance를 희생하고 Receiver를 단순화하는 선택**이 의미가 있을 수 있다.

---



## 34. Power Consumption

Implantable System에서는 Power가 가장 중요한 설계 변수 중 하나이다.

전체 Power를 단순하게 나누면



```text
Total Power

= Sensing
+ Analog Front-End
+ ADC
+ Digital Processing
+ Memory
+ Communication
+ Stimulation
```



으로 생각할 수 있다.

각 Block을 독립적으로 최적화하는 것보다 **전체 System Energy를 최소화하는 것**이 중요하다.

---



## 35. 왜 Communication Power가 문제가 되는가?

Wireless Transmission은 많은 Energy를 요구할 수 있다.

특히



```text
Data Rate ↑
Transmission Distance ↑
Required Reliability ↑
```



와 같은 조건에서는 Communication Energy가 커질 수 있다.

따라서 Implant 내부에서 Data를 줄이면 Communication Energy를 줄일 가능성이 있다.



```text
Raw Data
   ↓
Compression
   ↓
Smaller Data
   ↓
Lower Communication Load
```



---



## 36. Heat Generation

Implant Device에서 소비된 Electrical Power의 상당 부분은 결국 Heat로 변한다.



```text
Power Consumption
      ↓
Heat Generation
      ↓
Tissue Temperature ↑
```



따라서 Implant에서는 일반적인 Electronic Device보다 **Thermal Management와 Tissue Safety**가 중요하다.

이 때문에



```text
High-performance
```



만을 목표로 Hardware를 설계할 수 없다.

---



## 37. Size Constraint

Implant Device는 가능한 작아야 하는 경우가 많다.

하지만 System에는



```text
Electrode
AFE
ADC
Processor
Memory
Transceiver
Power Management
Antenna / Coil
```



등이 필요하다.

따라서



```text
Functionality ↑
      vs
Chip / Device Size ↓
```



의 Trade-off가 발생한다.

---



## 38. Antenna Size와 Frequency

일반적으로 Electromagnetic Antenna의 Physical Size는 사용 Frequency의 Wavelength와 밀접한 관계가 있다.

Wavelength는



$$
\lambda=\frac{v}{f}
$$



로 표현할 수 있다.

따라서 Frequency가 증가하면 Wavelength가 짧아져 Antenna Miniaturization 측면에서 유리한 요소가 생길 수 있다.

하지만 Tissue Propagation Loss, Circuit Complexity, Power 등 다른 특성도 Frequency에 따라 달라진다.

따라서

> **Frequency를 높이면 무조건 Implant Communication에 유리하다**

라고 단순하게 결론 내릴 수는 없다.

---



## 39. Reliability

Medical 또는 Neural System에서는 Communication Error가 단순한 Video Quality 저하와 다른 의미를 가질 수 있다.

따라서 Application에 따라 매우 높은 Reliability가 요구될 수 있다.



```text
Noise
Interference
Packet Loss
Bit Error
      ↓
Incorrect Data
      ↓
Incorrect Interpretation
```



따라서

- CRC
- Error Detection
- FEC
- Retransmission
- Packet Sequence Management

등의 방법을 고려할 수 있다.

---



## 40. Latency

Closed-loop System에서는 Data가 정확한 것뿐 아니라 **제때 도착하는 것**도 중요하다.

전체 Latency는 개념적으로



```text
Sensing Delay
+
ADC Delay
+
Processing Delay
+
Communication Delay
+
Decision Delay
+
Stimulation Delay
```



로 구성될 수 있다.

따라서



```text
Reliability ↑
```



만을 위해 지나치게 복잡한 Processing이나 Retransmission을 사용하면 Latency가 증가할 수 있다.

---



## 41. Reliability와 Latency의 Trade-off

강한 Error Correction 또는 Retransmission을 사용하면 Communication Reliability를 높일 수 있다.

하지만



```text
More Coding
More Processing
Retransmission
      ↓
Latency ↑
Energy ↑
```



가 될 수 있다.

따라서 Application에 따라 적절한 균형이 필요하다.

---



## 42. Living Hybrid Chip으로 확장

여기서 한 단계 더 나아가면 Living Cell과 Semiconductor Electronic System이 하나의 Platform 안에서 상호작용하는 **Living Hybrid Chip** 개념으로 연결할 수 있다.

예를 들어



```text
Living Neural Network
        ↕
Microelectrode Array
        ↕
Analog Front-End
        ↕
ADC / DAC
        ↕
Digital Processor
        ↕
Communication Interface
        ↕
External AI / Computer
```



와 같은 구조를 생각할 수 있다.

여기서는 Biological System이 단순한 Sensor가 아니라 **Signal을 생성하고 처리하는 살아있는 Network 자체**가 될 수 있다는 점이 흥미롭다.

---



## 43. Living Neural Network와 Electronic System

기존 Computer System에서는 모든 정보처리가 Silicon Circuit 안에서 이루어진다.



```text
Sensor
 ↓
Silicon Processor
 ↓
Output
```



하지만 Living Hybrid System에서는



```text
Biological Neural Network
           ↕
     Electronic Interface
           ↕
     Digital Computer
```



와 같이 Biological Processing과 Electronic Processing이 직접 연결될 수 있다.

이를 위해서는 단순한 Communication Theory뿐만 아니라

- Electrophysiology
- Microelectrode Array
- Analog Circuit Design
- ADC / DAC
- Signal Processing
- Machine Learning
- Embedded System
- Communication
- Microfabrication

등의 지식이 서로 연결된다.

---



## 44. Microelectrode Array

Living Neural Network와 Electronic Chip 사이의 대표적인 Interface가 **MEA(Microelectrode Array)**이다.

개념적으로 여러 개의 작은 Electrode를 배열하여



```text
Neuron
Neuron
Neuron
   ↓
┌─┬─┬─┬─┐
│●│●│●│●│
├─┼─┼─┼─┤
│●│●│●│●│
└─┴─┴─┴─┘
Microelectrode Array
```



와 같이 여러 위치에서 Electrical Activity를 기록하거나 자극할 수 있다.

MEA Channel 수가 증가하면 공간적으로 더 많은 정보를 얻을 수 있지만 Data Rate 역시 증가한다.

---



## 45. Closed-loop Living Hybrid System

Living Hybrid Chip을 Closed-loop System으로 생각하면 다음과 같은 구조가 가능하다.



```text
Living Neural Network
        ↓
Electrical Recording
        ↓
AFE
        ↓
ADC
        ↓
Signal Processing / AI
        ↓
Decision
        ↓
DAC / Stimulator
        ↓
Electrical Stimulation
        ↓
Living Neural Network
```



즉



```text
Biological System
        ↕
Electronic System
```



이 계속 정보를 주고받는 구조이다.

이 경우 Communication은 단순한 Wireless Link만을 의미하는 것이 아니라

> **Biological Domain과 Electronic Domain 사이에서 Information을 전달하는 전체 Interface**

라는 더 넓은 의미로 볼 수 있다.

---



## 46. 지금까지 공부한 개념이 어디에 연결되는가?

지금까지 `Digital Communication & Signal Systems`에서 공부한 내용을 Living Hybrid / Implant System과 연결하면 다음과 같다.

| 공부한 개념 | Bio/Implant에서의 연결 |
| --- | --- |
| Information vs Signal | Neural Information ↔ Electrical Signal |
| Sampling | Neural Signal ADC |
| Quantization | ADC Digital Representation |
| Bit Stream | Sensor Data |
| Symbol | Wireless Transmission Symbol |
| Modulation | Implant Wireless Link |
| Channel | Tissue / Air / Inductive Link |
| Noise | Electrode + Electronic + Channel Noise |
| SNR | Neural Recording 및 Wireless Quality |
| BER | Wireless Data Reliability |
| Source Coding | Bio Signal Compression |
| Channel Coding | Wireless Error Correction |
| Multiplexing | Multi-electrode Channel Sharing |
| Multiple Access | Multiple Implant / Sensor Communication |
| Spread Spectrum | Interference-resistant Wireless Link |
| Synchronization | Sampling / Symbol / Frame Timing |
| ADC / DAC | Recording / Stimulation Interface |
| RF Front-End | Implant Wireless Transceiver |
| DSP / FPGA / MCU | Local Signal Processing |

즉 지금까지 배운 각 개념은 모두 Bioelectronic System 안에서 실제 역할을 가진다.

---



## 47. Bio/Implant Communication의 핵심 Trade-off

Bio/Implant Communication에서는 다음 목표들을 동시에 만족시키고 싶다.



```text
Data Rate ↑
Channel Count ↑
Signal Quality ↑
Reliability ↑

Power ↓
Heat ↓
Size ↓
Latency ↓
Hardware Complexity ↓
```



하지만 이들을 동시에 최대로 만들기는 어렵다.

예를 들어



```text
Channel Count ↑
      ↓
Information ↑
      ↓
Data Rate ↑
      ↓
ADC / DSP Load ↑
      ↓
Communication Power ↑
```



가 된다.

또한



```text
Higher-order Modulation
      ↓
Spectral Efficiency ↑
      ↓
Required SNR / Circuit Precision ↑
```



가 될 수 있다.

또



```text
Strong Channel Coding
      ↓
Reliability ↑
      ↓
Redundant Data ↑
Processing ↑
Latency ↑
```



가 될 수 있다.

따라서 핵심은 **Application에 맞는 최적의 System Trade-off를 찾는 것**이다.

---



## 48. 시스템 전체를 하나의 흐름으로 보기

Neural Implant를 기준으로 지금까지의 내용을 하나로 연결하면 다음과 같다.



```text
Biological Information
        ↓
Neural Activity
        ↓
Electrode
        ↓
Analog Front-End
        ↓
Amplification / Filtering
        ↓
ADC
        ↓
Digital Samples
        ↓
Local Signal Processing
        ↓
Compression / Feature Extraction
        ↓
Channel Coding
        ↓
Symbol Mapping
        ↓
Modulation
        ↓
RF / Inductive / Other Physical Link
        ↓
Biological / Wireless Channel
        ↓
External Receiver
        ↓
Filtering
        ↓
Demodulation
        ↓
Detection
        ↓
Channel Decoding
        ↓
Recovered Data
        ↓
Signal Processing / AI
        ↓
Clinical / Research Information
```



Closed-loop System에서는 여기서 다시



```text
AI / Controller
      ↓
Command
      ↓
Implant Receiver
      ↓
Stimulator
      ↓
Electrode
      ↓
Biological System
```



으로 연결된다.

---



## 49. 결국 필요한 것은 통합 설계이다

Bio/Implant System에서는 하나의 Block만 잘 만드는 것으로 충분하지 않다.

예를 들어 ADC만 매우 높은 성능으로 만들더라도



```text
ADC Power ↑
      ↓
Heat ↑
      ↓
Implant Safety 문제
```



가 생길 수 있다.

반대로 Data를 지나치게 많이 압축하면



```text
Data Rate ↓
Communication Power ↓

하지만

Information Loss ↑
```



가 될 수 있다.

따라서 다음 요소를 동시에 고려해야 한다.



```text
Biology
+
Electrode
+
Analog Circuit
+
ADC / DAC
+
Digital Signal Processing
+
Communication
+
Power Management
+
Packaging
+
System Safety
```



이것이 Bioelectronic System Design의 핵심이라고 볼 수 있다.

---



## 50. 이 시리즈 전체를 다시 연결하면

이 시리즈에서 공부한 내용은 다음 순서로 연결된다.



```text
① Digital Communication 전체 구조

Information
→ Signal
→ Channel
→ Receiver

        ↓

② Analog Information → Digital Signal

Sampling
Quantization
Bit Stream
Baseband

        ↓

③ Digital Modulation

Bit
→ Symbol
→ M-ary
→ Waveform
→ QAM

        ↓

④ Channel

Noise
SNR
BER
Shannon Capacity

        ↓

⑤ Resource Sharing

Multiplexing
Multiple Access

        ↓

⑥ Spread Spectrum

DSSS
FHSS
THSS

        ↓

⑦ Communication Hardware

ADC / DAC
Mixer
PLL
LNA
PA
RF Front-End
DSP / FPGA

        ↓

⑧ Bio / Implant Communication

Electrode
AFE
ADC
Processing
Wireless Link
Tissue Channel
Closed-loop System

        ↓

Living Hybrid System
```



따라서 Digital Communication을 공부한다는 것은 단순히 Wireless Network를 공부하는 것이 아니라

> **어떤 Information을 Physical World에서 측정하고, Digital Data로 표현하고, 제한된 Channel을 통해 전달하고, 다시 Information으로 복원하는 전체 System을 이해하는 것**

이라고 볼 수 있다.

---



## 생각해볼 질문

1. Neural Signal은 Communication System에서 Information인가 Signal인가?
2. Electrode는 Biological Domain과 Electronic Domain 사이에서 어떤 역할을 하는가?
3. Neural Channel 수가 증가하면 왜 Communication 문제가 커지는가?
4. Sampling Rate를 무조건 높이면 왜 좋은 것이 아닌가?
5. ADC Resolution을 높이면 어떤 장점과 단점이 생기는가?
6. Raw Neural Data를 모두 Wireless로 보내는 것이 항상 좋은 방법인가?
7. Implant 내부에서 Data Compression을 하면 왜 Communication Energy를 줄일 수 있는가?
8. Local Processing Energy와 Wireless Communication Energy 사이에는 어떤 Trade-off가 존재하는가?
9. Tissue가 Communication Channel이 되면 일반 Free-space Channel과 무엇이 달라지는가?
10. Implant에서 고차 QAM을 무조건 사용하는 것이 좋은 선택이 아닌 이유는 무엇인가?
11. Closed-loop Neural Interface에서 Latency가 중요한 이유는 무엇인가?
12. Recording과 Stimulation에서 ADC와 DAC는 각각 어떤 역할을 하는가?
13. 여러 Electrode Channel을 하나의 ADC로 처리하려면 어떤 Multiplexing 개념을 적용할 수 있는가?
14. Living Neural Network와 Silicon Processor를 연결하려면 어떤 Engineering 분야들이 필요한가?
15. Bio/Implant System에서는 Data Rate, Power, Heat, Size, Reliability 중 무엇을 우선해야 하는가?

---



## 핵심 정리

Bio/Implant Communication의 기본 구조는 다음과 같다.



```text
Biological Signal
       ↓
Electrode
       ↓
Analog Front-End
       ↓
ADC
       ↓
Digital Processing
       ↓
Encoding
       ↓
Modulation
       ↓
Communication Channel
       ↓
Receiver
       ↓
Signal Processing
       ↓
Recovered Biological Information
```



Closed-loop System에서는 여기에 Stimulation Path가 추가된다.



```text
Biological Signal
      ↓
Recording
      ↓
Processing
      ↓
Decision
      ↓
Stimulation
      ↓
Biological System
```



따라서 Bio/Implant System에서도 지금까지 공부한



```text
Sampling
Quantization
Bit
Symbol
Modulation
Channel
SNR
BER
Coding
Multiplexing
Synchronization
ADC / DAC
RF Hardware
```



등의 개념이 그대로 사용된다.

차이점은 Bio/Implant 환경에서는



```text
Power
Heat
Size
Safety
Biocompatibility
Data Rate
Reliability
Latency
```



와 같은 제약이 훨씬 중요하다는 것이다.

---



## 한 문장으로 정리

Bio/Implant Communication은 **생체에서 발생한 정보를 Electrode와 Analog Front-End를 통해 측정하고 ADC와 Digital Processing으로 Bit Data로 변환한 뒤, 제한된 Power·Size·Heat·Safety 조건 안에서 Physical/Biological Channel을 통해 외부 시스템으로 전달하고, 필요하면 다시 Stimulation을 통해 생체 시스템과 Closed-loop를 구성하는 통합 Communication System**이다.

---

이전: [QPSK 수신기 완전 정리: Correlator·Matched Filter부터 Bandpass/Baseband 복조와 OQPSK까지](../../theory/modulation/qpsk-receivers-and-oqpsk.md)
