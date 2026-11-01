# Digital Communication Handbook

디지털 통신 시스템을 **정보원 → 송신기 → 채널 → 수신기 → 목적지** 흐름으로 읽는 학습 자료이다.

[블록도와 학습 로드맵](roadmap.md) · [전체 구조 이해하기](theory/foundations/communication-system-overview.md)

## 송신기 · 1~10

포맷팅 → 소스 부호화 → 암호화 → 채널 부호화 → 다중화 → 펄스 변조 → 대역통과 변조 → 주파수 확산 → 다중 접속 → 송신기

[01. 포맷팅부터 시작하기](blocks/format-input.md)

## 채널 · 11

[채널·잡음·Fading·BER·Shannon Capacity](blocks/channel.md)

## 수신기 · 12~21

수신기 → 다중 접속 → 주파수 역확산 → 복조 및 샘플링 → 검파 → 역다중화 → 채널 복호화 → 복호화 → 소스 복호화 → 포맷팅·정보 복원

[12. 수신기부터 읽기](blocks/receiver.md)

## 공통 과정과 응용

- [동기화](blocks/synchronization.md)는 송수신 전반에 걸친 공통 과정이다.
- [Bio/Implant Communication](applications/bio-implant/bio-and-implant-communication.md)으로 통신의 목적과 설계 제약을 연결한다.

왼쪽 목차의 01~21번은 로드맵의 블록 번호와 같다. 각 단계의 개요에서 관련 상세 글로 이동하고, 오른쪽 목차에서 글 안의 세부 항목을 탐색한다.
