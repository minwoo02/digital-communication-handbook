# 아날로그 정보가 디지털 전송 파형이 되기까지

[TOC]

디지털 통신 시스템에서 우리가 보내고 싶은 정보는 처음부터 `0`과 `1`의 형태로 존재하는 것이 아니다.

음성이나 생체 신호처럼 시간에 따라 연속적으로 변화하는 **아날로그 정보(Analog Information)**도 있고, 문자처럼 이미 이산적인 형태를 가진 정보도 있다.

이러한 정보가 실제 통신 채널을 통해 전달되기 위해서는 다음과 같은 과정을 거친다.



$$
\text{Information} \rightarrow \text{Formatting} \rightarrow \text{Bit Stream} \rightarrow \text{Baseband Waveform} \rightarrow \text{Transmission}
$$



특히 아날로그 정보라면



$$
\boxed{ \text{Analog Signal} \rightarrow \text{Sampling} \rightarrow \text{Quantization} \rightarrow \text{Encoding} \rightarrow \text{Bit Stream} }
$$



과정을 거쳐 디지털 정보로 변환된다.

이번 글에서는 이 과정을 중심으로 다음 내용을 정리한다.

- Formatting과 Character Coding
- Sampling
- Nyquist Sampling Theorem
- Ideal / Natural / Sample-and-Hold Sampling
- Quantization과 PCM
- Aliasing
- Anti-Aliasing Filter
- Downsampling과 Upsampling
- Interpolation
- D/A Conversion과 Reconstruction
- Filtering과 Convolution

---



## 1. Formatting과 Baseband Modulation

디지털 통신 시스템에서 **Formatting**은 정보원을 통신 시스템에서 처리할 수 있는 디지털 표현으로 바꾸는 과정이다.

정보원의 종류에 따라 과정이 달라진다.

### 아날로그 정보

음성, 생체 신호, 온도, 센서 신호처럼 연속적으로 변하는 정보라면 다음 과정을 거친다.



$$
\text{Analog Information} \rightarrow \text{Sample} \rightarrow \text{Quantize} \rightarrow \text{Encode}
$$



즉,

1. Sampling으로 시간을 이산화하고
2. Quantization으로 진폭을 이산화하고
3. Encoding으로 이를 bit sequence로 표현한다.

---



### 문자 정보

문자는 이미 이산적인 정보이므로 Sampling과 Quantization이 필요하지 않다.

예를 들어

`HELLO`

라는 문자는 문자 코드(Character Code)를 통해 바로 bit stream으로 바뀔 수 있다.

대표적인 문자 코드에는 다음이 있다.

- ASCII
- EBCDIC
- Baudot Code

따라서 전체 흐름은 다음과 같이 볼 수 있다.



$$
\boxed{ \text{Information Source} \rightarrow \text{Formatting} \rightarrow \text{Bit Stream} \rightarrow \text{Pulse Modulation} \rightarrow \text{Waveform} }
$$



중요한 점은 **bit 자체가 물리적인 신호는 아니라는 것**이다.

`0`, `1`이라는 논리적 정보는 마지막에 전압, 전류 또는 전자기파와 같은 실제 **waveform**으로 변환되어야 한다.

---



## 2. Character Coding

문자도 결국 통신 시스템에서는 bit sequence로 표현해야 한다.

예를 들어 ASCII는 문자에 숫자를 할당하고 그 숫자를 binary로 표현한다.

표준 ASCII는 **7 bit**를 사용한다.

따라서 표현 가능한 상태의 수는



$$
2^7=128
$$



개이다.

즉,



$$
\boxed{ \text{Character} \rightarrow \text{Binary Code} \rightarrow \text{Bit Stream} }
$$



의 과정이다.

---

## 3. ASCII와 Parity Bit

7-bit ASCII에 오류 검출을 위한 bit 하나를 추가하여 8 bit 형태로 전송하는 경우가 있다.

이 추가 bit가 **Parity Bit**이다.

대표적으로 다음 두 방식이 있다.

- Even Parity
- Odd Parity

### Even Parity

Even Parity에서는 전체 bit에서 `1`의 개수가 짝수가 되도록 parity bit를 결정한다.

예를 들어 문자 `S`의 7-bit ASCII가

`1010011`

이라고 하자.

`1`의 개수는 4개로 이미 짝수이다.

따라서 Even Parity Bit는

`0`

이 된다.

따라서 parity를 포함한 표현은

`01010011`

이 된다.

반대로 `1`의 개수가 홀수라면 parity bit를 `1`로 추가하여 전체 `1`의 개수를 짝수로 만든다.

> Parity bit는 오류를 **검출**하기 위한 방법이다. 모든 오류를 검출하거나 수정할 수 있는 것은 아니다.

특히 짝수 개의 bit가 동시에 뒤집히는 오류는 단순 parity만으로 검출하지 못할 수 있다.

---



## 4. UART Frame과 Parity

UART(Universal Asynchronous Receiver/Transmitter)는 대표적인 비동기 직렬 통신 방식이다.

일반적인 UART frame은 다음과 같이 구성할 수 있다.



$$
\boxed{ \text{Start Bit} \;|\; \text{Data Bits} \;|\; \text{Parity Bit} \;|\; \text{Stop Bit} }
$$



강의 예제처럼 7-bit ASCII `S`를 전송한다고 하자.

ASCII 표현은

`1010011`

이다.

UART에서는 설정에 따라 data bit를 LSB부터 전송할 수 있다.

따라서 강의 예제의 전송 순서는

`1100101`

이 된다.

Even parity를 사용하면 `1`의 수가 이미 4개이므로 parity bit는 0이다.

즉 예제 frame은 다음과 같이 볼 수 있다.

| Start | Data | Parity | Stop |
| --- | --- | --- | --- |
| 0 | 1100101 | 0 | 1 |

즉 문자 하나도 실제 통신에서는 단순한 ASCII 7 bit만 전송되는 것이 아니라, 통신 방식에 따라 **frame 구조**를 가지고 전달될 수 있다.

---



## 5. 문자에서 M-ary Symbol로

bit stream은 이후 여러 bit씩 묶여 하나의 symbol이 될 수 있다.

하나의 symbol에 $k$ bit를 묶는다면 가능한 symbol의 수는



$$
M=2^k
$$



이다.

따라서



$$
k=\log_2M
$$



이다.

예를 들면 다음과 같다.

| 방식 | $M$ | Bits/Symbol |
| --- | --- | --- |
| Binary | 2 | 1 |
| 4-ary | 4 | 2 |
| 8-ary | 8 | 3 |
| 16-ary | 16 | 4 |
| 32-ary | 32 | 5 |

예를 들어 16-ary system이라면



$$
M=16
$$



이므로



$$
k=\log_2 16=4
$$



이다.

즉 흐름은

**Bit Stream → 4 bit씩 묶음 → 16개의 가능한 Symbol 중 하나 선택 → Waveform**

으로 볼 수 있다.

M-ary symbol과 실제 ASK, PSK, QAM 성상도에 대한 내용은 다음 글에서 자세히 다룬다.

> 강의 자료에서는 계산 예제로 6-bit character coding이 등장한다. 여기서는 이를 강의 계산용 6-bit 문자 표현으로 이해하며, 표준 ASCII 자체는 7-bit이다.

---



## 6. 아날로그 신호의 Digital Representation

음성, 생체 신호, 지진 신호, Radar, Sonar 등 대부분의 실제 물리 신호는 본질적으로 아날로그이다.

아날로그 신호 $x(t)$는 일반적으로

- 시간(Time)이 연속적이고
- 진폭(Amplitude)도 연속적이다.

즉,



$$
\boxed{ \text{Continuous Time} + \text{Continuous Amplitude} }
$$



의 특성을 가진다.

하지만 디지털 시스템에서는 이산적인 데이터 표현을 사용한다.

따라서 Analog-to-Digital Conversion, 즉 **ADC**가 필요하다.

---



## 7. ADC의 기본 과정

아날로그 신호를 디지털화하는 핵심 과정은 다음과 같다.



$$
\boxed{ x(t) \rightarrow \text{Sampling} \rightarrow \text{Quantization} \rightarrow \text{Encoding} }
$$



각 단계의 의미는 다음과 같다.

| 단계 | 변화 |
| --- | --- |
| Sampling | Continuous Time → Discrete Time |
| Quantization | Continuous Amplitude → Discrete Amplitude |
| Encoding | Quantization Level → Binary Code |

따라서 Sampling만 수행했다고 해서 완전한 binary bit stream이 되는 것은 아니다.

Sampling 직후 신호는



$$
\boxed{ \text{Discrete Time} + \text{Continuous Amplitude} }
$$



로 생각할 수 있다.

이후 Quantization을 수행하면서 amplitude도 유한한 level로 제한된다.

---



## 8. Sampling

Sampling은 연속 시간 신호에서 일정한 시간 간격마다 값을 취하는 과정이다.

연속 시간 신호를



$$
x(t)
$$



라고 하면 sampling된 sequence는



$$
x[n]=x(nT_s)
$$



로 표현할 수 있다.

여기서

- $T_s$ : Sampling Period
- $f_s$ : Sampling Frequency

이며



$$
\boxed{ f_s=\frac{1}{T_s} }
$$



이다.

즉 $f_s$는 **1초 동안 몇 개의 sample을 얻는가**를 나타낸다.

---



## 9. Sampling의 핵심 의미

Sampling은 신호의 시간축을 연속적인 $t$에서 이산적인 $n$으로 바꾸는 과정이다.



$$
t=nT_s
$$



이므로



$$
n=\frac{t}{T_s}
$$



의 관계를 가진다.

따라서



$$
x(t) \rightarrow x[n]
$$



의 변환은 단순히 데이터의 수를 줄이는 것이 아니라 **continuous-time representation을 discrete-time representation으로 바꾸는 과정**이다.

---



## 10. Nyquist Sampling Theorem

Sampling에서 가장 중요한 질문은 다음과 같다.

> Sample을 얼마나 자주 측정해야 원래 신호를 다시 복원할 수 있을까?

원신호가 최대 주파수 $f_{\max}$까지 포함하는 band-limited signal이라고 하자.

이 경우 이상적인 Sampling Theorem에서는



$$
\boxed{ f_s \ge 2f_{\max} }
$$



을 만족해야 원신호를 복원할 수 있다.

이를 **Nyquist Sampling Criterion**이라고 한다.

그리고



$$
2f_{\max}
$$



을 **Nyquist Rate**라고 부른다.

---



### 예시

원신호의 최대 주파수가



$$
f_{\max}=5\,\text{kHz}
$$



라면 최소 sampling frequency는



$$
f_s\ge10\,\text{kHz}
$$



이다.

즉 최소 초당 10,000개의 sample이 필요하다.

실제 시스템에서는 filter의 transition band와 여러 비이상적인 조건 때문에 정확히 $2f_{\max}$보다 높은 sampling frequency를 사용하는 경우가 많다.

---



## 11. 왜 $2f_{\max}$가 필요한가?

Sampling을 Frequency Domain에서 보면 그 이유가 명확해진다.

원신호의 Fourier Transform을



$$
X(f)
$$



라고 하자.

Sampling을 수행하면 원래 spectrum의 복사본이



$$
0,\pm f_s,\pm2f_s,\cdots
$$



위치에 반복해서 생성된다.

즉 Sampling 이후의 spectrum은



$$
X_s(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} X(f-kf_s)
$$



로 표현된다.

따라서 Sampling은 Frequency Domain에서

> **원래 Spectrum을 $f_s$ 간격으로 반복시키는 과정**

으로 볼 수 있다.

---



## 12. Ideal Sampling / Impulse Sampling

이론적인 Sampling은 impulse train을 이용하여 표현할 수 있다.

Sampling impulse train을



$$
p(t) = \sum_{n=-\infty}^{\infty} \delta(t-nT_s)
$$



라고 하자.

Sampling된 신호는



$$
x_s(t)=x(t)p(t)
$$



이다.

따라서



$$
x_s(t) = x(t) \sum_{n=-\infty}^{\infty} \delta(t-nT_s)
$$



이고 Delta Function의 성질에 의해



$$
\boxed{ x_s(t) = \sum_{n=-\infty}^{\infty} x(nT_s)\delta(t-nT_s) }
$$



가 된다.

즉 각각의 impulse 위치에 원신호의 sample 값이 들어간다.

---



## 13. Impulse Train의 Frequency Domain 표현

Time Domain의 impulse train



$$
p(t) = \sum_{n=-\infty}^{\infty} \delta(t-nT_s)
$$



은 Frequency Domain에서도 impulse train이 된다.



$$
P(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} \delta(f-kf_s)
$$



여기서



$$
f_s=\frac{1}{T_s}
$$



이다.

Sampling은 Time Domain에서 곱셈이다.



$$
x_s(t)=x(t)p(t)
$$



Fourier Transform의 성질에 의해 Time Domain의 곱셈은 Frequency Domain의 Convolution에 대응한다.



$$
\boxed{ x(t)p(t) \Longleftrightarrow X(f)*P(f) }
$$



따라서



$$
X_s(f) = X(f)*P(f)
$$



이고 결과적으로



$$
\boxed{ X_s(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} X(f-kf_s) }
$$



가 된다.

이 식이 바로 Sampling을 하면 Spectrum Replica가 $f_s$ 간격으로 만들어지는 이유이다.

---



## 14. Sampling을 Frequency Domain에서 이해하기

원래 spectrum이



$$
-f_{\max}\le f\le f_{\max}
$$



범위에 존재한다고 하자.

Sampling을 하면 같은 spectrum이



$$
\cdots,-2f_s,-f_s,0,f_s,2f_s,\cdots
$$



를 중심으로 반복된다.

인접한 spectrum이 서로 겹치지 않으려면



$$
f_s-f_{\max}\ge f_{\max}
$$



이어야 한다.

따라서



$$
\boxed{ f_s\ge2f_{\max} }
$$



이라는 Nyquist 조건이 얻어진다.

---



## 15. Aliasing

Sampling frequency가 충분하지 않으면 인접한 spectrum replica가 서로 겹친다.

즉,



$$
\boxed{ f_s<2f_{\max} }
$$



인 경우 spectral overlap이 발생할 수 있다.

이 현상을 **Aliasing**이라고 한다.

Aliasing이 발생하면 서로 다른 원래의 주파수 성분이 sampled signal에서 구분되지 않는 형태로 나타날 수 있다.

즉 수신 또는 복원 단계에서

> 이 주파수 성분이 원래 어느 주파수였는지 구분할 수 없게 된다.

---



## 16. 정상 Sampling과 Undersampling

### 정상 Sampling



$$
f_s>2f_{\max}
$$



이면 spectrum replica들이 서로 분리된다.

따라서 가운데 baseband spectrum을 Low-Pass Filter로 추출하여 원신호를 복원할 수 있다.

### Undersampling



$$
f_s<2f_{\max}
$$



이면 spectrum replica가 겹친다.

이 경우 서로 다른 spectral component가 섞이기 때문에 원래 spectrum을 단순히 분리할 수 없게 된다.

즉,



$$
\boxed{ \text{Undersampling} \rightarrow \text{Spectral Overlap} \rightarrow \text{Aliasing} }
$$



이다.

---



## 17. Anti-Aliasing Filter

실제 물리 신호는 이상적으로 완전히 band-limited되어 있지 않은 경우가 많다.

따라서 ADC 이전에 높은 주파수 성분을 제한하는 과정이 필요하다.

이때 사용하는 것이 **Anti-Aliasing Filter**이다.

일반적인 구성은



$$
\boxed{ \text{Analog Signal} \rightarrow \text{Anti-Aliasing LPF} \rightarrow \text{ADC} }
$$



이다.

Sampling frequency가 $f_s$라면 sampling 전에 유효한 신호 bandwidth를 대략



$$
\frac{f_s}{2}
$$



이하로 제한해야 한다.

즉 이상적인 관점에서는



$$
f_c\le\frac{f_s}{2}
$$



로 생각할 수 있다.

실제 filter는 transition band를 가지므로 실제 설계에서는 어느 정도 여유를 둔다.

---



## 18. Anti-Aliasing Filter가 필요한 이유

Sampling 전에 $f_s/2$보다 높은 주파수 성분이 충분히 존재하면 Sampling 과정에서 해당 성분이 baseband 영역으로 겹쳐 들어올 수 있다.

따라서 실제 시스템에서는

**Analog Signal → Anti-Aliasing LPF → Band-Limited Signal → Sampling**

과 같은 구조를 사용한다.

핵심은 다음과 같다.



$$
\boxed{ \text{Aliasing이 발생하기 전에 불필요한 고주파 성분을 제한한다.} }
$$



한 번 서로 다른 spectrum이 실제로 겹쳐 aliasing이 발생하면 단순한 후처리 LPF만으로 원래의 서로 다른 주파수 성분을 완전히 분리할 수 없는 경우가 일반적이다.

따라서 Sampling 이전의 Anti-Aliasing Filtering이 중요하다.

---



## 19. Sampling의 종류

강의에서는 Sampling 방법을 다음과 같이 구분하였다.

1. Ideal Sampling / Impulse Sampling
2. Natural Sampling
3. Flat-Top Sampling
4. Sample-and-Hold

---



## 20. Ideal Sampling

Ideal Sampling은 폭이 0인 이상적인 impulse를 사용하는 수학적 모델이다.



$$
p(t) = \sum_{n=-\infty}^{\infty} \delta(t-nT_s)
$$



를 이용하여



$$
x_s(t)=x(t)p(t)
$$



로 표현한다.

수학적인 분석에는 매우 편리하지만 실제 회로에서는 폭이 0이고 높이가 무한대인 이상적인 impulse를 만들 수 없다.

따라서 실제 시스템에서는 finite-width pulse를 사용한다.

---



## 21. Natural Sampling

Natural Sampling에서는 일정한 폭을 가진 rectangular pulse train을 사용한다.

Impulse Sampling과 달리 sampling pulse가 일정한 시간 동안 열린다.

따라서 pulse가 열려 있는 동안 출력은 원래 analog waveform을 따라간다.

즉 각 sampling pulse의 위쪽 모양이 평평하지 않고 원신호의 형태를 유지한다.

개념적으로는



$$
x_s(t)=x(t)p(t)
$$



로 나타낼 수 있다.

여기서 $p(t)$는 periodic rectangular pulse train이다.

Sampling Period를 $T_s$, pulse width를 $\tau$라고 두면 일반적으로



$$
\tau<T_s
$$



이다.

---



## 22. Sample-and-Hold Operation

실제 ADC에서는 **Sample-and-Hold(S/H)** 구조가 중요하다.

동작은 다음과 같다.

1. 특정 순간의 신호값을 Sample한다.
2. 그 값을 일정 시간 동안 Hold한다.
3. 다음 sample 시점에서 새로운 값을 얻는다.

따라서 출력은 계단 형태를 가진다.

Sample-and-Hold가 필요한 이유는 ADC가 Quantization과 Encoding을 수행하는 동안 입력이 계속 변하면 정확한 변환이 어려워질 수 있기 때문이다.

따라서 변환이 진행되는 짧은 시간 동안 sample 값을 유지한다.

---



## 23. Quantization

Sampling을 수행해도 각 sample의 amplitude는 여전히 연속값일 수 있다.

따라서 amplitude를 유한한 level 중 하나로 근사해야 한다.

이 과정을 **Quantization**이라고 한다.

예를 들어 3-bit Quantizer라면



$$
2^3=8
$$



개의 quantization level을 사용할 수 있다.

| Quantization Level | Binary Code |
| --- | --- |
| 0 | 000 |
| 1 | 001 |
| 2 | 010 |
| 3 | 011 |
| 4 | 100 |
| 5 | 101 |
| 6 | 110 |
| 7 | 111 |

즉,



$$
\boxed{ \text{Continuous Amplitude} \rightarrow \text{Discrete Amplitude} }
$$



의 과정이다.

---



## 24. Sampling과 Quantization의 차이

Sampling과 Quantization은 서로 다른 축을 이산화한다.

### Sampling

시간축을 이산화한다.



$$
x(t)\rightarrow x[n]
$$



### Quantization

진폭축을 이산화한다.



$$
x[n]\rightarrow x_q[n]
$$



따라서 전체 과정은

**Analog Signal → Sampling → Discrete-Time Signal → Quantization → Discrete-Amplitude Signal**

로 볼 수 있다.

---



## 25. PCM

**PCM(Pulse Code Modulation)**은 아날로그 신호를 digital code sequence로 표현하는 대표적인 방식이다.

기본적인 과정은



$$
\boxed{ \text{Sampling} \rightarrow \text{Quantization} \rightarrow \text{Encoding} }
$$



이다.

예를 들어 8개의 quantization level을 사용한다면



$$
8=2^3
$$



이므로 각 sample을 표현하는 데 3 bit가 필요하다.

따라서 Quantized Sample은

`000`, `001`, `010`, ..., `111`

과 같은 binary sequence로 표현될 수 있다.

---



## 26. Bit Stream에서 Baseband Waveform으로

Encoding 결과는 다음과 같은 bit stream이 된다.

`101100101001...`

그러나 bit stream 자체는 물리적인 전송 신호가 아니다.

따라서 bit 또는 symbol을 실제 waveform으로 변환해야 한다.



$$
\boxed{ \text{Bit Stream} \rightarrow \text{Symbol} \rightarrow \text{Pulse Waveform} }
$$



이 과정이 Baseband Modulation 또는 Pulse Modulation과 연결된다.

이후 필요한 시스템에서는 ASK, PSK, FSK, QAM 등의 Bandpass Modulation 단계가 이어질 수 있다.

---



## 27. Digital Processing of Analog Signals

아날로그 신호를 디지털 방식으로 처리하는 전체 흐름은 다음과 같이 볼 수 있다.



$$
\boxed{ \text{Analog} \rightarrow \text{A/D} \rightarrow \text{Digital Processing} \rightarrow \text{D/A} \rightarrow \text{Analog} }
$$



강의에서는 여기에 sampling rate 변환을 포함하여 다음 구조를 다루었다.

**Analog Signal → A/D Converter → Downsampling → Digital Signal Processing → Upsampling → D/A Converter → Analog Signal**

각 단계에서 필요에 따라 filter가 함께 사용된다.

---



## 28. Filtering을 포함한 전체 구조

실제 시스템의 흐름을 조금 더 자세히 쓰면 다음과 같다.



$$
\text{Analog Signal}
$$



↓



$$
\text{Analog Anti-Alias Filter}
$$



↓



$$
\text{A/D Converter}
$$



↓



$$
\text{Digital Anti-Alias Filter}
$$



↓



$$
\text{Downsampling}
$$



↓



$$
\text{Digital Signal Processing}
$$



↓



$$
\text{Upsampling}
$$



↓



$$
\text{Interpolation Filter}
$$



↓



$$
\text{D/A Converter}
$$



↓



$$
\text{Analog Reconstruction Filter}
$$



↓



$$
\text{Analog Signal}
$$



이 구조를 이해하면 Anti-Alias Filter와 Interpolation Filter가 각각 어디에 사용되는지도 쉽게 구분할 수 있다.

---



## 29. Downsampling

Downsampling은 이미 존재하는 discrete-time sequence의 sampling rate를 낮추는 과정이다.

$N$배 Downsampling을 한다면



$$
\boxed{ y[n]=x[nN] }
$$



이다.

즉 입력 sample 중 $N$개마다 하나의 sample을 남긴다.

Sampling Rate도



$$
f_s \rightarrow \frac{f_s}{N}
$$



로 감소한다.

예를 들어 $N=2$라면 sample을 하나씩 건너뛰면서 절반만 남긴다.

---



## 30. Downsampling과 Anti-Alias Filter

Downsampling에서 단순히 sample만 제거하면 aliasing이 발생할 수 있다.

Downsampling 후의 새로운 sampling frequency는



$$
f_s'=\frac{f_s}{N}
$$



이다.

따라서 새로운 Nyquist frequency는



$$
\frac{f_s'}{2} = \frac{f_s}{2N}
$$



이다.

입력 신호가 이 범위를 넘는 주파수 성분을 포함하고 있다면 Downsampling 과정에서 spectrum이 겹칠 수 있다.

따라서 일반적으로



$$
\boxed{ x[n] \rightarrow \text{Anti-Alias LPF} \rightarrow \downarrow N \rightarrow y[n] }
$$



의 구조를 사용한다.

---



## 31. Downsampling의 Frequency Domain 의미

Downsampling은 Time Domain에서 sample 수를 줄이는 과정이다.

Frequency Domain에서는 normalized spectrum의 형태가 확장되면서 서로 다른 spectral replica가 겹칠 가능성이 생긴다.

따라서



$$
\boxed{ \text{Downsampling 전 Anti-Alias Filtering이 필요하다.} }
$$



는 것이 핵심이다.

강의 자료에서도 Downsampling 전에 Anti-Alias Filter를 배치하는 구조를 사용한다.

---



## 32. Upsampling

Upsampling은 discrete-time sequence의 sampling rate를 증가시키는 과정이다.

$L$배 Upsampling을 수행하면 기존 sample 사이에



$$
L-1
$$



개의 zero sample을 삽입한다.

예를 들어 $L=4$라면

원래 sequence가

`x[0], x[1], x[2], ...`

일 때

`x[0], 0, 0, 0, x[1], 0, 0, 0, x[2], ...`

와 같은 형태가 된다.

수식으로는



$$
x_{\uparrow L}[n] = \begin{cases} x[n/L], & n=0,\pm L,\pm2L,\cdots\\ 0, & \text{otherwise} \end{cases}
$$



이다.

Sampling Rate는



$$
f_s \rightarrow Lf_s
$$



가 된다.

---



## 33. Zero Insertion만으로는 충분하지 않다

Upsampling 과정에서 zero를 삽입했다고 해서 실제 중간 sample 값을 새롭게 얻은 것은 아니다.

따라서 Zero Insertion 이후에는 **Interpolation Filter**가 필요하다.

전체 과정은



$$
\boxed{ \text{Zero Insertion} \rightarrow \text{Interpolation Filter} }
$$



이다.

Interpolation Filter는 zero insertion 과정에서 생긴 spectral image를 제거하고 기존 sample 사이의 값을 자연스럽게 연결한다.

---



## 34. Upsampling의 Frequency Domain 의미

Upsampling 과정에서 zero를 삽입하면 Frequency Domain에 여러 spectral image가 나타난다.

따라서



$$
\boxed{ \text{Upsampling} \rightarrow \text{Spectral Images} \rightarrow \text{Interpolation LPF} }
$$



과정을 거친다.

즉 Upsampling은 단순히 zero를 넣는 것까지가 아니라 실제 사용 관점에서는



$$
\boxed{ \text{Zero Insertion} + \text{Interpolation Filtering} }
$$



으로 이해하면 좋다.

---



## 35. Downsampling과 Upsampling 비교

| 구분 | Downsampling | Upsampling |
| --- | --- | --- |
| Sampling Rate | 감소 | 증가 |
| Time Domain | Sample 제거 | Zero 삽입 |
| 대표 표현 | $\downarrow N$ | $\uparrow L$ |
| 주요 Filter | Anti-Alias Filter | Interpolation Filter |
| 주요 문제 | Aliasing | Spectral Imaging |

따라서 핵심은 다음과 같다.



$$
\boxed{ \text{Downsampling 전에는 Anti-Alias Filter} }
$$





$$
\boxed{ \text{Upsampling 후에는 Interpolation Filter} }
$$



---



## 36. D/A Conversion

Digital sequence를 다시 analog waveform으로 변환하는 과정이 **Digital-to-Analog Conversion(DAC)**이다.

이론적으로 discrete sequence



$$
y[n]
$$



를 다음과 같은 impulse train으로 표현할 수 있다.



$$
\tilde y(t) = \sum_{n=-\infty}^{\infty} y[n]\delta(t-nT_s)
$$



그다음 Reconstruction Filter를 통과시키면 continuous-time signal을 얻는다.



$$
\boxed{ y[n] \rightarrow \text{Impulse Train} \rightarrow \text{Reconstruction Filter} \rightarrow y_c(t) }
$$



---



## 37. D/A를 Frequency Domain에서 보기

Discrete-time sequence의 DTFT는 주기성을 가진다.

Normalized Frequency를



$$
\lambda=\frac{f}{f_s}
$$



라고 두면



$$
Y(e^{j2\pi\lambda})
$$



는 $\lambda$에 대해 주기적이다.

이를 실제 frequency axis로 변환하면 spectrum은



$$
0,\pm f_s,\pm2f_s,\cdots
$$



주변에서 반복된다.

DAC 이후 Reconstruction Filter는 이 중 원하는 baseband spectrum을 추출하는 역할을 한다.

---



## 38. Ideal Reconstruction

Aliasing 없이 Sampling이 이루어졌다면 이상적인 Low-Pass Filter를 통해 원신호를 복원할 수 있다.

Frequency Domain에서는 반복된 spectrum 중 baseband에 위치한 spectrum 하나만 추출한다.

Time Domain에서는 이상적인 Low-Pass Filter의 impulse response가 sinc 형태가 된다.

정규화 sinc를



$$
\operatorname{sinc}(x) = \frac{\sin(\pi x)}{\pi x}
$$



라고 정의하면 이상적인 reconstruction은



$$
\boxed{ x(t) = \sum_{n=-\infty}^{\infty} x[n] \operatorname{sinc} \left( \frac{t-nT_s}{T_s} \right) }
$$



형태로 나타낼 수 있다.

즉 각각의 sample이 sinc waveform을 만들고, 이들을 합하면 연속적인 원신호를 재구성할 수 있다.

이를 **Sinc Interpolation**이라고 한다.

---



## 39. Sampling된 값만으로 원신호를 복원할 수 있는 이유

Sampling 과정에서는 sample 사이의 값을 직접 저장하지 않는다.

그럼에도 원래 신호가 band-limited이고 Nyquist 조건을 만족한다면



$$
x[n]=x(nT_s)
$$



만으로 전체 continuous waveform을 결정할 수 있다.

즉,



$$
\boxed{ \text{Band-Limited Signal} + \text{충분한 Sampling Rate} \Rightarrow \text{Ideal Reconstruction 가능} }
$$



이라는 것이 Sampling Theorem의 핵심이다.

---



## 40. Zero-Order Hold

실제 DAC에서 이상적인 impulse를 직접 출력하는 것은 어렵다.

따라서 가장 간단하게 사용할 수 있는 방법 중 하나가 **Zero-Order Hold(ZOH)**이다.

Zero-Order Hold는 각 sample 값을 다음 sample 시점까지 그대로 유지한다.

따라서 출력 waveform은 계단 형태가 된다.

ZOH는 구현이 간단하여 실제 hardware에서 자주 사용되는 개념이다.

---



## 41. Zero-Order Hold의 Impulse Response

이상적인 ZOH의 impulse response는 rectangular pulse로 표현할 수 있다.

개념적으로



$$
h_{\text{ZOH}}(t) = \begin{cases} 1, & 0\le t<T_s\\ 0, & \text{otherwise} \end{cases}
$$



로 나타낼 수 있다.

따라서 출력은 convolution으로 표현된다.



$$
\boxed{ y_c(t) = \tilde y(t)*h_{\text{ZOH}}(t) }
$$



즉 impulse train의 각 impulse가 폭 $T_s$의 rectangular pulse로 확장된다.

---



## 42. Zero-Order Hold는 이상적인 Reconstruction Filter가 아니다

ZOH는 구현이 간단하지만 이상적인 Low-Pass Reconstruction Filter와 동일하지 않다.

Rectangular pulse의 Fourier Transform은 sinc 형태이기 때문에 Frequency Domain에서는 ZOH의 응답도 sinc 형태의 envelope를 가진다.

따라서 높은 주파수 쪽으로 갈수록 amplitude가 감소하는 **droop**가 나타날 수 있다.

또한 spectrum의 image를 완벽하게 제거하지 못한다.

따라서 실제 DAC 시스템에서는 ZOH 이후에 추가적인 Analog Reconstruction Filter를 사용할 수 있다.

---



## 43. Filtering은 Time Domain에서 Convolution이다

LTI System의 Filter Impulse Response를



$$
h(t)
$$



라고 하자.

입력 신호가



$$
x(t)
$$



이면 출력은



$$
\boxed{ y(t)=x(t)*h(t) }
$$



이다.

Convolution은 다음과 같이 정의된다.



$$
y(t) = \int_{-\infty}^{\infty} x(\tau)h(t-\tau)d\tau
$$



즉 Filtering은 Time Domain에서 **입력 신호와 Filter Impulse Response의 Convolution**으로 이해할 수 있다.

---



## 44. Delta Function과 Convolution

Delta Function에는 매우 중요한 성질이 있다.



$$
\boxed{ x(t)*\delta(t)=x(t) }
$$



또한 shifted delta와 convolution하면



$$
\boxed{ x(t)*\delta(t-t_0) = x(t-t_0) }
$$



가 된다.

즉 Delta Function의 위치를 이동시키면 Convolution 결과에서도 신호가 같은 만큼 이동한다.

Sampling에서 impulse train이 중요한 이유도 이 성질과 연결된다.

---



## 45. Time Domain과 Frequency Domain의 관계

Fourier Transform에서는 두 가지 관계가 매우 중요하다.

### Time Domain Convolution



$$
x(t)*h(t)
$$



은 Frequency Domain에서



$$
X(f)H(f)
$$



가 된다.

즉,



$$
\boxed{ x(t)*h(t) \Longleftrightarrow X(f)H(f) }
$$



이다.

### Time Domain Multiplication

반대로 Time Domain의 곱셈은 Frequency Domain의 Convolution에 대응한다.



$$
\boxed{ x(t)p(t) \Longleftrightarrow X(f)*P(f) }
$$



Sampling이 바로 이 경우이다.

Sampling에서는



$$
x_s(t)=x(t)p(t)
$$



이므로



$$
X_s(f)=X(f)*P(f)
$$



가 된다.

그 결과 원 spectrum의 replica가 반복해서 생성된다.

---



## 46. Sampling과 Filtering의 관계

Sampling과 Filtering을 Fourier Transform 관점에서 비교하면 다음과 같다.

| 동작 | Time Domain | Frequency Domain |
| --- | --- | --- |
| Sampling | Multiplication | Convolution |
| Filtering | Convolution | Multiplication |

즉,



$$
\boxed{ \text{Sampling} : \text{Time Multiplication} \rightarrow \text{Frequency Convolution} }
$$



이고,



$$
\boxed{ \text{Filtering} : \text{Time Convolution} \rightarrow \text{Frequency Multiplication} }
$$



이다.

이 관계는 이후 디지털 통신과 DSP에서 계속 반복해서 등장한다.

---



## 47. 전체 Digital Signal Processing 흐름

이번 강의에서 다룬 내용을 하나의 흐름으로 연결하면 다음과 같다.



$$
\text{Physical Analog Signal}
$$



↓



$$
\text{Analog Anti-Alias Filter}
$$



↓



$$
\text{Sampling}
$$



↓



$$
\text{Quantization}
$$



↓



$$
\text{Encoding}
$$



↓



$$
\text{Digital Sequence}
$$



↓



$$
\text{Digital Anti-Alias Filter}
$$



↓



$$
\text{Downsampling}
$$



↓



$$
\text{Digital Signal Processing}
$$



↓



$$
\text{Upsampling}
$$



↓



$$
\text{Interpolation Filter}
$$



↓



$$
\text{DAC}
$$



↓



$$
\text{Zero-Order Hold}
$$



↓



$$
\text{Analog Reconstruction Filter}
$$



↓



$$
\text{Continuous Analog Signal}
$$



이를 가장 단순하게 표현하면



$$
\boxed{ \text{Analog} \rightarrow \text{ADC} \rightarrow \text{DSP} \rightarrow \text{DAC} \rightarrow \text{Analog} }
$$



이다.

---



## 48. Sampling → Quantization → Encoding 다시 보기

전체 과정을 신호의 성질 변화로 보면 이해하기 쉽다.

### 원래 Analog Signal



$$
\boxed{ \text{Continuous Time} + \text{Continuous Amplitude} }
$$



### Sampling



$$
\boxed{ \text{Discrete Time} + \text{Continuous Amplitude} }
$$



### Quantization



$$
\boxed{ \text{Discrete Time} + \text{Discrete Amplitude} }
$$



### Encoding

Quantization Level을 Binary Representation으로 바꾼다.



$$
\text{Quantization Level} \rightarrow \text{Binary Code} \rightarrow \text{Bit Stream}
$$



### Waveform Mapping



$$
\text{Bit Stream} \rightarrow \text{Symbol} \rightarrow \text{Physical Waveform}
$$



결국 디지털 통신에서 실제 채널을 통과하는 것은 추상적인 `0`, `1` 자체가 아니라 **bit와 symbol에 대응되는 물리적 waveform**이다.

---



## 49. Sampling과 Downsampling은 같은 것인가?

둘은 비슷해 보이지만 출발점이 다르다.

### Sampling

Continuous-time signal을 discrete-time sequence로 변환한다.



$$
x(t)\rightarrow x[n]
$$



### Downsampling

이미 discrete-time인 sequence의 sampling rate를 낮춘다.



$$
x[n]\rightarrow y[n]=x[nN]
$$



따라서



$$
\boxed{ \text{Sampling} : \text{Continuous} \rightarrow \text{Discrete} }
$$



이고,



$$
\boxed{ \text{Downsampling} : \text{Discrete} \rightarrow \text{Lower-Rate Discrete} }
$$



이다.

---



## 50. Upsampling은 새로운 정보를 만드는가?

Upsampling을 하면 sample의 개수는 증가한다.

하지만 Zero Insertion 자체가 새로운 측정 정보를 생성하는 것은 아니다.

예를 들어 기존 sample 사이에

`0, 0, 0`

을 삽입한다고 해서 실제 물리 신호를 새롭게 세 번 측정한 것은 아니다.

Interpolation Filter는 기존 sample을 이용해 그 사이를 매끄럽게 채울 값을 계산한다.

따라서



$$
\boxed{ \text{Upsampling} \neq \text{새로운 정보 생성} }
$$



이다.

Sampling Rate Representation을 더 높은 rate로 바꾸는 과정으로 이해하는 것이 적절하다.

---



## 51. Anti-Alias Filter와 Interpolation Filter 비교

둘 다 LPF 형태로 구현되는 경우가 많지만 목적이 다르다.

| Filter | 위치 | 목적 |
| --- | --- | --- |
| Anti-Alias Filter | Sampling / Downsampling 전 | Spectrum overlap 방지 |
| Interpolation Filter | Upsampling 후 | Spectral image 제거 |
| Reconstruction Filter | DAC 후 | Analog waveform 복원 |

따라서 단순히 모두 LPF라고 외우기보다 **어떤 문제를 해결하기 위한 filter인지**를 구분해야 한다.

---



## 52. 이번 강의의 가장 중요한 연결

Sampling 과정은 다음과 같이 연결된다.



$$
x_s(t)=x(t)p(t)
$$



↓



$$
X_s(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} X(f-kf_s)
$$



↓



$$
\text{Spectrum Replica가 }f_s\text{ 간격으로 생성}
$$



↓



$$
f_s\ge2f_{\max}
$$



↓

Nyquist 조건을 만족하지 않으면



$$
f_s<2f_{\max}
$$



↓



$$
\text{Spectral Overlap}
$$



↓



$$
\boxed{ \text{Aliasing} }
$$



따라서 해결 방법은



$$
\boxed{ \text{충분한 Sampling Rate} + \text{Anti-Alias Filtering} }
$$



이다.

이 흐름을 이해하면 단순히



$$
f_s\ge2f_{\max}
$$



공식 하나를 외우는 것보다 Sampling Theorem을 훨씬 자연스럽게 이해할 수 있다.

---



## 53. 핵심 정리

### Formatting

정보를 통신 시스템에서 처리할 수 있는 digital representation으로 바꾸는 과정이다.

---



### Character Coding

문자 정보는 ASCII 등의 code를 통해 bit stream으로 변환할 수 있다.

표준 ASCII는 7 bit이며



$$
2^7=128
$$



개의 code를 표현할 수 있다.

---



### Sampling

Continuous-Time Signal을 Discrete-Time Signal로 바꾼다.



$$
x[n]=x(nT_s)
$$





$$
f_s=\frac{1}{T_s}
$$



---



### Nyquist Sampling Theorem

Band-limited signal을 aliasing 없이 sampling하려면



$$
\boxed{ f_s\ge2f_{\max} }
$$



이어야 한다.

---



### Sampling의 Frequency Domain 의미

Sampling하면 원 spectrum이 $f_s$ 간격으로 반복된다.



$$
X_s(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} X(f-kf_s)
$$



---



### Aliasing

Spectrum replica가 서로 겹치는 현상이다.



$$
f_s<2f_{\max}
$$



이면 발생할 수 있다.

---



### Anti-Alias Filter

Sampling 전에 불필요한 고주파 성분을 제한하여 aliasing을 방지한다.

---



### Quantization

연속적인 amplitude를 유한한 discrete level로 바꾼다.

---



### PCM



$$
\boxed{ \text{Sampling} \rightarrow \text{Quantization} \rightarrow \text{Encoding} }
$$



과정을 통해 Analog Signal을 Digital Bit Stream으로 표현한다.

---



### Downsampling

Sampling Rate를 감소시킨다.



$$
y[n]=x[nN]
$$



Downsampling 전에 Anti-Alias Filter를 사용해야 할 수 있다.

---



### Upsampling

기존 sample 사이에 zero를 삽입하여 Sampling Rate를 높인다.



$$
\boxed{ \text{Zero Insertion} + \text{Interpolation Filter} }
$$



로 구성된다.

---



### DAC

Digital Sequence를 Continuous-Time Signal로 변환한다.



$$
\text{Sequence} \rightarrow \text{Impulse/Hold Signal} \rightarrow \text{Reconstruction Filter}
$$



---



### Zero-Order Hold

각 sample 값을 다음 sample까지 유지한다.

구현이 간단하지만 이상적인 Reconstruction Filter는 아니다.

---



### Filtering

Time Domain에서는 Convolution이다.



$$
y(t)=x(t)*h(t)
$$



Frequency Domain에서는 Multiplication이다.



$$
Y(f)=X(f)H(f)
$$



---



## 54. 한 문장으로 정리

> **아날로그 정보는 Sampling으로 시간을 이산화하고, Quantization으로 진폭을 이산화한 뒤 Encoding을 통해 Bit Stream으로 변환되며, 이 과정에서 Nyquist 조건과 Anti-Aliasing Filtering이 원신호의 정보를 보존하는 핵심 역할을 한다.**

---



## 55. 다음 글과의 연결

이번 글에서 얻은 결과는 결국 **Bit Stream**이다.

하지만 실제 통신 채널을 통과하는 것은 Bit 자체가 아니라 물리적인 Waveform이다.

따라서 다음 단계는



$$
\boxed{ \text{Bit Stream} \rightarrow \text{Symbol} \rightarrow \text{Signal Waveform} }
$$



이다.

여기서 등장하는 핵심 개념이 다음과 같다.

- Symbol
- $M$-ary Signaling
- Bit Rate
- Symbol Rate
- Baud Rate
- ASK
- FSK
- PSK
- QAM
- Constellation Diagram
- Decision Boundary
- Gray Coding

다음 글에서는 이러한 개념을 연결하여 **디지털 변조와 신호자리표(Constellation Diagram)**를 정리한다.

---

이전: [디지털 통신의 전체 구조와 핵심 개념](communication-system-overview.md)

다음: [디지털 변조와 신호자리표: Bit에서 QAM까지](../modulation/digital-modulation-and-constellations.md)
