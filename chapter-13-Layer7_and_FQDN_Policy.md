# Layer 7 and FQDN Policy

## 📘 Chapter 13 — Layer 7 and FQDN Policy

### 🎯 핵심 메시지

이 챕터는 **Cilium이 제공하는 L7(애플리케이션 레벨) 정책과 FQDN 기반 Egress 정책을 통해 네트워크 보안을 어떻게 강화하는지**를 설명
기본 Kubernetes NetworkPolicy가 제공하지 못하는 **HTTP/gRPC 기반 제어, DNS/FQDN 기반 제어, 외부 API 접근 통제** 등을 Cilium이 어떻게 구현하는지 집중적으로 다룸

## 🧩 1. 왜 L7 정책이 필요한가

기본 Kubernetes NetworkPolicy는 L3/L4(IP·포트)만 제어할 수 있슴

- HTTP method 제어 불가
- URL path 제어 불가
- Header 기반 제어 불가
- gRPC 서비스/메서드 제어 불가

즉, **애플리케이션 레벨 보안이 불가능**

Chapter 13은 Cilium이 Envoy 프록시와 eBPF를 결합해 이 문제를 해결하는 과정을 설명

## 🚀 2. Cilium의 L7 정책 기능

Cilium은 Envoy L7 프록시를 통해 다음을 제어할 수 있슴

### **HTTP 정책**

- method(GET/POST/DELETE 등)
- path prefix(`/api/v1/*`)
- header 조건(Content-Type 등)
- host 기반 제어

### **gRPC 정책**

- 서비스 이름
- 메서드 이름
- 특정 RPC만 허용

### **Kafka 정책**

- topic 기반 제어
- producer/consumer 분리

### **TLS 정책**

- SNI 기반 제어
- mTLS 기반 인증

이 모든 정책은 **CiliumNetworkPolicy(CNP)** 안에서 정의됨

## 🔐 3. FQDN 기반 Egress 정책

Chapter 13의 또 다른 핵심은 **FQDN 기반 외부 접근 제어**

### 왜 중요한가?

- 외부 API 접근을 도메인 단위로 제한
- SaaS 서비스(OpenAI, Stripe, GitHub 등) 접근 통제
- 데이터 유출 방지
- 규제 준수(특정 도메인만 허용)

### 동작 방식

1. Pod가 DNS 요청을 보냄
2. Cilium이 DNS 응답을 가로채서 IP를 정책에 자동 매핑
3. FQDN 정책이 해당 IP로의 트래픽만 허용
4. IP가 바뀌어도 정책이 자동 유지됨

## 🧭 4. L7 정책과 FQDN 정책의 결합

Chapter 13은 L7 정책과 FQDN 정책을 결합하면 **완전한 Zero Trust 모델**을 만들 수 있다고 설명

예:

- 내부 API는 HTTP method/path로 제어
- 외부 API는 FQDN으로 제어
- 내부·외부 모두 기본 차단(default deny)
- 필요한 경로만 명시적으로 허용

이 조합은 특히 **ML/AI, 금융, 규제 산업**에서 강력한 보안 모델을 제공.

## 🧩 5. 대표적인 정책 예시 (개념적)

### L7 HTTP 정책 예시

- `/api/v1/orders` POST만 허용
- `/admin/*`은 내부 서비스만 접근 가능
- `/metrics`는 Prometheus만 접근 가능

### FQDN 정책 예시

- `api.openai.com`만 허용
- `.s3.amazonaws.com`만 허용
- `huggingface.co`만 허용
- 나머지 모든 외부 접근 차단

## 📊 6. 관측성(Hubble)

Chapter 13은 L7 정책과 FQDN 정책이 **Hubble을 통해 완전히 관측 가능**하다는 점도 강조.

Hubble을 통해:

- 어떤 HTTP 요청이 어떤 L7 규칙에 의해 허용/차단되었는지
- 어떤 FQDN 정책이 어떤 외부 도메인 접근을 허용했는지
- 정책 충돌 여부
- L7 프록시(Envoy)에서 발생한 이벤트

를 모두 확인할 수 있슴.

## 📝 Chapter 13 핵심 요약

- Kubernetes 기본 NetworkPolicy는 L7 제어가 불가능
- Cilium은 Envoy 기반 L7 정책으로 HTTP/gRPC/Kafka/TLS까지 제어
- FQDN 기반 Egress 정책으로 외부 API 접근을 도메인 단위로 제한
- L7 + FQDN 조합으로 완전한 Zero Trust 모델 구현 가능
- Hubble을 통해 정책이 실제로 어떻게 적용되는지 관측 가능
- ML/AI, 금융, SaaS-heavy 환경에서 특히 중요