# 채널 코딩과 에러 제어: Block Coding, Hamming Code, Syndrome와 Convolution Coding

[TOC]

디지털 통신에서는 채널을 통과하는 동안 잡음이나 간섭 때문에 전송한 비트가 뒤집힐 수 있다.

예를 들어 송신한 데이터가



```text
1011001
```



이었는데 채널에서 한 비트가 바뀌어



```text
1001001
```



과 같이 수신될 수 있다.

수신기는 단순히 수신된 비트만 보고서는 **어느 비트가 잘못되었는지 알 수 없다.**

이를 해결하기 위해 사용하는 것이 **채널 코딩(Channel Coding)** 이다.

채널 코딩에서는 원본 데이터에 일정한 규칙을 가진 **중복 비트(Redundancy)** 를 추가한다.



```text
정보 비트
    ↓
Channel Encoder
    ↓
정보 비트 + Redundancy
    ↓
Channel
    ↓
수신 데이터
    ↓
Channel Decoder
    ↓
오류 검출 / 정정
```



대표적인 채널 코딩 방법으로

- Block Coding
- Convolution Coding

이 있다.

이번 글에서는 특히 **Block Coding의 Generator Matrix, Parity-Check Matrix, Syndrome을 이용한 오류 검출과 정정 과정**을 중심으로 정리한다.

---

## 1. Block Coding

**Block Coding**은 입력 데이터를 일정한 길이의 블록으로 나누고, 각 블록에 오류 검출 또는 정정을 위한 비트를 추가하는 방식이다.

원래 정보 비트가 $k$개이고 채널 코딩 후 $n$개의 비트가 만들어진다면



$$
(n,k)
$$



Block Code라고 한다.

예를 들어 Hamming $(7,4)$ Code에서는



$$
k=4,\qquad n=7
$$



이므로



```text
4개의 정보 비트
        ↓
Block Encoder
        ↓
7개의 Codeword 비트
```



가 된다.

추가되는



$$
n-k
$$



개의 비트는 오류 검출 및 정정을 위한 **Parity Bit**이다.

Hamming $(7,4)$의 경우



$$
n-k=3
$$



이므로 4개의 정보 비트에 3개의 parity bit를 추가한다.

---



## 2. Block Coding에서 사용되는 세 가지 벡터

Block Coding의 동작을 이해할 때 다음 세 벡터를 구분하는 것이 중요하다.

### Information Vector

송신하고자 하는 원본 데이터를



$$
\mathbf{u}
$$



라고 하자.

Hamming $(7,4)$ Code에서는



$$
\mathbf{u} = [u_1\ u_2\ u_3\ u_4]
$$



이므로 크기는



$$
1\times4
$$



이다.

예를 들어



$$
\mathbf{u} = [1\ 0\ 1\ 1]
$$



이라고 하자.

---

### Codeword

Channel Encoder를 통과해 생성된 전송용 코드워드를



$$
\mathbf{c}
$$



라고 한다.

Hamming $(7,4)$에서는



$$
\mathbf{c} = [c_1\ c_2\ \cdots c_7]
$$



으로 크기는



$$
1\times7
$$



이다.

---



### Error Vector

채널에서 발생한 오류를



$$
\mathbf{e}
$$



라고 한다.

예를 들어 7비트 중 세 번째 비트에만 오류가 발생하면



$$
\mathbf{e} = [0\ 0\ 1\ 0\ 0\ 0\ 0]
$$



으로 나타낼 수 있다.

수신 데이터 $\mathbf{r}$은



$$
\mathbf{r} = \mathbf{c}+\mathbf{e}
$$



로 표현한다.

여기서 $+$는 일반적인 덧셈이 아니라 **Modulo-2 연산**, 즉 XOR이다.

---



## 3. Modulo-2 연산

Block Coding에서 행렬 계산은 일반적인 실수 연산이 아니라 **GF(2)** 위에서 수행한다.

즉 모든 계산 결과는 0 또는 1이다.



$$
0+0=0
$$





$$
0+1=1
$$





$$
1+0=1
$$





$$
1+1=0
$$



따라서 덧셈은 XOR와 동일하다.



$$
1\oplus1=0
$$



이 성질 때문에 같은 비트열을 두 번 XOR하면 사라진다.



$$
x\oplus x=0
$$



이 성질이 Syndrome 계산에서 매우 중요하다.

---



## 4. Generator Matrix $G$

송신단에서는 **Generator Matrix $G$** 를 이용하여 정보 비트로부터 Codeword를 생성한다.

일반적인 $(n,k)$ Block Code에서



$$
G:k\times n
$$



이다.

정보 벡터가



$$
\mathbf{u}:1\times k
$$



이므로



$$
\mathbf{c} = \mathbf{u}G
$$



를 계산하면



$$
(1\times k)(k\times n) = 1\times n
$$



의 Codeword가 생성된다.

즉



$$
\boxed{\mathbf{c}=\mathbf{u}G}
$$



이다.

---



## 5. Systematic Code

이번 수업에서 사용한 형태는 정보 비트와 parity bit가 구분되는 **Systematic Code**이다.

Parity bit를 앞에 두고 Information bit를 뒤에 두면



$$
\mathbf{c} = [\text{Parity}\mid\text{Information}]
$$



형태가 된다.

이때 Generator Matrix는



$$
G= [P^T\mid I_k]
$$



로 나타낼 수 있다.

여기서

- $P$ : parity 생성에 필요한 행렬
- $I_k$ : $k\times k$ Identity Matrix

이다.

반대로 Information bit를 앞에 두는 convention을 사용할 수도 있다.

그 경우



$$
G=[I_k\mid P^T]
$$



가 된다.

따라서 책이나 강의 자료에서 $G$의 모양이 다르다고 해서 서로 다른 원리를 사용하는 것은 아니다.

**Codeword에서 정보 비트와 parity bit를 어느 쪽에 배치했는지 확인해야 한다.**

---



## 6. Hamming $(7,4)$ Code의 $H$ Matrix

이번 예제에서는 다음 Parity-Check Matrix를 사용한다.



$$
H= \begin{bmatrix} 1&0&0&0&1&1&1\\ 0&1&0&1&0&1&1\\ 0&0&1&1&1&0&1 \end{bmatrix}
$$



크기는



$$
3\times7
$$



이다.

일반적인 $(n,k)$ Code에서



$$
H:(n-k)\times n
$$



이므로



$$
(7-4)\times7 = 3\times7
$$



이 된다.

위 행렬은



$$
H=[I_{n-k}\mid P]
$$



형태이다.

따라서



$$
H=[I_3\mid P]
$$



이다.

여기서



$$
P= \begin{bmatrix} 0&1&1&1\\ 1&0&1&1\\ 1&1&0&1 \end{bmatrix}
$$



이다.

---



## 7. $H$로부터 $G$ 만들기



$$
H=[I_{n-k}\mid P]
$$



형태라면 대응하는 Generator Matrix는



$$
G=[P^T\mid I_k]
$$



로 만들 수 있다.

따라서



$$
P^T= \begin{bmatrix} 0&1&1\\ 1&0&1\\ 1&1&0\\ 1&1&1 \end{bmatrix}
$$



이고



$$
G= \begin{bmatrix} 0&1&1&1&0&0&0\\ 1&0&1&0&1&0&0\\ 1&1&0&0&0&1&0\\ 1&1&1&0&0&0&1 \end{bmatrix}
$$



가 된다.

크기는



$$
4\times7
$$



이다.

즉



```text
u : 1 × 4

G : 4 × 7

c = uG : 1 × 7
```



의 관계를 가진다.

---



## 8. 왜 $HG^T=0$이어야 하는가?

Generator Matrix $G$로 만든 모든 정상 Codeword는 Parity-Check Matrix $H$의 검사를 통과해야 한다.

따라서



$$
\boxed{HG^T=0}
$$



이 되도록 $H$와 $G$를 설계한다.

현재



$$
H=[I_{n-k}\mid P]
$$



이고



$$
G=[P^T\mid I_k]
$$



이므로



$$
G^T= \begin{bmatrix} P\\ I_k \end{bmatrix}
$$



이다.

따라서



$$
HG^T = [I_{n-k}\mid P] \begin{bmatrix} P\\ I_k \end{bmatrix}
$$





$$
= I_{n-k}P+PI_k
$$





$$
=P+P
$$



Modulo-2 연산에서는



$$
P+P=0
$$



이므로



$$
\boxed{HG^T=0}
$$



이 된다.

이 식은 Block Coding에서 매우 중요한 관계이다.

---



## 9. 실제 Encoding 예제

정보 비트를



$$
\mathbf{u} = [1\ 0\ 1\ 1]
$$



이라고 하자.

Generator Matrix는



$$
G= \begin{bmatrix} 0&1&1&1&0&0&0\\ 1&0&1&0&1&0&0\\ 1&1&0&0&0&1&0\\ 1&1&1&0&0&0&1 \end{bmatrix}
$$



이다.

Codeword는



$$
\mathbf{c} = \mathbf{u}G
$$



로 계산한다.

$\mathbf{u}$에서 값이 1인 위치는 첫 번째, 세 번째, 네 번째이므로 $G$의 해당 행들을 XOR하면 된다.



$$
[0\ 1\ 1\ 1\ 0\ 0\ 0]
$$





$$
\oplus [1\ 1\ 0\ 0\ 0\ 1\ 0]
$$





$$
\oplus [1\ 1\ 1\ 0\ 0\ 0\ 1]
$$



결과는



$$
\boxed{ \mathbf{c} = [0\ 1\ 0\ 1\ 0\ 1\ 1] }
$$



이다.

현재 $G=[P^T\mid I_4]$ 형태이므로 Codeword의 뒤쪽 네 비트가 원본 데이터이다.



```text
Codeword

0 1 0 | 1 0 1 1
─────   ───────
Parity  Original Data
```



즉



$$
\mathbf{u}=[1\ 0\ 1\ 1]
$$



이 그대로 포함되어 있다.

---



## 10. 정상 Codeword인지 확인하기

정상 Codeword라면



$$
H\mathbf{c}^T=0
$$



이어야 한다.

현재



$$
\mathbf{c} = [0\ 1\ 0\ 1\ 0\ 1\ 1]
$$



이므로



$$
H\mathbf{c}^T = \begin{bmatrix} 1&0&0&0&1&1&1\\ 0&1&0&1&0&1&1\\ 0&0&1&1&1&0&1 \end{bmatrix} \begin{bmatrix} 0\\ 1\\ 0\\ 1\\ 0\\ 1\\ 1 \end{bmatrix}
$$



Modulo-2 연산을 수행하면



$$
= \begin{bmatrix} 0\\ 0\\ 0 \end{bmatrix}
$$



이다.

따라서 정상적인 Codeword임을 확인할 수 있다.

---



## 11. Syndrome이란?

수신단에서는 수신 데이터 $\mathbf{r}$에 $H$를 곱한다.



$$
\boxed{ \mathbf{s}=H\mathbf{r}^T }
$$



이때 나오는



$$
\mathbf{s}
$$



를 **Syndrome**이라고 한다.

Hamming $(7,4)$에서는



$$
H:3\times7
$$



이고



$$
\mathbf{r}^T:7\times1
$$



이므로



$$
\mathbf{s}:3\times1
$$



이 된다.

즉 Syndrome은 3비트이다.



```text
Received Word
     r
     ↓
     H
     ↓
Syndrome

s = Hrᵀ
```



---



## 12. 에러가 없는 경우

에러 벡터를 $\mathbf{e}$라고 하면



$$
\mathbf{r} = \mathbf{c}+\mathbf{e}
$$



이다.

에러가 없다면



$$
\mathbf{e}=0
$$



이므로



$$
\mathbf{r}=\mathbf{c}
$$



이다.

따라서



$$
\mathbf{s} = H\mathbf{r}^T
$$





$$
= H\mathbf{c}^T
$$





$$
= 0
$$



이다.

즉



$$
\boxed{ \mathbf{s}=0 }
$$



이면 정상 Codeword이다.

---



## 13. 에러가 있는 경우

채널에서 에러가 발생했다고 하자.



$$
\mathbf{r} = \mathbf{c}+\mathbf{e}
$$



Syndrome을 계산하면



$$
\mathbf{s} = H\mathbf{r}^T
$$





$$
= H(\mathbf{c}+\mathbf{e})^T
$$





$$
= H\mathbf{c}^T+H\mathbf{e}^T
$$



그런데 정상 Codeword에서는



$$
H\mathbf{c}^T=0
$$



이므로



$$
\boxed{ \mathbf{s}=H\mathbf{e}^T }
$$



만 남는다.

즉 Syndrome에는 **정상 Codeword의 정보는 사라지고 에러에 관한 정보만 남게 된다.**

이것이 Syndrome Error Detection의 핵심이다.

---



## 14. 왜 Syndrome으로 에러 위치를 알 수 있는가?

한 비트만 오류가 발생했다고 가정하자.

예를 들어 세 번째 비트에 오류가 발생하면



$$
\mathbf{e} = [0\ 0\ 1\ 0\ 0\ 0\ 0]
$$



이다.

그러면



$$
\mathbf{s} = H\mathbf{e}^T
$$



이다.

$\mathbf{e}$의 세 번째 원소만 1이므로 행렬 곱셈 결과는 결국 **$H$의 세 번째 Column**만 남는다.

즉



$$
\mathbf{s} = \text{3번째 column of }H
$$



이다.

현재 $H$의 세 번째 열은



$$
\begin{bmatrix} 0\\ 0\\ 1 \end{bmatrix}
$$



이므로



$$
\boxed{ \mathbf{s} = \begin{bmatrix} 0\\ 0\\ 1 \end{bmatrix} }
$$



이 된다.

따라서 Syndrome과 $H$의 각 Column을 비교하면 어느 위치에서 오류가 발생했는지 알 수 있다.

---



## 15. 실제 1-bit Error Correction 예제

정상 Codeword가



$$
\mathbf{c} = [0\ 1\ 0\ 1\ 0\ 1\ 1]
$$



이라고 하자.

세 번째 비트에 오류가 발생했다고 가정하면



$$
\mathbf{e} = [0\ 0\ 1\ 0\ 0\ 0\ 0]
$$



이다.

수신 데이터는



$$
\mathbf{r} = \mathbf{c}\oplus\mathbf{e}
$$



이므로



$$
\mathbf{r} = [0\ 1\ 1\ 1\ 0\ 1\ 1]
$$



이다.

이제 Syndrome을 계산한다.



$$
\mathbf{s} = H\mathbf{r}^T
$$





$$
= \begin{bmatrix} 0\\ 0\\ 1 \end{bmatrix}
$$



이다.

$H$를 다시 보면



$$
H= \begin{bmatrix} \color{gray}{1}&\color{gray}{0}&\boxed{0}&0&1&1&1\\ \color{gray}{0}&\color{gray}{1}&\boxed{0}&1&0&1&1\\ \color{gray}{0}&\color{gray}{0}&\boxed{1}&1&1&0&1 \end{bmatrix}
$$



Syndrome



$$
\begin{bmatrix} 0\\ 0\\ 1 \end{bmatrix}
$$



은 정확히 $H$의 **세 번째 Column**과 같다.

따라서

> 세 번째 비트에서 오류가 발생했다.

고 판단한다.

세 번째 비트를 다시 반전하면



$$
[0\ 1\ \boxed{1}\ 1\ 0\ 1\ 1]
$$



에서



$$
[0\ 1\ \boxed{0}\ 1\ 0\ 1\ 1]
$$



이 되어 원래 Codeword를 복구할 수 있다.

---



## 16. Syndrome의 핵심 원리

정리하면



$$
\boxed{ \mathbf{s}=H\mathbf{r}^T }
$$



이고



$$
\mathbf{r} = \mathbf{c}+\mathbf{e}
$$



이므로



$$
\mathbf{s} = H(\mathbf{c}+\mathbf{e})^T
$$





$$
= H\mathbf{c}^T+H\mathbf{e}^T
$$





$$
= H\mathbf{e}^T
$$



이다.

따라서 다음과 같이 생각하면 된다.



```text
정상 Codeword
      ↓
Hcᵀ = 0

수신 데이터
r = c + e
      ↓
Hrᵀ
      ↓
Hcᵀ + Heᵀ
      ↓
0 + Heᵀ
      ↓
Syndrome
```



Hamming $(7,4)$에서 **단일 비트 오류**가 발생하면 Syndrome은 오류 위치에 해당하는 $H$의 Column이 된다.

---



## 17. 중요한 주의점

다음 설명은 모든 Block Code에 무조건 적용되는 것은 아니다.

> Syndrome이 0이 아니면 오류가 있다는 것은 일반적인 Linear Block Code에서 사용할 수 있지만, Syndrome이 곧바로 특정 비트의 위치를 나타내는 것은 Code 구조와 가정에 따라 달라진다.

이번 Hamming $(7,4)$ 예제에서는 $H$의 각 Column이 서로 다른 non-zero 3-bit pattern으로 구성되어 있기 때문에 **1-bit error의 위치를 유일하게 찾을 수 있다.**

따라서 이번 예제의 핵심은



$$
\boxed{\text{Hamming Code + Single-bit Error}}
$$



이다.

여러 비트가 동시에 오류가 나는 경우에는 단순히 하나의 Column과 Syndrome을 매칭하는 방법만으로 정확한 위치를 결정할 수 없다.

---



## 18. 송신부터 오류 정정까지 전체 흐름

지금까지의 과정을 한 번에 연결하면 다음과 같다.



```text
Information bits
u = [1011]
      ↓
Generator Matrix G
      ↓
c = uG
      ↓
Codeword
c = [0101011]
      ↓
Channel
      ↓
Error e 발생
      ↓
r = c + e
      ↓
Received Word
      ↓
Parity-Check Matrix H
      ↓
s = Hrᵀ
      ↓
Syndrome
      ↓
H의 Column과 비교
      ↓
Error Position 검출
      ↓
해당 bit 반전
      ↓
Codeword 복구
      ↓
Information bits 복원
```



---



## 19. $G$와 $H$의 역할 비교

| 구분 | Generator Matrix $G$ | Parity-Check Matrix $H$ |
| --- | --- | --- |
| 사용 위치 | 송신단 | 수신단 |
| 목적 | Codeword 생성 | Codeword 검사 |
| 크기 | $k\times n$ | $(n-k)\times n$ |
| 입력 | Information vector $\mathbf{u}$ | Received vector $\mathbf{r}$ |
| 계산 | $\mathbf{c}=\mathbf{u}G$ | $\mathbf{s}=H\mathbf{r}^T$ |
| 결과 | $n$ bit Codeword | $(n-k)$ bit Syndrome |

그리고 두 행렬은



$$
\boxed{HG^T=0}
$$



의 관계를 가진다.

---



## 20. Block Coding의 핵심

Block Coding의 핵심은 단순히 parity bit를 추가하는 것이 아니다.

송신단에서



$$
\mathbf{c}=\mathbf{u}G
$$



를 이용해 **특정한 규칙을 만족하는 Codeword만 만들어 전송**하고,

수신단에서는



$$
\mathbf{s}=H\mathbf{r}^T
$$



를 이용해 수신된 데이터가 그 규칙을 만족하는지 검사한다.

즉 Codeword는 전체 $2^n$개의 가능한 n-bit 조합 중 아무 조합이나 사용하는 것이 아니라, **$H\mathbf{c}^T=0$을 만족하는 제한된 Codeword 집합만 사용한다.**

채널 오류로 비트가 바뀌면 이 규칙에서 벗어나기 때문에 오류를 검출할 수 있는 것이다.

---



## 21. Block Coding과 Convolution Coding

Channel Coding의 또 다른 대표적인 방식은 **Convolution Coding**이다.

두 방법의 가장 큰 차이는 **메모리의 존재 여부**이다.

### Block Coding

Block Coding은 데이터를 일정한 $k$ bit 단위로 끊어서 각각 독립적으로 처리한다.



```text
Data Stream

1011 | 0010 | 1101 | ...
 ↓       ↓       ↓
Code    Code    Code
```



현재 Block의 Encoding에는 이전 Block의 정보가 직접적으로 영향을 주지 않는다.

---



### Convolution Coding

Convolution Coding은 데이터를 독립적인 Block으로 나누기보다 연속적인 Bit Stream으로 처리한다.

Encoder 내부에는 **Shift Register**가 존재한다.



```text
Input
  ↓
Shift Register
  ↓
현재 bit + 이전 bit
  ↓
XOR
  ↓
Encoded Output
```



따라서 현재 출력은

> 현재 입력 + 과거 입력 상태

에 의해 결정된다.

즉 Convolution Coding은 **Memory를 가진 Code**라고 볼 수 있다.

---



## 22. Convolution Encoder의 동작

입력 비트가 하나씩 들어오면 Shift Register의 값이 이동한다.

예를 들어



```text
현재 입력
   ↓
[현재][과거1][과거2]
        ↓
     XOR 조합
        ↓
  여러 개의 출력 bit
```



와 같이 동작한다.

따라서 동일한 현재 입력 비트가 들어오더라도 과거 상태가 다르면 출력 결과가 달라질 수 있다.

이 상태 변화를 시간에 따라 표현한 것이 **Trellis Diagram**이다.

---



## 23. Convolution Coding의 Decoder

Convolution Coding에서는 대표적으로 **Viterbi Decoder**를 사용한다.

Viterbi Algorithm은 수신된 Bit Stream을 보고

> Encoder가 실제로 지나갔을 가능성이 가장 높은 상태 경로

를 찾는다.

주요 개념은 다음과 같다.

#### Trellis Diagram

Encoder에서 가능한 모든 State Transition을 시간에 따라 나타낸 것이다.

#### Hamming Distance

수신된 비트와 예상되는 비트가 몇 자리 다른지를 나타낸다.

예를 들어



```text
Received : 11
Expected : 10
```



이면 한 비트가 다르므로 Hamming Distance는



$$
1
$$



이다.

---



## 24. Viterbi Decoder의 동작

Viterbi Decoder의 핵심은

**Add → Compare → Select**

과정이다.

### ① Branch Metric

현재 수신된 비트와 각 경로에서 예상되는 출력 비트를 비교한다.



```text
Received  : 11
Candidate : 10

Hamming Distance = 1
```



이 값을 **Branch Metric**이라고 한다.

---



### ② Path Metric

현재 Branch Metric을 이전까지 누적된 오류 값에 더한다.



$$
\text{New Path Metric} = \text{Previous Path Metric} + \text{Branch Metric}
$$



---



### ③ Compare & Select

같은 State에 도착하는 여러 경로가 있다면 Path Metric을 비교한다.

오류가 더 적은 경로를 남기고 나머지는 제거한다.

남겨진 경로를

**Survivor Path**

라고 한다.

---



### ④ Traceback

이 과정을 계속 반복한 후 최종적으로 가장 가능성이 높은 Survivor Path를 역으로 따라간다.

이를

**Traceback**

이라고 한다.

Traceback을 통해 원래 전송되었을 가능성이 가장 높은 입력 비트열을 복원한다.

---



## 25. Block Coding vs Convolution Coding

| 구분 | Block Coding | Convolution Coding |
| --- | --- | --- |
| 입력 형태 | 일정한 $k$ bit Block | 연속 Bit Stream |
| Memory | 없음 | 있음 |
| 과거 입력 영향 | 없음 | 있음 |
| Encoder | Generator Matrix $G$ | Shift Register + XOR |
| Decoder | Parity-Check Matrix $H$, Syndrome | Viterbi Decoder 등 |
| 오류 판단 | Syndrome | Path Metric |
| 대표 구조 | Hamming Code 등 | Convolutional Code |
| 핵심 개념 | $H\mathbf{c}^T=0$ | Trellis / Survivor Path |

---



## 26. Source Coding과 Channel Coding의 차이

Digital Communication 전체 구조에서 헷갈리기 쉬운 부분이다.

### Source Coding

Source Coding은 정보를 효율적으로 표현하기 위해 **불필요한 중복을 줄이는 것**이 주요 목적이다.



```text
Information
    ↓
Source Coding
    ↓
보다 효율적인 Bit Representation
```



### Channel Coding

Channel Coding은 채널 오류에 견디기 위해 **의도적으로 구조화된 중복을 추가**한다.



```text
Bit Stream
    ↓
Channel Coding
    ↓
Information + Redundancy
```



따라서 두 과정은 목적이 다르다.



```text
Source Coding  → redundancy 감소
Channel Coding → redundancy 추가
```



---



## 27. 전체 통신 과정에서 Channel Coding 위치

Digital Communication System에서 보면



```text
Information Source
        ↓
Formatting
        ↓
Source Coding
        ↓
Channel Coding
        ↓
Modulation
        ↓
Channel
        ↓
Demodulation / Detection
        ↓
Channel Decoding
        ↓
Source Decoding
        ↓
Destination
```



의 흐름을 가진다.

Channel Coding은 **채널을 통과하면서 발생할 오류를 복구할 수 있도록 송신 전에 데이터를 준비하는 과정**이라고 볼 수 있다.

---



## 28. 핵심 식 정리

Block Coding에서 가장 중요한 식은 다음과 같다.

#### Encoding



$$
\boxed{ \mathbf{c}=\mathbf{u}G }
$$



#### Generator Matrix와 Parity-Check Matrix의 관계



$$
\boxed{ HG^T=0 }
$$



#### 정상 Codeword



$$
\boxed{ H\mathbf{c}^T=0 }
$$



#### 채널 오류



$$
\boxed{ \mathbf{r}=\mathbf{c}+\mathbf{e} }
$$



#### Syndrome



$$
\boxed{ \mathbf{s}=H\mathbf{r}^T }
$$



그리고



$$
\mathbf{s} = H(\mathbf{c}+\mathbf{e})^T
$$





$$
= H\mathbf{c}^T+H\mathbf{e}^T
$$





$$
= H\mathbf{e}^T
$$



이므로



$$
\boxed{ \mathbf{s}=H\mathbf{e}^T }
$$



이다.

---



## 29. 한 번에 이해하기

Block Coding을 한 문장으로 정리하면 다음과 같다.

> 송신단에서는 Generator Matrix $G$를 이용해 오류 검사가 가능한 Codeword를 만들고, 수신단에서는 Parity-Check Matrix $H$를 이용해 Syndrome을 계산하여 Codeword의 오류 여부를 판단한다.

Hamming $(7,4)$에서 1-bit error가 발생하면

> Syndrome = Error가 발생한 위치에 해당하는 $H$의 Column

이 되므로 위치를 찾아 해당 bit를 반전할 수 있다.

Convolution Coding은 이와 달리

> 현재 입력뿐 아니라 과거 입력 상태까지 함께 Encoding하고, 수신단에서 Trellis와 Viterbi Algorithm을 이용해 가장 가능성이 높은 원래 경로를 찾는다.

---



## 핵심 정리

- **Channel Coding**은 채널 오류를 검출·정정하기 위해 구조화된 redundancy를 추가하는 과정이다.
- **Block Coding**은 데이터를 일정한 $k$ bit 단위로 처리하여 $n$ bit Codeword를 만든다.
- $(n,k)$ Code에서는 $n-k$개의 redundancy bit가 추가된다.
- 송신단에서는 **Generator Matrix $G$** 를 사용한다.



$$
\mathbf{c}=\mathbf{u}G
$$



- 수신단에서는 **Parity-Check Matrix $H$** 를 사용한다.



$$
\mathbf{s}=H\mathbf{r}^T
$$



- 정상 Codeword는



$$
H\mathbf{c}^T=0
$$



을 만족한다.

- $G$와 $H$는



$$
HG^T=0
$$



을 만족하도록 구성한다.

- Hamming $(7,4)$의 단일 비트 오류에서는 Syndrome이 $H$의 해당 오류 위치 Column과 일치한다.
- **Convolution Coding**은 Shift Register를 사용해 현재 입력과 과거 입력을 함께 Encoding한다.
- Convolution Code는 **Trellis Diagram과 Viterbi Algorithm**을 이용해 Decoding할 수 있다.
- Viterbi Decoder에서는 Branch Metric → Path Metric → Compare & Select → Traceback 과정으로 원본 데이터를 추정한다.

---



### 한 문장 요약

> **Block Coding은 $G$로 올바른 Codeword를 만들고 $H$와 Syndrome으로 오류를 찾아내며, Convolution Coding은 과거 상태를 포함해 Encoding한 뒤 Trellis상의 최적 경로를 추적하여 데이터를 복원하는 방식이다.**

---

블록도 기준: [04. 채널 부호화](../../blocks/channel-encoding.md) · [전체 로드맵](../../roadmap.md)
