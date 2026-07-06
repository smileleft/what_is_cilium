# Cluster Egress

## 📘 Chapter 11 — Cluster Egress

### 🎯 핵심 메시지

이 챕터는 **쿠버네티스 클러스터에서 외부(인터넷, 외부 API, SaaS 서비스 등)로 나가는 트래픽을 어떻게 제어하고 보호할 것인가**를 설명.
특히 Cilium이 제공하는 **eBPF 기반 Egress 정책, NAT 관리, FQDN 정책, Egress Gateway** 등을 중심으로 다룬다.

## 🌐 1. 왜 Egress 제어가 중요한가

클러스터 내부에서 외부로 나가는 트래픽은 다음과 같은 위험을 가짐

- Pod가 임의의 외부 서비스로 통신할 수 있음
- 데이터 유출 위험
- 규제 준수(예: 특정 도메인만 허용) 필요
- 비용 관리(불필요한 외부 API 호출 차단)
- 보안 정책을 네임스페이스·팀 단위로 분리해야 함

그래서 **Egress 제어는 Zero Trust 모델의 필수 요소**

## 🔐 2. Cilium의 Egress 제어 모델

Cilium은 eBPF 기반으로 다음 기능을 제공

### **1) FQDN 기반 Egress 정책**

- 특정 도메인만 허용
- DNS 요청을 추적하여 IP를 자동 매핑
- 예: `api.openai.com`만 허용

### **2) Egress NAT**

- Pod → 외부로 나갈 때 NAT IP를 통제
- 팀/네임스페이스별 NAT IP 분리 가능
- 감사·추적·보안에 유리

### **3) Egress Gateway**

- 외부 트래픽을 특정 노드로 강제 라우팅
- 방화벽, 프록시, 보안 장비와 연동 가능
- 규제 산업에서 필수

### **4) L3/L4/L7 정책 결합**

- 외부로 나가는 트래픽도 포트·프로토콜·HTTP 기반으로 제어 가능

## 🧩 3. FQDN 기반 Egress 정책

Chapter 11에서 가장 강조하는 기능 중 하나.

### 특징

- DNS 기반으로 외부 도메인 접근을 제어
- IP가 바뀌어도 자동으로 정책이 유지
- SaaS API 접근 제어에 최적화

### 예시

- `.github.com` 허용
- `api.stripe.com`만 허용
- 나머지 모든 외부 접근 차단

## 🚪 4. Egress Gateway

Egress Gateway는 **클러스터 외부로 나가는 트래픽을 특정 노드로 강제 우회시키는 기능**.

### 왜 필요한가

- 방화벽 규칙을 단일 노드에만 적용하면 됨
- 외부로 나가는 트래픽을 중앙 집중적으로 모니터링
- 규제 준수(예: 금융, 공공기관)
- NAT IP를 고정할 수 있음

### 동작 방식

1. Pod → 외부로 나가는 트래픽
2. Cilium이 Egress Gateway 노드로 라우팅
3. Gateway 노드에서 NAT 수행
4. 외부로 전송

## 🔀 5. Egress NAT

Cilium은 eBPF 기반 NAT을 제공하여 다음을 가능하게 함

- Pod → 외부로 나갈 때 NAT IP를 통제
- 팀별 NAT IP 분리
- 감사 로그에서 “어떤 팀이 어떤 외부 API를 호출했는지” 추적 가능
- 멀티클러스터 환경에서도 NAT 정책 유지

## 🔒 6. Zero Trust Egress 모델

Chapter 11은 Egress 제어가 Zero Trust의 핵심이라고 강조

### 구성 요소

- 기본 차단(default deny)
- 명시적 도메인/포트만 허용
- NAT IP 고정
- Egress Gateway로 중앙화
- 내부 정책(CiliumNetworkPolicy)와 결합

## 🧭 7. 운영 패턴

### **1) 팀/네임스페이스별 Egress 정책 분리**

- 개발팀: GitHub만 허용
- 데이터팀: S3만 허용
- ML팀: HuggingFace API만 허용

### **2) 규제 산업 패턴**

- 모든 외부 트래픽 → Egress Gateway → 방화벽 → 외부
- NAT IP는 고정
- 감사 로그 필수

### **3) SaaS API 접근 제어**

- Stripe, OpenAI, Slack 등 특정 도메인만 허용
- 비용·보안·규제 모두 충족

## 📝 Chapter 11 핵심 요약

- Egress 제어는 클러스터 보안의 핵심
- Cilium은 FQDN 정책, Egress NAT, Egress Gateway로 강력한 제어 제공
- Zero Trust 모델을 외부 트래픽까지 확장
- 규제 산업, SaaS API 접근, 멀티테넌시 환경에서 필수 기능
- 내부 정책(CNP) + 외부 정책(Egress) 조합으로 완전한 보안 모델 구축 가능

원하면 다음으로

- **FQDN 기반 Egress 정책 예제**
- **Egress Gateway 실제 구성 예시**
- **ML/AI 워크로드에서 Egress 최적화 전략**
- **Zero Trust Egress 전체 아키텍처 설계**

같은 내용도 더 깊게 파고들어줄