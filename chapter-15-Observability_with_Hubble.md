# Observability with Hubble

## 📘 Chapter 15 — Observability with Hubble

### 🎯 핵심 메시지

이 챕터는 **Cilium의 관측성 플랫폼인 Hubble이 어떻게 네트워크 흐름을 실시간으로 추적하고, 정책·보안·성능 문제를 분석하는지**를 설명
Hubble은 eBPF 기반으로 **Pod ↔ Pod, Node ↔ Node, L3/L4/L7 흐름**을 모두 관찰할 수 있으며, Zero Trust 네트워크 운영의 핵심 도구

## 🌐 1. 왜 Hubble이 필요한가

쿠버네티스 네트워크는 복잡

- Pod 간 통신 경로가 동적으로 변함
- 정책이 여러 개 겹쳐서 어떤 규칙이 적용됐는지 알기 어려움
- L7 프록시(Envoy)까지 포함되면 디버깅 난이도 증가
- 멀티클러스터 환경에서는 흐름이 더 복잡해짐

Hubble은 이 문제를 해결하기 위해 **네트워크 흐름을 시각화하고, 정책·보안·성능 문제를 실시간으로 보여주는 도구**

## 🚀 2. Hubble의 핵심 구성 요소

Hubble의 아키텍처

### **1) Hubble Agent**

- 각 노드에서 Cilium과 함께 실행
- eBPF로 네트워크 이벤트를 수집
- L3/L4/L7 흐름을 실시간으로 캡처

### **2) Hubble Relay**

- 여러 노드의 이벤트를 중앙에서 집계
- 클러스터 전체 흐름을 단일 API로 제공

### **3) Hubble CLI**

- `hubble observe`, `hubble status`, `hubble flows` 등
- 실시간 디버깅에 사용

### **4) Hubble UI**

- 흐름을 그래프로 시각화
- 서비스 간 통신 관계를 자동으로 보여줌
- 정책 허용/차단 이벤트 표시

## 🔍 3. Hubble이 제공하는 관측 기능

### **1) L3/L4 흐름 추적**

- 어떤 Pod가 어떤 Pod로 통신했는지
- TCP/UDP 포트 기반 흐름
- 허용/차단 여부

### **2) L7 흐름 추적**

- HTTP method/path
- gRPC 서비스/메서드
- Kafka topic
- TLS SNI

### **3) 정책 관측**

- 어떤 CiliumNetworkPolicy가 트래픽을 허용/차단했는지
- 정책 충돌 여부
- Zero Trust 정책 검증

### **4) 보안 이벤트**

- DNS 요청
- FQDN 정책 적용
- 암호화(IPsec/WireGuard) 흐름

### **5) 성능 분석**

- 지연(latency)
- 재전송
- 실패율
- L7 프록시(Envoy) 통계

## 🧭 4. Hubble을 활용한 운영 패턴

실제 운영에서 Hubble을 어떻게 활용하는가?

### **1) 정책 디버깅**

- `hubble observe --verdict DROPPED`
- 어떤 정책이 트래픽을 막았는지 즉시 확인

### **2) 서비스 간 통신 분석**

- Hubble UI에서 서비스 그래프 확인
- 예상치 못한 통신 경로 탐지
- Zero Trust 모델 검증

### **3) L7 API 디버깅**

- HTTP path/method 기반 흐름 확인
- gRPC 호출 실패 원인 분석
- Envoy 필터 동작 확인

### **4) Egress 분석**

- 어떤 Pod가 어떤 외부 도메인에 접근했는지
- FQDN 정책이 제대로 적용됐는지 확인
- 외부 API 호출 비용 분석

### **5) 멀티클러스터 흐름 분석**

- Cluster Mesh 환경에서 cross-cluster 흐름 확인
- 지역 간 지연 분석

## 📊 5. Hubble UI의 주요 기능

- 서비스 간 통신 그래프
- 정책 허용/차단 이벤트 표시
- L7 요청 상세 정보
- 지연/성능 메트릭
- 필터 기반 흐름 검색 (Pod, namespace, label, verdict 등)

## 📝 Chapter 15 핵심 요약

- Hubble은 Cilium의 eBPF 기반 관측성 플랫폼
- L3/L4/L7 흐름을 실시간으로 추적
- 정책 허용/차단을 명확하게 보여줌
- Zero Trust 네트워크 운영의 핵심 도구
- Hubble Relay + Hubble UI로 클러스터 전체 흐름을 시각화
- ML/AI, 금융, 멀티클러스터 환경에서 특히 중요