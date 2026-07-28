# 부록 2 - CiliumNetworkPolicy(CNP) 예제

## 🔐 1) 기본 L3/L4 정책 — 특정 네임스페이스/라벨만 허용

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  endpointSelector:
    matchLabels:
      app: backend
  ingress:
    - fromEndpoints:
        - matchLabels:
            app: frontend
      toPorts:
        - ports:
            - port: "8080"
              protocol: TCP
```

**설명**

- `backend` Pod는 `frontend` Pod에서 오는 **TCP 8080** 트래픽만 허용
- 나머지 모든 트래픽은 기본적으로 차단(default deny)

## 🌐 2) L7 HTTP 정책 — HTTP method/path 기반 제어

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: http-route-policy
spec:
  endpointSelector:
    matchLabels:
      app: api-server
  ingress:
    - fromEndpoints:
        - matchLabels:
            app: web
      toPorts:
        - ports:
            - port: "80"
              protocol: TCP
          rules:
            http:
              - method: GET
                path: "/public"
              - method: POST
                path: "/login"

```

**설명**

- `web` Pod는 `api-server`의 `/public` GET, `/login` POST만 호출 가능
- 다른 HTTP 경로는 모두 차단
- L7 정책이기 때문에 Envoy 프록시가 자동으로 적용됨

## 🔒 3) 외부 접근 차단 + 내부 특정 서비스만 허용

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: deny-external-allow-internal
spec:
  endpointSelector:
    matchLabels:
      app: internal-service
  ingress:
    - fromEndpoints:
        - matchLabels:
            team: platform

```

**설명**

- `internal-service`는 **platform 팀 라벨을 가진 Pod만 접근 가능**
- 외부 LoadBalancer, NodePort, 다른 네임스페이스 모두 차단
- Zero Trust 기본 패턴

## 🔁 4) egress 정책 — 외부 API 호출을 특정 도메인으로 제한

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: restrict-external-api
spec:
  endpointSelector:
    matchLabels:
      app: data-processor
  egress:
    - toFQDNs:
        - matchName: "api.openai.com"
      toPorts:
        - ports:
            - port: "443"
              protocol: TCP

```

**설명**

- `data-processor` Pod는 **api.openai.com:443**만 호출 가능
- 다른 모든 외부 인터넷 접근 차단
- FQDN 기반 정책은 Cilium이 DNS 요청을 추적해서 자동 적용

## 🧩 5) Clusterwide 정책(CCNP) — 클러스터 전체에 적용

```yaml
apiVersion: cilium.io/v2
kind: CiliumClusterwideNetworkPolicy
metadata:
  name: cluster-default-deny
spec:
  endpointSelector: {}
  ingress:
    - {}
  egress:
    - {}

```

**설명**

- 클러스터 전체에 **기본 차단(default deny)** 적용
- 이후 필요한 서비스만 개별 CNP로 허용
- 대규모 조직에서 가장 많이 쓰는 Zero Trust 패턴

## 🔥 6) ML/AI 워크로드용 — GPU 노드로 들어오는 트래픽 제한

yaml

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: gpu-node-protection
spec:
  endpointSelector:
    matchLabels:
      node-type: gpu
  ingress:
    - fromEndpoints:
        - matchLabels:
            app: model-server

```

**설명**

- GPU 노드에 올라간 Pod는 **model-server**에서 오는 트래픽만 허용
- 데이터 파이프라인, 실험용 Pod 등이 GPU 노드를 실수로 때리는 것을 방지
- ML/AI 클러스터에서 매우 유용