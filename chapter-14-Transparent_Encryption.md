# Transparent Encryption

## 📘 Chapter 14 — Transparent Encryption

### 🎯 핵심 메시지

이 챕터는 **쿠버네티스 클러스터 내부 트래픽을 자동으로 암호화하는 방법**, 즉 *Transparent Encryption*을 설명
Cilium은 eBPF 기반으로 **IPsec 또는 WireGuard**를 사용해 **Pod ↔ Pod, Node ↔ Node** 트래픽을 자동 암호화하며, 애플리케이션은 아무 수정 없이 그대로 동작.

## 🔐 1. 왜 Transparent Encryption이 필요한가

쿠버네티스 네트워크는 기본적으로 평문(plaintext)으로 통신.
이 때문에 다음과 같은 위험이 존재:

- 노드 간 트래픽이 스니핑될 수 있음
- Pod ↔ Pod 트래픽이 외부에 노출될 수 있음
- 멀티테넌트 환경에서 데이터 격리가 어려움
- 규제 산업(금융, 공공, 의료)에서 암호화 요구사항 존재

Transparent Encryption은 **네트워크 레벨에서 자동으로 암호화**하여 이 문제를 해결.

## 🚀 2. Cilium Transparent Encryption의 핵심 개념

Cilium은 eBPF 기반으로 다음을 제공

### **1) IPsec 또는 WireGuard 기반 암호화**

- IPsec ESP 모드
- WireGuard 터널
- 둘 중 하나를 선택해 사용 가능

### **2) 애플리케이션 수정 불필요**

- TLS를 직접 구현할 필요 없음
- Pod는 평문으로 통신하지만, 네트워크 레벨에서 자동 암호화

### **3) Node-to-Node, Pod-to-Pod 암호화**

- 모든 east-west 트래픽 보호
- 클러스터 내부 전체 암호화 가능

### **4) eBPF 기반 고성능 암호화**

- 커널 공간에서 암호화/복호화 수행
- 기존 IPsec 대비 오버헤드 감소
- WireGuard는 특히 고성능

## 🧩 3. IPsec vs WireGuard 비교

Chapter 14는 두 방식의 차이를 설명

### **IPsec**

- 오래된 표준, 광범위한 호환성
- ESP 모드 사용
- 성능은 WireGuard보다 낮을 수 있음
- 기존 보안 장비와 통합이 쉬움

### **WireGuard**

- 최신 암호화 기술
- 매우 빠르고 단순한 설계
- 고성능 ML/AI, 대규모 클러스터에 적합
- Cilium에서 점점 WireGuard가 기본 선택이 되는 추세

## 🔀 4. 암호화 동작 방식

1. Pod가 평문 패킷을 전송
2. Cilium eBPF 프로그램이 패킷을 가로채 암호화
3. 암호화된 패킷이 네트워크를 통해 전달
4. 목적지 노드에서 복호화
5. Pod는 평문 패킷을 받음

애플리케이션은 암호화를 전혀 인지하지 못함

## 🛡️ 5. Transparent Encryption과 정책

암호화는 네트워크 정책과 결합해 더 강력한 보안을 제공.

- L3/L4/L7 정책 + 암호화
- 멀티클러스터 환경에서도 암호화 유지
- Zero Trust 모델 완성
- Egress 정책과도 결합 가능

## 🧭 6. 운영 고려사항

### **1) 성능 영향**

- WireGuard는 IPsec보다 성능이 좋음
- CPU 오버헤드가 발생할 수 있으므로 노드 리소스 고려 필요

### **2) 키 관리**

- Cilium이 자동으로 키를 회전
- 수동 관리 필요 없음

### **3) 멀티클러스터**

- Cluster Mesh와 결합해 클러스터 간 암호화 가능

### **4) 관측성**

- Hubble은 암호화된 트래픽도 정상적으로 관측 가능
- 패킷 내용은 보이지 않지만 흐름(flow)은 보임

## 📝 Chapter 14 핵심 요약

- Transparent Encryption은 클러스터 내부 트래픽을 자동으로 암호화
- IPsec 또는 WireGuard 기반
- 애플리케이션 수정 없이 보안 강화
- Node-to-Node, Pod-to-Pod 트래픽 모두 보호
- Zero Trust 모델의 핵심 구성 요소
- 성능·운영·멀티클러스터 고려사항까지 다룸