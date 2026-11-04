# 통신 시스템의 하드웨어 설계

[TOC]

## 통신 시스템의 하드웨어 설계: RF Front-End부터 ADC와 DSP까지

앞에서는 Digital Communication System을 다음과 같은 기능적 Block Diagram으로 살펴보았다.



```text
Information
    ↓
Encoding
    ↓
Modulation
    ↓
Channel
    ↓
Demodulation
    ↓
Detection
    ↓
Recovered Information
```



하지만 실제 통신 장치를 만들려면 이러한 기능들을 **물리적인 Hardware Circuit**으로 구현해야 한다.

예를 들어



```text
Modulation
Filtering
Amplification
Frequency Conversion
ADC / DAC
Signal Processing
```



등은 실제로

- Amplifier
- Filter
- Mixer
- Oscillator
- ADC
- DAC
- DSP
- FPGA
- RF IC

등의 Hardware로 구현된다.

따라서 이번 글에서는 앞에서 배운 Digital Communication의 기능적 Block들이 실제 송수신기 Hardware에서는 어떻게 구성되는지 정리한다.

---

## 1. 통신 시스템의 Hardware 구조

실제 Wireless Communication System을 크게 나누면 다음과 같다.



```text
Digital Baseband
      ↓
RF Transmitter
      ↓
Antenna
      ↓
Wireless Channel
      ↓
Antenna
      ↓
RF Receiver
      ↓
Digital Baseband
```



조금 더 자세하게 보면



```text
Digital Data
     ↓
Baseband Processing
     ↓
DAC
     ↓
Filter
     ↓
Mixer / Up-conversion
     ↓
Power Amplifier
     ↓
Antenna
     ↓
=================
 Wireless Channel
=================
     ↓
Antenna
     ↓
RF Filter
     ↓
LNA
     ↓
Mixer / Down-conversion
     ↓
Filter
     ↓
ADC
     ↓
Digital Signal Processing
     ↓
Recovered Data
```



와 같은 구조가 된다.

---



## 2. Digital Domain과 Analog/RF Domain

통신 Hardware를 이해할 때 가장 먼저 구분해야 하는 것은 **Digital Domain**과 **Analog/RF Domain**이다.



```text
Digital Domain
     ↓
DAC
     ↓
Analog Domain
     ↓
RF Domain
```



Receiver에서는 반대로



```text
RF Domain
     ↓
Analog Domain
     ↓
ADC
     ↓
Digital Domain
```



으로 이동한다.

---



### Digital Domain

Digital Domain에서는 Data를



```text
Bit
Symbol
Sample
```



등의 숫자로 표현한다.

주로

- CPU
- DSP
- FPGA
- MCU
- ASIC

등을 이용하여 처리한다.

여기에서는



```text
Channel Coding
Symbol Mapping
Digital Filtering
Synchronization
Detection
FFT
Equalization
```



등의 연산을 수행할 수 있다.

---

### Analog Domain

Analog Domain에서는 실제 시간에 따라 연속적으로 변화하는 전압 또는 전류를 다룬다.

대표적인 회로는



```text
Amplifier
Analog Filter
ADC / DAC
```



등이다.

---



### RF Domain

RF(Radio Frequency) Domain은 실제 Wireless Transmission에 사용되는 높은 Frequency 영역이다.

예를 들어



```text
Bluetooth
Wi-Fi
Cellular
GPS
```



등은 서로 다른 RF Band를 사용한다.

RF 영역에서는

- Amplification
- Filtering
- Frequency Conversion
- Impedance Matching
- Antenna

등의 문제가 중요해진다.

---



## 3. 송신기(Transmitter)의 Hardware 구조

송신기의 기본적인 목적은

> **Digital Data를 실제 Antenna를 통해 방사할 수 있는 RF Signal로 변환하는 것**

이다.

전체적인 Hardware Chain은 다음과 같다.



```text
Digital Data
     ↓
Baseband Processor
     ↓
DAC
     ↓
Low-Pass Filter
     ↓
Up-conversion
     ↓
RF Filter
     ↓
Power Amplifier
     ↓
Antenna
```



---



## 4. Baseband Processor

Baseband Processor는 Digital Communication의 핵심적인 Signal Processing을 수행한다.

대표적으로



```text
Source / Channel Processing
       ↓
Symbol Mapping
       ↓
Pulse Shaping
       ↓
I/Q Signal Generation
```



등을 처리한다.

예를 들어 QPSK에서는 입력 Bit를 2 bit씩 묶는다.



```text
00
01
10
11
```



그리고 각각을 하나의 Symbol로 Mapping한다.



```text
Bits
 ↓
Symbol Mapper
 ↓
I/Q Samples
```



이 결과는 아직 Antenna를 통해 보낼 수 있는 RF Signal이 아니라 **Digital Baseband Sample**이다.

---



## 5. I/Q Signal

Modern Digital Modulation에서는 Signal을 보통



```text
I = In-phase
Q = Quadrature
```



두 성분으로 표현한다.

복소 Baseband Signal은



$$
x(t)=I(t)+jQ(t)
$$



와 같이 표현할 수 있다.

RF Signal은 이를 Carrier에 실어



$$
s(t) = I(t)\cos(2\pi f_ct) - Q(t)\sin(2\pi f_ct)
$$



와 같이 만들 수 있다.

여기서

- $I(t)$ : In-phase Component
- $Q(t)$ : Quadrature Component
- $f_c$ : Carrier Frequency

이다.

즉 QAM이나 QPSK의 Constellation Point는 실제 Hardware에서 **I와 Q 두 Signal Path**로 구현된다.

---



## 6. DAC

Digital Baseband Processor에서 생성한 Sample은 숫자이기 때문에 바로 Analog Circuit에 전달할 수 없다.

따라서 **DAC(Digital-to-Analog Converter)**가 필요하다.



```text
Digital Samples
      ↓
DAC
      ↓
Analog Waveform
```



DAC는 Digital Number를 대응되는 Analog Voltage 또는 Current로 변환한다.

예를 들어



```text
0000
0100
1000
1111
```



과 같은 Digital Code를 서로 다른 Voltage Level로 변환할 수 있다.

---



## 7. Reconstruction Filter

DAC의 출력은 이상적인 부드러운 Analog Waveform이 아니다.

Digital Sample을 Analog로 변환하는 과정에서 고주파 Image Component가 나타날 수 있다.

따라서 DAC 이후에는 일반적으로 **Reconstruction Filter**가 사용된다.



```text
DAC Output
    ↓
Low-Pass Filter
    ↓
Smooth Baseband Signal
```



주로 Low-Pass Filter를 이용하여 원하는 Baseband Component만 남긴다.

---



## 8. Up-conversion

Baseband Signal을 Antenna에서 직접 방사하기에는 Frequency가 너무 낮을 수 있다.

따라서 Carrier Frequency 영역으로 Signal을 이동시켜야 한다.

이를 **Up-conversion**이라고 한다.



```text
Baseband Signal
       +
Local Oscillator
       ↓
Mixer
       ↓
RF Signal
```



Frequency Domain에서 보면



```text
Baseband

      ↓ Up-conversion

Carrier Frequency fc
        중심으로 이동
```



하는 과정이다.

---



## 9. Mixer

Mixer는 두 Signal을 곱하여 새로운 Frequency Component를 만드는 회로이다.

두 입력 Frequency가



$$
f_1,\quad f_2
$$



라고 하면 Mixer Output에는



$$
f_1+f_2
$$



와



$$
|f_1-f_2|
$$



성분이 나타난다.

예를 들어 Baseband Signal과 Local Oscillator를 Mixer에 입력하면 원하는 RF Frequency로 Signal을 이동시킬 수 있다.



```text
Baseband
    +
LO
    ↓
Mixer
    ↓
RF Band
```



Receiver에서는 반대로 RF Signal을 낮은 Frequency 영역으로 이동시키는 데 사용한다.

---



## 10. Local Oscillator

Mixer가 Frequency Conversion을 하기 위해서는 기준이 되는 Sinusoidal Signal이 필요하다.

이 Signal을 만드는 회로가 **Local Oscillator(LO)**이다.



```text
Oscillator
    ↓
Local Oscillator Signal
    ↓
Mixer
```



실제 통신장치에서는 원하는 Frequency를 정밀하게 만들기 위해



```text
Crystal Oscillator
PLL
Frequency Synthesizer
```



등을 사용한다.

---



## 11. PLL

PLL은 **Phase-Locked Loop**의 약자이다.

Reference Signal을 기준으로 원하는 Frequency의 안정적인 Signal을 생성하거나 추적하는 데 사용한다.

개념적으로는



```text
Reference Clock
      ↓
     PLL
      ↓
Stable Carrier Frequency
```



이다.

통신 시스템에서는

- Carrier Generation
- Frequency Synthesis
- Carrier Recovery
- Clock Recovery

등에서 매우 중요하다.

FHSS처럼 Carrier Frequency를 계속 바꾸는 시스템에서도 Frequency Synthesizer와 PLL의 성능이 중요하다.

---



## 12. RF Filter

Up-conversion 이후에는 원하는 RF Signal 외에도 불필요한 Frequency Component가 나타날 수 있다.

따라서 RF Filter를 이용하여 원하는 Frequency Band만 통과시킨다.



```text
Mixer Output
      ↓
RF Band-Pass Filter
      ↓
Desired RF Signal
```



Filter는

- Out-of-band Signal
- Mixer Image
- Harmonic
- Interference

등을 줄이는 데 사용한다.

---



## 13. Power Amplifier

Antenna를 통해 Signal을 충분한 거리까지 전달하려면 Signal Power를 높여야 한다.

이를 담당하는 회로가 **Power Amplifier(PA)**이다.



```text
Small RF Signal
      ↓
Power Amplifier
      ↓
High-Power RF Signal
      ↓
Antenna
```



PA는 Transmitter에서 매우 많은 Power를 소비할 수 있는 Block 중 하나이다.

특히 Battery-powered Device나 Implantable Device에서는 매우 중요한 설계 대상이다.

---



## 14. Power Amplifier와 Linearity

PA에서는 단순히 Signal을 크게 만드는 것만 중요한 것이 아니다.

Input과 Output 사이의 관계가 가능한 한 선형적으로 유지되어야 한다.



```text
Input
  ↓
Amplifier
  ↓
Output
```



Ideal한 경우



$$
y(t)=Gx(t)
$$



처럼 동작한다.

하지만 Amplifier가 Saturation 영역에 가까워지면 Non-linearity가 발생한다.



```text
Input ↑↑

      ↓

Output Saturation
```



이 경우 Signal의 Amplitude와 Phase가 왜곡될 수 있다.

---



## 15. QAM과 PA Linearity

QAM은 Amplitude와 Phase에 모두 정보를 담는다.

따라서 Amplifier의 Non-linearity가 발생하면 Constellation Point가 변형될 수 있다.



```text
Ideal

•   •   •   •

•   •   •   •

        ↓

Nonlinear PA

 •  •     •
    •  •
 •      •
```



따라서 고차 QAM일수록 PA의 Linearity 요구조건이 더 까다로워진다.

이것은 앞에서 배운



```text
Higher-order Modulation
→ Spectral Efficiency ↑
→ Hardware Requirement ↑
```



라는 Trade-off와 연결된다.

---



## 16. Antenna

Antenna는 Electrical Signal과 Electromagnetic Wave 사이를 변환하는 장치이다.

Transmitter에서는



```text
Electrical RF Signal
        ↓
Antenna
        ↓
Electromagnetic Wave
```



Receiver에서는 반대로



```text
Electromagnetic Wave
        ↓
Antenna
        ↓
Electrical RF Signal
```



이 된다.

즉 Antenna는 Circuit과 Wireless Channel의 경계라고 볼 수 있다.

---



## 17. Receiver의 Hardware 구조

Receiver의 목적은 매우 작은 RF Signal을 받아 Digital Data로 복원하는 것이다.

전체 구조는 다음과 같다.



```text
Antenna
   ↓
RF Filter
   ↓
LNA
   ↓
Mixer
   ↓
IF / Baseband Filter
   ↓
ADC
   ↓
Digital Signal Processing
```



---



## 18. RF Filter

Antenna에는 원하는 Signal만 들어오는 것이 아니다.

다양한 Frequency의 Signal과 Interference가 동시에 들어온다.



```text
Desired Signal
Wi-Fi
Bluetooth
Cellular
Noise
Interference
      ↓
Antenna
```



따라서 Receiver Front-End에서는 먼저 원하는 Frequency Band를 선택해야 한다.



```text
Antenna
   ↓
RF Band-Pass Filter
   ↓
Desired Frequency Band
```



---



## 19. LNA

LNA는 **Low-Noise Amplifier**의 약자이다.

Antenna에서 들어온 Signal은 매우 작을 수 있다.

따라서 Signal Processing 전에 Amplification이 필요하다.



```text
Weak RF Signal
      ↓
LNA
      ↓
Amplified RF Signal
```



그러나 Amplifier 자체도 Noise를 추가한다.

따라서 Receiver의 첫 Amplifier에서는

> **Signal은 크게 증폭하면서 추가 Noise는 최대한 작게 만드는 것**

이 중요하다.

이 때문에 Low-Noise Amplifier라고 부른다.

---



## 20. Noise Figure

Receiver Circuit이 얼마나 많은 Noise를 추가하는지를 나타내는 대표적인 지표가 **Noise Figure(NF)**이다.

개념적으로



$$
NF = \frac{SNR_{in}}{SNR_{out}}
$$



의 비율과 관련된다.

dB로 표현하면



$$
NF_{dB} = 10\log_{10} \left( \frac{SNR_{in}}{SNR_{out}} \right)
$$



이다.

Receiver Circuit은 일반적으로 Noise를 추가하므로



```text
SNRout < SNRin
```



이다.

따라서 Noise Figure가 작을수록 좋은 Receiver라고 볼 수 있다.

---



## 21. 왜 LNA가 앞에 위치하는가?

Receiver Chain에서 첫 번째 Amplifier의 Noise Performance는 전체 Receiver 성능에 큰 영향을 준다.



```text
Antenna
 ↓
LNA
 ↓
Mixer
 ↓
ADC
```



Antenna에서 들어온 매우 작은 Signal을 초기에 충분히 증폭하면 이후 Block에서 발생하는 Noise의 상대적인 영향을 줄일 수 있다.

따라서 LNA는 Receiver Front-End에서 매우 중요한 Block이다.

---



## 22. Down-conversion

Receiver에 들어오는 RF Signal은 매우 높은 Frequency를 가질 수 있다.

이를 그대로 ADC와 DSP로 처리하기에는 어려움이 있다.

따라서 Mixer를 이용하여 Frequency를 낮춘다.



```text
RF Signal
    +
Local Oscillator
    ↓
Mixer
    ↓
IF or Baseband Signal
```



이를 **Down-conversion**이라고 한다.

---



## 23. IF란?

IF는 **Intermediate Frequency**의 약자이다.

RF Signal을 한 번에 Baseband까지 내리지 않고 중간 Frequency로 변환할 수도 있다.



```text
RF
 ↓
IF
 ↓
Baseband
```



예를 들어



```text
2.4 GHz RF
     ↓
Mixer
     ↓
10.7 MHz IF
     ↓
Mixer
     ↓
Baseband
```



와 같은 구조를 만들 수 있다.

---



## 24. Superheterodyne Receiver

RF Signal을 먼저 IF로 변환한 뒤 Baseband로 변환하는 대표적인 구조를 **Superheterodyne Receiver**라고 한다.



```text
Antenna
   ↓
RF Filter
   ↓
LNA
   ↓
Mixer
   ↓
IF
   ↓
IF Filter
   ↓
Mixer
   ↓
Baseband
```



장점은 Frequency Selectivity와 Gain을 여러 Stage로 나누어 구현하기 쉽다는 것이다.

반면

- Mixer
- LO
- IF Filter

등의 Hardware가 추가되므로 구조가 복잡해질 수 있다.

---



## 25. Direct Conversion Receiver

RF Signal을 중간 IF 없이 바로 Baseband로 내리는 방식을 **Direct Conversion Receiver** 또는 Zero-IF Receiver라고 한다.



```text
RF
 ↓
Mixer
 ↓
I / Q Baseband
```



구조가 단순해지고 Integration하기 쉬워 현대 RF IC에서 많이 사용된다.

하지만

- DC Offset
- I/Q Imbalance
- LO Leakage
- Flicker Noise

등의 문제를 고려해야 한다.

---



## 26. ADC

Analog Receiver에서 처리된 Signal을 Digital Signal Processing으로 넘기기 위해서는 ADC가 필요하다.

**ADC(Analog-to-Digital Converter)**는



```text
Analog Signal
      ↓
Sampling
      ↓
Quantization
      ↓
Digital Samples
```



과정을 수행한다.

---



## 27. Sampling Rate

ADC는 Analog Signal을 일정한 시간 간격으로 측정한다.

Sampling Frequency를



$$
f_s
$$



라고 하면 Sampling Period는



$$
T_s=\frac{1}{f_s}
$$



이다.

Band-limited Signal을 복원하기 위한 기본적인 Sampling 조건은



$$
f_s > 2B
$$



이다.

여기서 $B$는 Signal Bandwidth이다.

이를 **Nyquist Sampling Criterion**이라고 한다.

실제 시스템에서는 Filter 특성 등을 고려하여 Nyquist Rate보다 높은 Sampling Rate를 사용하는 경우가 많다.

---



## 28. ADC Resolution

ADC가 Analog Amplitude를 얼마나 세밀하게 표현할 수 있는지를 나타내는 것이 **Resolution**이다.

$n$ bit ADC라면



$$
2^n
$$



개의 Level을 표현할 수 있다.

예를 들어



```text
8-bit ADC
→ 256 Levels

10-bit ADC
→ 1024 Levels

12-bit ADC
→ 4096 Levels

16-bit ADC
→ 65536 Levels
```



이다.

Resolution이 높을수록 더 작은 Signal Difference를 표현할 수 있다.

---



## 29. Sampling Rate와 Resolution의 Trade-off

ADC의 성능을 높이고 싶다면



```text
Sampling Rate ↑
Resolution ↑
```



가 이상적이다.

하지만 일반적으로



```text
Sampling Rate ↑
Resolution ↑
        ↓
Power Consumption ↑
Circuit Complexity ↑
Data Rate ↑
```



라는 비용이 발생한다.

특히 Battery-powered Device나 Implantable Device에서는 매우 중요한 Trade-off이다.

---



## 30. ADC 이후의 Digital Signal Processing

ADC를 통과하면 Signal을 다시 숫자로 처리할 수 있다.



```text
ADC Samples
     ↓
Digital Filtering
     ↓
Synchronization
     ↓
Demodulation
     ↓
Detection
     ↓
Channel Decoding
     ↓
Recovered Bits
```



이 영역은

- DSP
- FPGA
- CPU
- ASIC

등으로 구현할 수 있다.

---



## 31. DSP

DSP는 **Digital Signal Processor**의 약자이다.

Signal Processing에 필요한 반복적인 Multiply-Accumulate Operation을 빠르게 수행하도록 설계된 Processor이다.

통신 시스템에서는



```text
Filtering
FFT
Equalization
Synchronization
Demodulation
Detection
```



등에 사용할 수 있다.

---



## 32. FPGA

FPGA는 **Field Programmable Gate Array**이다.

Software처럼 재구성할 수 있지만 내부적으로는 Parallel Hardware Logic을 구성한다.

따라서



```text
High Throughput
Parallel Processing
Low Latency
```



가 필요한 통신 Signal Processing에 적합하다.

예를 들어



```text
FFT
FIR Filter
OFDM
MIMO Processing
High-speed Interface
```



등에 활용할 수 있다.

---



## 33. DSP와 FPGA 비교

| 구분 | DSP | FPGA |
| --- | --- | --- |
| 구조 | Processor 기반 | Programmable Logic |
| 처리 방식 | 명령어 순차 실행 중심 | Parallel Hardware Processing |
| 개발 난이도 | 비교적 낮음 | 비교적 높음 |
| 유연성 | Software 수정 용이 | Hardware 구조 재구성 가능 |
| Latency | 일반적으로 FPGA보다 큼 | 매우 낮게 구현 가능 |
| 병렬 처리 | 제한적 | 매우 강함 |
| 대표 용도 | 일반 DSP Algorithm | 고속 PHY Processing |

실제 Communication System에서는 DSP와 FPGA를 함께 사용하기도 한다.

---



## 34. MCU는 어디에 사용되는가?

모든 Signal Processing을 고성능 DSP나 FPGA가 담당할 필요는 없다.

MCU(Microcontroller)는 주로

- Device Control
- Protocol Management
- Sensor Interface
- Power Management
- Configuration
- Low-rate Signal Processing

등에 사용될 수 있다.



```text
RF / DSP Hardware
       ↕
      MCU
       ↓
System Control
```



앞에서 공부한 ATmega128A 같은 Microcontroller도 저속 Communication Interface와 System Control에서 이러한 역할을 수행할 수 있다.

---



## 35. 전체 Transceiver 구조

송신기와 수신기를 하나로 합치면 **Transceiver**가 된다.

전체 구조는 다음과 같이 정리할 수 있다.



```text
                 TRANSMITTER

Digital Bits
     ↓
Channel Coding
     ↓
Symbol Mapping
     ↓
Pulse Shaping
     ↓
DAC
     ↓
Baseband Filter
     ↓
I/Q Mixer ← Local Oscillator
     ↓
RF Filter
     ↓
Power Amplifier
     ↓
Antenna

==================================
          Physical Channel
==================================

Antenna
     ↓
RF Filter
     ↓
LNA
     ↓
I/Q Mixer ← Local Oscillator
     ↓
Baseband Filter
     ↓
ADC
     ↓
Synchronization
     ↓
Detection / Demodulation
     ↓
Channel Decoding
     ↓
Recovered Bits

                  RECEIVER
```



---



## 36. RF Front-End란?

통신 장비에서 **RF Front-End**라는 표현을 자주 사용한다.

일반적으로 Antenna와 Digital Baseband 사이에서 RF Signal을 직접 처리하는 Analog/RF Circuit들을 의미한다.

Receiver를 예로 들면



```text
Antenna
 ↓
RF Filter
 ↓
LNA
 ↓
Mixer
 ↓
Analog Filter
```



정도를 RF Front-End라고 볼 수 있다.

Transmitter에서는



```text
Mixer
 ↓
RF Filter
 ↓
Power Amplifier
 ↓
Antenna
```



등이 포함될 수 있다.

---



## 37. Analog Front-End와 RF Front-End

두 용어는 비슷하지만 응용에 따라 구분해서 사용하기도 한다.

#### RF Front-End

Radio Frequency Signal을 직접 처리한다.



```text
Antenna
RF Filter
LNA
Mixer
PA
```



#### Analog Front-End

Sensor나 Electrode 등에서 발생한 Analog Signal을 ADC에 넣기 전에 처리한다.



```text
Sensor
 ↓
Low-Noise Amplifier
 ↓
Analog Filter
 ↓
ADC
```



이 차이는 이후 **Bio/Implant Communication**에서 매우 중요하다.

---



## 38. 통신 Hardware에서 중요한 설계 지표

실제 통신 Hardware에서는 단순히 Signal이 전달되는지만 보는 것이 아니다.

여러 성능 지표를 함께 고려해야 한다.



```text
Noise
Gain
Bandwidth
Linearity
Power
Dynamic Range
Data Rate
Latency
Chip Area
Cost
```



---



### Gain

Signal을 얼마나 증폭하는지를 나타낸다.



$$
G=\frac{P_{out}}{P_{in}}
$$



dB로 표현하면



$$
G_{dB} = 10\log_{10} \left( \frac{P_{out}}{P_{in}} \right)
$$



이다.

---



### Bandwidth

Circuit이 원하는 성능으로 처리할 수 있는 Frequency Range이다.

Bandwidth가 부족하면 Signal Spectrum의 일부가 손실되어 Distortion이 발생할 수 있다.

---



### Noise

Circuit 자체에서 발생하는 Noise가 Receiver의 SNR을 악화시킬 수 있다.

특히 LNA, Mixer, ADC 등의 Noise Performance가 중요하다.

---



### Linearity

Input Signal의 관계를 왜곡 없이 유지하는 능력이다.

고차 QAM처럼 Amplitude와 Phase를 세밀하게 구분하는 시스템에서는 매우 중요하다.

---



### Dynamic Range

매우 작은 Signal부터 큰 Signal까지 얼마나 넓은 범위를 처리할 수 있는지를 나타낸다.

Receiver에는 매우 약한 Signal과 강한 Interference가 동시에 들어올 수 있기 때문에 중요한 특성이다.

---



## 39. 통신 Hardware의 가장 중요한 Trade-off

실제 Hardware 설계에서는 거의 항상 Trade-off가 존재한다.

예를 들어



```text
Higher Gain
     ↓
Power Consumption ↑

Higher ADC Resolution
     ↓
Power / Data Rate ↑

Higher Linearity
     ↓
Power Consumption ↑

Higher Bandwidth
     ↓
Noise ↑
Power ↑

Higher Sampling Rate
     ↓
ADC / DSP Power ↑
```



즉

> **최고 성능의 회로가 항상 최고의 시스템은 아니다.**

주어진 Application의 요구조건에 맞는 Hardware를 설계하는 것이 중요하다.

---



## 40. Software Defined Radio

최근의 통신 시스템에서는 가능한 많은 기능을 Digital Processing 영역에서 구현하려는 경향이 있다.

이를 대표하는 개념이 **SDR(Software Defined Radio)**이다.

전통적인 Radio에서는



```text
Filtering
Modulation
Demodulation
Detection
```



중 많은 기능을 Analog Hardware로 구현하였다.

SDR에서는 가능한 한 빠르게 ADC를 수행한 뒤



```text
RF / IF
   ↓
ADC
   ↓
Software / DSP / FPGA
```



에서 여러 통신 기능을 처리한다.

---



## 41. SDR의 장점

Hardware를 완전히 바꾸지 않고 Software를 수정하여



```text
Modulation Scheme
Bandwidth
Channel Coding
Protocol
Filter
```



등을 변경할 수 있다.

따라서 하나의 Radio Platform에서 여러 Communication Standard를 지원할 수 있다.

---



## 42. 그렇다면 모든 것을 Digital로 만들면 되는가?

이론적으로 Digital Processing을 많이 사용하면 높은 유연성을 얻을 수 있다.

하지만 Antenna에서 바로 매우 높은 RF Frequency를 Sampling하려면



```text
Extremely High-Speed ADC
High Resolution
Huge Data Rate
High DSP Throughput
```



이 필요하다.

이 과정에서 Power Consumption이 크게 증가한다.

따라서 실제 시스템에서는



```text
RF / Analog Processing
        +
Digital Processing
```



을 적절하게 나누어 사용한다.

---



## 43. Bio/Implant Communication과 Hardware

이제 이 Hardware 구조를 Bioelectronic System에 연결할 수 있다.

예를 들어 Neural Signal을 측정한다고 하자.



```text
Neuron
  ↓
Electrode
  ↓
Analog Front-End
  ↓
ADC
  ↓
Digital Signal Processing
  ↓
Channel Coding
  ↓
Modulation
  ↓
RF / Optical / Inductive Transmitter
  ↓
Body / Free-space Channel
  ↓
External Receiver
```



여기서 특히 중요한 것이 **Analog Front-End**이다.

Neural Signal은 매우 작은 Voltage를 가지기 때문에



```text
Low-Noise Amplifier
        ↓
Filter
        ↓
ADC
```



과 같은 Signal Conditioning이 필요하다.

---



## 44. Implant System에서 왜 Power가 중요한가?

Implantable Device에서는 사용할 수 있는 Energy가 매우 제한적이다.



```text
Battery Capacity
      ↓

ADC
DSP
RF Transmitter
      ↓

Power Consumption
```



Power Consumption이 커지면

- Battery Life 감소
- Heat 발생
- Tissue Heating
- Device Size 증가

등의 문제가 생길 수 있다.

따라서 Implant Communication에서는

> **Data Rate를 무조건 높이는 것보다 필요한 정보만 효율적으로 전송하는 것**

이 더 중요할 수 있다.

---



## 45. 데이터 압축과 Hardware Power의 관계

예를 들어 Sensor에서



```text
Raw Data
100 Mbps
```



가 발생한다고 하자.

이를 그대로 Wireless로 전송하면 높은 RF Transmission Power가 필요할 수 있다.

반대로 Chip 내부에서 Processing을 수행하여



```text
Raw Data
   ↓
Feature Extraction
   ↓
Compression
   ↓
1 Mbps
```



로 줄이면 Wireless Transmission Energy를 크게 낮출 가능성이 있다.

하지만 Local Processing에도 Energy가 필요하다.

따라서



```text
Local Computation Energy
        vs
Communication Energy
```



사이의 Trade-off를 고려해야 한다.

이 문제는 Bio/Implant Communication에서 매우 중요한 설계 문제이다.

---



## 46. 통신 Block Diagram과 실제 Hardware 연결

앞에서 배운 Functional Block과 Hardware를 대응시키면 다음과 같다.

| Communication Function | 실제 Hardware / 구현 |
| --- | --- |
| Source / Channel Coding | CPU, DSP, FPGA, ASIC |
| Symbol Mapping | DSP, FPGA, ASIC |
| Pulse Shaping | Digital Filter, DAC |
| Bandpass Modulation | I/Q Mixer, LO |
| Frequency Generation | PLL, Frequency Synthesizer |
| Transmission | PA, Antenna |
| Channel | Cable, Air, Fiber, Tissue |
| Frequency Selection | RF/BPF Filter |
| Weak Signal Amplification | LNA |
| Down-conversion | Mixer, LO |
| Sampling | ADC |
| Demodulation | DSP, FPGA, Analog/Digital Circuit |
| Detection | DSP, FPGA, ASIC |
| Synchronization | PLL, Timing Recovery Circuit, DSP |

즉 앞에서 배운 Digital Communication Block들은 실제로 모두 특정한 **Circuit 또는 Computing Hardware**로 구현된다.

---



## 47. 전체 흐름

최종적으로 Communication Hardware를 하나의 흐름으로 정리하면 다음과 같다.



```text
              DIGITAL DOMAIN

Information Bits
       ↓
Coding
       ↓
Symbol Mapping
       ↓
Digital Baseband Samples
       ↓

────────────────────────────

              ANALOG DOMAIN

DAC
 ↓
Filter
 ↓

────────────────────────────

                RF DOMAIN

Mixer / Up-conversion
 ↓
RF Filter
 ↓
Power Amplifier
 ↓
Antenna

================================

           PHYSICAL CHANNEL

================================

Antenna
 ↓
RF Filter
 ↓
LNA
 ↓
Mixer / Down-conversion

────────────────────────────

              ANALOG DOMAIN

Filter
 ↓
ADC
 ↓

────────────────────────────

              DIGITAL DOMAIN

Digital Samples
       ↓
Synchronization
       ↓
Demodulation
       ↓
Detection
       ↓
Decoding
       ↓
Recovered Information
```



---



## 핵심 정리

| Block | 역할 |
| --- | --- |
| Baseband Processor | Coding, Mapping, Digital Signal Processing |
| DAC | Digital Sample → Analog Signal |
| ADC | Analog Signal → Digital Sample |
| Mixer | Frequency Conversion |
| Local Oscillator | Carrier / Mixing Frequency 생성 |
| PLL | Frequency / Phase 제어 |
| RF Filter | 원하는 Frequency Band 선택 |
| Power Amplifier | 송신 Signal Power 증가 |
| LNA | 약한 수신 Signal을 낮은 Noise로 증폭 |
| Antenna | Electrical Signal ↔ Electromagnetic Wave |
| DSP | Digital Signal Processing |
| FPGA | High-speed Parallel Signal Processing |
| MCU | System Control 및 저속 Processing |
| RF Front-End | Antenna 주변의 RF Processing Circuit |
| Analog Front-End | Sensor Signal을 ADC 전 단계에서 Conditioning |

---



## 통신 시스템을 바라보는 세 가지 영역



```text
Digital
│
│ Bit / Symbol / Sample
│ DSP / FPGA / Processor
│
▼
Analog
│
│ Continuous Voltage / Current
│ ADC / DAC / Filter
│
▼
RF
│
│ Carrier / Mixer / PA / LNA
│
▼
Antenna
│
▼
Physical Channel
```



Receiver에서는 이 순서가 반대로 진행된다.

---



## 한 문장으로 정리

실제 Digital Communication System은 **Bit와 Symbol을 처리하는 Digital Baseband, ADC·DAC와 Filter를 포함하는 Analog Circuit, Mixer·PLL·LNA·PA·Antenna로 이루어진 RF Front-End가 연결되어 동작하며, 성능·Bandwidth·Noise·Linearity·Power·Complexity 사이의 Trade-off를 고려하여 전체 Hardware Architecture를 설계하는 것이 핵심**이다.

---

블록도 기준: [10. XMT](../../blocks/transmitter.md) · [전체 로드맵](../../roadmap.md)
