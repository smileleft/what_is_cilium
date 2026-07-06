# Cluster Access

## 📘 Chapter 10 — Cluster Access

### 🎯 핵심 메시지

이 챕터는 **쿠버네티스 클러스터에 안전하고 일관되게 접근하는 방법**을 설명.
특히 Cilium이 제공하는 **API 접근 제어, 인증·인가, 네트워크 정책, 외부 접근 경로** 등을 중심으로 클러스터 보안을 강화하는 전략을 다룸.

## 🔐 1. 클러스터 접근이 중요한 이유

쿠버네티스는 기본적으로 다음과 같은 접근 경로를 가짐

- Kubernetes API Server
- Node-level SSH / 관리 인터페이스
- Pod-to-Pod / Pod-to-Service 통신
- 외부 → 클러스터 Ingress/Gateway

Chapter 10은 이 모든 접근 경로를 **일관된 보안 모델로 통제**하는 것이 왜 중요한지 설명함

## 🧩 2. Kubernetes 기본 접근 제어 모델

### **1) 인증(Authentication)**

- X.509 client cert
- ServiceAccount token
- OIDC
- Cloud provider IAM 연동

### **2) 인가(Authorization)**

- RBAC(Role-Based Access Control)
- ABAC
- Node authorizer

### **3) Admission Control**

- PodSecurity
- ValidatingWebhook / MutatingWebhook

Chapter 10은 이 기본 모델 위에 **Cilium이 어떻게 네트워크 레벨 접근 제어를 결합하는지** 설명

## 🛡️ 3. Cilium이 제공하는 접근 제어

Cilium은 L3/L4/L7 수준에서 접근을 제어할 수 있슴

### **1) CiliumNetworkPolicy (CNP)**

- Pod 간 통신을 라벨 기반으로 제어
- L3/L4/L7 정책을 하나의 리소스로 통합
- HTTP method, path 기반 제어 가능

### **2) CiliumClusterwideNetworkPolicy (CCNP)**

- 클러스터 전체에 적용되는 정책
- 멀티클러스터 환경에서도 일관성 유지

### **3) Identity 기반 접근 제어**

- Pod의 라벨을 기반으로 고유 ID 부여
- 네트워크 정책이 라벨 기반으로 자동 적용
- 멀티클러스터에서도 동일한 ID 모델 사용

## 🌐 4. 외부에서 클러스터로 접근하는 경로

Chapter 10은 외부 접근을 크게 두 가지로 나눈다.

### **1) API Server 접근**

- kubectl, CI/CD, GitOps 시스템 등이 접근
- OIDC + RBAC 조합을 권장
- 네트워크 레벨에서 Cilium 정책으로 접근 제한 가능

### **2) 애플리케이션 트래픽 접근**

- Ingress
- Gateway API
- NodePort / LoadBalancer
- Cilium L7 프록시(Envoy)

Cilium은 이 경로들에 대해 **세밀한 L7 접근 제어**를 제공.

## 🔒 5. Zero Trust 접근 모델

Chapter 10은 Cilium이 Zero Trust 모델을 구현하는 데 적합하다고 강조.

### Zero Trust 구성 요소

- **모든 요청은 인증·인가 필요**
- **네트워크 정책으로 기본 차단(default deny)**
- **서비스 간 통신도 mTLS 적용 가능**
- **정책은 라벨 기반으로 자동 적용**

Cilium은 eBPF 기반으로 이 모델을 **고성능으로 구현**.

## 🧭 6. 운영 패턴

### **1) 개발자 접근 제어**

- 개발자에게 필요한 최소 권한만 부여
- 네임스페이스 단위 RBAC
- 네트워크 정책으로 개발용 서비스만 접근 허용

### **2) 운영팀 접근 제어**

- 클러스터 전체 관리 권한
- Cilium 정책으로 운영 도구 접근 보호

### **3) 외부 시스템 접근 제어**

- GitOps, CI/CD 시스템의 API 접근을 RBAC + Cilium 정책으로 보호

## 📝 Chapter 10 핵심 요약

- 클러스터 접근은 인증·인가·네트워크 정책이 결합된 모델로 보호해야 함
- Cilium은 L3/L4/L7 정책을 통해 접근 제어를 강화
- Identity 기반 접근 제어로 멀티클러스터에서도 일관성 유지
- Gateway API와 결합해 외부 접근을 안전하게 제어
- Zero Trust 모델을 구현하는 데 최적화된 구조

### 추가로 학습해야 할 내용

- CiliumNetworkPolicy 작성 실습
- Zero Trust를 Cilium으로 구현하는 실제 구성
- Gateway API 기반 접근 제어 설계