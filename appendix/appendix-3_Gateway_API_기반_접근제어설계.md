# 부록 3 - Gateway API 기반 접근제어 설계

**Gateway API 기반 접근 제어 설계**는 Cilium을 쓸 때 정말 강력해짐
Ingress보다 훨씬 정교하고, L4/L7 정책을 네이티브하게 결합할 수 있어서 **Zero Trust 네트워크 아키텍처**를 구현할 때 최적의 방식임

**핵심 개념 → 설계 원칙 → 실제 구조 → 예시 → 운영 전략** 순서로 정리

## 🧩 Gateway API 기반 접근 제어가 왜 중요한가

Gateway API가 기존 Ingress보다 훨씬 강력한 이유

- **역할 기반 모델(Role-oriented)**
인프라팀은 Gateway를 만들고, 애플리케이션팀은 Route만 정의
- **L7 제어가 세밀함**
HTTP method, header, path, host 기반 제어
- **멀티클러스터·멀티테넌시에 강함**
- **Cilium의 eBPF + Envoy 조합으로 고성능**
- **네트워크 정책(CiliumNetworkPolicy)와 자연스럽게 결합됨**

즉,

> Gateway API는 “클러스터 외부 → 내부 서비스”로 들어오는 트래픽을 L7에서 제어하고,
CiliumNetworkPolicy는 “클러스터 내부” 트래픽을 L3/L4/L7에서 제어함.
> 

둘을 합치면 완전한 Zero Trust 모델이 됨

## 🧭 Gateway API 기반 접근 제어 설계 원칙

### 1) **경계(Edge)에서 L7 제어**

- 외부 트래픽은 반드시 Gateway를 통과
- Gateway에서 인증, TLS, 라우팅, 필터링 수행
- 불필요한 트래픽은 클러스터 내부로 들어오지 못하게 차단

### 2) **내부에서는 CiliumNetworkPolicy로 L3/L4/L7 제어**

- Gateway → Backend 서비스로 가는 트래픽도 정책으로 제한
- Pod 간 lateral movement 차단
- 서비스 간 통신은 라벨 기반으로 허용

### 3) **정책을 계층화**

- L7: Gateway API
- L4/L3: CiliumNetworkPolicy
- L7 내부 제어: Cilium L7 정책(Envoy)

### 4) **기본 차단(Default deny) + 명시적 허용**

Zero Trust의 핵심.

## 🏗️ 전체 아키텍처 구조 (개념도)

코드

```
[External Client]
      |
      v
+---------------------+
|   Gateway (Envoy)   |  ← L7 인증, TLS, 라우팅, 필터링
+---------------------+
      |
      v
+---------------------+
|  CiliumNetworkPolicy| ← L3/L4/L7 내부 접근 제어
+---------------------+
      |
      v
[Backend Service Pods]
```

## 📘 실제 구성 예시

### 1) Gateway 정의 (인프라팀)

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: main-gateway
spec:
  gatewayClassName: cilium
  listeners:
    - name: https
      protocol: HTTPS
      port: 443
      tls:
        mode: Terminate

```

### 2) HTTPRoute 정의 (애플리케이션팀)

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: api-route
spec:
  parentRefs:
    - name: main-gateway
  hostnames:
    - "api.example.com"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: "/v1"
      backendRefs:
        - name: api-service
          port: 8080

```

### 3) Gateway → Backend 접근을 CiliumNetworkPolicy로 제한

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: allow-gateway-to-api
spec:
  endpointSelector:
    matchLabels:
      app: api-service
  ingress:
    - fromEndpoints:
        - matchLabels:
            io.cilium.gateway: main-gateway
      toPorts:
        - ports:
            - port: "8080"
              protocol: TCP

```

**핵심 포인트**

- Gateway에서 오는 트래픽만 backend에 접근 가능
- 다른 Pod는 절대 접근 불가
- 완전한 Zero Trust

## 🔐 고급 접근 제어 기능

### 1) L7 필터링 (HTTP header, method, query)

Gateway API는 다음을 제어할 수 있어:

- HTTP method 제한
- 특정 header가 있을 때만 허용
- JWT 인증 필터
- Rate limiting
- Traffic mirroring
- mTLS between Gateway ↔ Backend

### 2) 멀티클러스터 접근 제어

- 클러스터 A의 Gateway → 클러스터 B의 서비스
- Cilium Cluster Mesh + Gateway API 조합
- 지역별 접근 제어 가능

### 3) 팀/테넌트별 Gateway 분리

- 팀별 Gateway
- 네임스페이스 단위 Route
- Cilium 정책으로 내부 접근 분리

## 🧠 운영 전략

### 1) Gateway는 “외부 경계”

- 인증, TLS, L7 필터링
- Rate limiting
- Bot detection
- WAF 기능(Envoy 필터)

### 2) CiliumNetworkPolicy는 “내부 경계”

- Pod 간 lateral movement 차단
- 서비스 간 통신 제한
- 기본 차단(default deny)

### 3) 관측성

- Gateway → Envoy metrics
- 내부 → Hubble
- 두 관측 시스템을 결합하면 전체 트래픽 흐름이 보임

## 📝 요약

- Gateway API는 외부 트래픽을 L7에서 제어하는 핵심 경계
- CiliumNetworkPolicy는 내부 트래픽을 L3/L4/L7에서 제어
- 둘을 결합하면 완전한 Zero Trust 네트워크 모델 완성
- 멀티클러스터, 멀티테넌시, ML/AI 워크로드에서도 강력한 구조