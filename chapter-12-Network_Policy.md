# Network Policy

## 📘 Chapter 12 — Network Policy

### 🎯 핵심 메시지

이 챕터는 **쿠버네티스 네트워크 정책의 한계와 Cilium이 이를 어떻게 확장하고 강화하는지**를 설명
기본 Kubernetes NetworkPolicy는 기능이 제한적이지만, Cilium은 eBPF 기반으로 **L3/L4/L7 정책, DNS 정책, FQDN 정책, Clusterwide 정책, 정책 관측성** 등을 제공해 완전한 Zero Trust 모델을 구현할 수 있슴.

## 🧩 1. Kubernetes 기본 NetworkPolicy의 한계

Kubernetes의 기본 NetworkPolicy는 다음과 같은 제약이 있슴

- **L3/L4 수준만 제어 가능**
(IP, 포트 기반)
- **L7 제어 불가능**
(HTTP method, path, header 등)
- **DNS 기반 정책 불가능**
- **정책 적용 범위가 네임스페이스 단위로 제한**
- **정책 충돌·우선순위 개념 없음**
- **관측성 부족**

Chapter 12는 이 한계를 해결하기 위해 Cilium이 제공하는 확장 기능을 설명

## 🚀 2. CiliumNetworkPolicy(CNP)의 확장 기능

Cilium은 eBPF + Envoy 기반으로 다음 기능을 제공

### **1) L7 정책 지원 (HTTP, gRPC, Kafka 등)**

- HTTP method
- path prefix
- header 기반 필터링
- gRPC 서비스/메서드 제어

### **2) DNS 기반 정책**

- 특정 도메인만 허용
- DNS 요청을 추적해 자동으로 IP 매핑

### **3) FQDN 기반 Egress 정책**

- 외부 API 접근 제어
- SaaS 서비스 접근 제한

### **4) Clusterwide 정책(CCNP)**

- 클러스터 전체에 정책 적용
- 멀티클러스터에서도 일관성 유지

### **5) 정책 우선순위 및 병합 모델**

- 여러 정책이 있을 때 충돌 없이 병합
- default deny → explicit allow 구조

### **6) 정책 관측성(Hubble)**

- 어떤 정책이 어떤 트래픽을 허용/차단했는지 시각화
- 정책 디버깅이 매우 쉬움

## 🔐 3. Zero Trust 모델 구현

Chapter 12는 Cilium이 Zero Trust 네트워크를 구현하는 데 최적이라고 강조

### Zero Trust 구성 요소

- 기본 차단(default deny)
- 라벨 기반 접근 제어
- L7 기반 세밀한 제어
- 외부 API 접근 제한(FQDN)
- 정책 관측성으로 지속적인 검증

## 🧭 4. 정책 설계 패턴

Chapter 12는 다음과 같은 정책 설계 패턴을 소개

### **1) 기본 차단(Default deny) + 명시적 허용**

가장 권장되는 패턴.

### **2) 서비스 간 통신 최소 허용**

- frontend → backend
- backend → database
- 그 외 모든 lateral movement 차단

### **3) 팀/네임스페이스 기반 정책 분리**

- 플랫폼팀
- ML팀
- 데이터팀
- 보안팀

### **4) L7 기반 API 보호**

- 인증 엔드포인트만 허용
- 민감한 API는 내부 서비스만 접근 가능

### **5) Egress 정책과 결합**

- 외부 API 접근 제한
- SaaS 서비스 접근 제어

## 🧩 5. Cilium 정책의 실제 적용 예시

Chapter 12에서는 다음과 같은 실제 예시를 다룸

- HTTP method/path 기반 정책
- gRPC 서비스 기반 정책
- DNS 기반 정책
- FQDN 기반 Egress 정책
- Clusterwide 정책
- 정책 관측성(Hubble) 활용

## 📊 6. 정책 관측성(Hubble)

정책이 제대로 적용되었는지 확인하는 것이 중요

Hubble을 통해 다음을 확인할 수 있슴

- 어떤 정책이 어떤 트래픽을 허용/차단했는지
- 정책 충돌 여부
- L7 요청이 어떤 규칙에 의해 처리되었는지
- Egress 정책이 어떤 도메인에 적용되었는지

## 📝 Chapter 12 핵심 요약

- Kubernetes 기본 NetworkPolicy는 기능이 제한적
- Cilium은 L3/L4/L7 정책, DNS, FQDN, Clusterwide 정책을 제공
- Zero Trust 네트워크 모델을 완전하게 구현 가능
- 정책 병합·우선순위·관측성까지 제공
- 멀티클러스터·대규모 조직·ML/AI 워크로드에서도 강력한 정책 모델