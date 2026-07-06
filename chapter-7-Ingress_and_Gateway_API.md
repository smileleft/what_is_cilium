# Ingress and Gateway API

## 📘 Chapter 7: Ingress and Gateway API — 핵심 요약

### 🎯 핵심 메시지

Chapter 7은 **쿠버네티스 클러스터 외부 트래픽을 서비스로 안전하고 유연하게 연결하는 방법**을 설명하며, 기존 **Ingress**의 한계를 짚고 **Gateway API**가 이를 어떻게 개선하는지 소개함.
Cilium이 이 두 API를 어떻게 구현하고 확장하는지도 함께 다룸.

## 🧩 1. 왜 Ingress가 필요한가?

- 클러스터 외부 → 내부 서비스로 들어오는 HTTP(S) 트래픽을 제어하는 표준 메커니즘
- 주요 기능
    - 호스트/경로 기반 라우팅
    - TLS 종료
    - 로드밸런싱
- 하지만 **표현력이 제한적**이고, **컨트롤러마다 동작이 달라 일관성이 부족**하다는 문제가 있슴.

## 🧩 2. Ingress의 한계

- 리소스 스펙이 단순해서 복잡한 라우팅 규칙을 표현하기 어려움
- 여러 팀이 공유하기 어려운 구조 (멀티테넌시 부족)
- 확장성 부족
- 구현체마다 동작이 달라 예측하기 어려움
- L7 기능 중심이라 L4/L7을 통합적으로 다루기 어려움

## 🧩 3. Gateway API의 등장

Gateway API는 Ingress의 한계를 해결하기 위해 만들어진 **차세대 쿠버네티스 네트워크 API**.

### 주요 특징

- **표현력 증가**: HTTPRoute, TCPRoute, TLSRoute 등 다양한 리소스
- **역할 기반 모델**:
    - 인프라 팀: Gateway 정의
    - 애플리케이션 팀: Route 정의
- **일관성 있는 스펙**
- **멀티테넌시 지원**
- **확장성**: 필드 확장, 커스텀 필터 등

## 🧩 4. Gateway API 구성 요소

- **GatewayClass**
    - 어떤 구현체(Cilium, Istio, Envoy 등)를 사용할지 정의
- **Gateway**
    - 실제 L4/L7 엔드포인트 (LoadBalancer, NodePort 등)
- **Route 리소스들**
    - HTTPRoute
    - TCPRoute
    - TLSRoute
    - GRPCRoute
- **Policy 리소스**
    - 인증, 라우팅, 트래픽 정책 등을 세밀하게 제어

## 🧩 5. Cilium과 Gateway API

Cilium은 Gateway API를 **네이티브하게 지원**하며 다음을 제공

### Cilium Gateway의 장점

- eBPF 기반 고성능 데이터패스
- Envoy 기반 L7 프록시 통합
- L4/L7 정책을 통합적으로 관리
- 고급 기능
    - mTLS
    - Traffic mirroring
    - Rate limiting
    - Observability (Hubble)

### Cilium Gateway 동작 방식

- GatewayClass → Cilium GatewayClass 사용
- Gateway 생성 → Cilium이 LoadBalancer/NodePort 엔드포인트 생성
- Route 연결 → Envoy로 L7 라우팅 처리
- eBPF → L4 로드밸런싱 및 정책 처리

## 🧩 6. 실제 사용 예시 (개념적)

- Gateway 생성
- HTTPRoute로 호스트/경로 기반 라우팅
- TLS 설정
- 백엔드 서비스로 트래픽 전달
- Cilium이 Envoy와 eBPF를 통해 고성능 처리

## 🧩 7. Chapter 7의 핵심 요약

- Ingress는 단순하지만 한계가 많음
- Gateway API는 이를 대체하는 더 강력하고 유연한 표준
- Cilium은 Gateway API를 완전 지원하며 고성능 네트워크 스택을 제공
- Gateway API는 쿠버네티스 네트워크의 미래 표준으로 자리 잡는 중

### 추가로 학습해야 할 내용

- Cilium Gateway 설정 실습
- Envoy가 Cilium에서 어떻게 동작하는지 실습
- Gateway API를 실제로 어떻게 배포하는지 실습