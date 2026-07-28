# 부록 9 - Cilium 운영 자동화 템플릿(GitOps 기반)

Argo CD / Flux 둘 다 적용 가능한 **GitOps 표준 구조**.

# 🧱 Cilium 운영 자동화 템플릿 (GitOps 기반 완성형)

GitOps 기반 운영 자동화는 크게 6개 계층으로 구성

1. **Cilium 설치/업그레이드 자동화**
2. **Cilium 구성(Helm values) 자동화**
3. **네트워크 정책(CNP/CCNP) 자동화**
4. **Hubble 구성 및 관측 자동화**
5. **Egress Gateway / FQDN 정책 자동화**
6. **운영·보안·장애 대응 자동화(Alerts + Sysdump)**

# 1) Cilium 설치/업그레이드 GitOps 템플릿

### 📁 Git 구조 예시

코드

```yaml
gitops/
  cilium/
    base/
      helm-values.yaml
      kustomization.yaml
    overlays/
      prod/
        helm-values.yaml
        kustomization.yaml
      staging/
        helm-values.yaml
        kustomization.yaml
```

### 🔹 base/helm-values.yaml

운영팀이 공통적으로 유지해야 하는 설정:

```yaml
kubeProxyReplacement: strict
hubble:
  enabled: true
  relay:
    enabled: true
  ui:
    enabled: true
ipam:
  mode: cluster-pool
encryption:
  enabled: true
  type: wireguard

```

### 🔹 overlays/prod/helm-values.yaml

프로덕션 특화 설정

```yaml
bandwidthManager:
  enabled: true
bpf:
  masquerade: true
  tproxy: true
egressGateway:
  enabled: true

```

### 🔹 GitOps 자동화 효과

- Cilium 버전 업그레이드 → Git에서 Helm chart 버전만 변경
- 모든 클러스터에 자동 롤링 업데이트
- 구성 drift 방지
- 운영자가 직접 kubectl로 설치할 필요 없음

# 2) Cilium 구성 자동화 (Helm values GitOps)

운영팀이 수동으로 조정하던 설정을 GitOps로 자동화:

### 자동화 대상

- kube-proxy replacement 모드
- BIG TCP / BBR 활성화
- WireGuard 암호화
- Envoy L7 프록시 리소스
- eBPF map 크기 조정
- NodeSelector 기반 구성

### 예시: ML/AI 클러스터용 WireGuard + BIG TCP 자동화

```yaml
encryption:
  enabled: true
  type: wireguard

bpf:
  tproxy: true
  preallocateMaps: true

bandwidthManager:
  enabled: true
  bbr: true

bigTCP:
  enabled: true

```

GitOps로 관리하면 **클러스터 전체에 일관된 성능·보안 설정**을 유지할 수 있어.

# 3) 네트워크 정책(CNP/CCNP) GitOps 자동화

### 📁 Git 구조

```yaml
gitops/
  network-policies/
    base/
      default-deny.yaml
    teams/
      ml/
        allow-ml-to-feature-store.yaml
      platform/
        allow-platform-to-api.yaml
    egress/
      ml/
        allow-huggingface.yaml

```

### 🔹 Clusterwide Default Deny 자동화

```yaml
apiVersion: cilium.io/v2
kind: CiliumClusterwideNetworkPolicy
metadata:
  name: default-deny
spec:
  endpointSelector: {}
  ingress: [{}]
  egress: [{}]

```

### 🔹 팀별 정책 자동화

ML팀 예시:

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: allow-ml-to-feature-store
spec:
  endpointSelector:
    matchLabels:
      team: ml
  ingress:
    - fromEndpoints:
        - matchLabels:
            app: feature-store

```

GitOps로 관리하면:

- 정책 변경 이력 추적
- 실수로 정책 삭제되는 사고 방지
- 멀티클러스터 정책 일관성 유지

# 4) Hubble 구성 및 관측 자동화

### 📁 Git 구조

```yaml
gitops/
  hubble/
    relay/
      values.yaml
    ui/
      values.yaml
    alerts/
      hubble-alerts.yaml
```

### 🔹 Hubble Relay 자동화

```
hubble:relay:enabled:trueresources:limits:cpu:"1"memory:"1Gi"
```

### 🔹 Hubble Alerts 자동화

예: DROP 증가 알림

yaml

```yaml
hubble:
  relay:
    enabled: true
    resources:
      limits:
        cpu: "1"
        memory: "1Gi"

```

GitOps로 관리하면:

- 관측성 구성 drift 방지
- 장애 알림 자동화
- 운영팀이 수동으로 설정할 필요 없음

# 5) Egress Gateway / FQDN 정책 자동화

### 📁 Git 구조

```yaml
gitops/
  egress/
    gateway/
      ml-egress-gateway.yaml
    fqdn/
      allow-openai.yaml
      allow-huggingface.yaml
```

### 🔹 Egress Gateway 자동화

```yaml
apiVersion: cilium.io/v2
kind: CiliumEgressGatewayPolicy
metadata:
  name: ml-egress-gateway
spec:
  endpointSelector:
    matchLabels:
      team: ml
  destinationCIDRs:
    - "0.0.0.0/0"
  egressGateway:
    nodeSelector:
      matchLabels:
        role: egress-gateway
    fallbackIP: "203.0.113.10"

```

### 🔹 FQDN 정책 자동화

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: allow-openai
spec:
  endpointSelector:
    matchLabels:
      team: ml
  egress:
    - toFQDNs:
        - matchName: "api.openai.com"

```

GitOps로 관리하면:

- 외부 API 접근 제어가 완전 자동화
- 규제 준수(금융/공공) 환경에서 필수
- 팀별 Egress 정책 일관성 유지

# 6) 운영·보안·장애 대응 자동화

### 🔹 Sysdump 자동 수집 템플릿

운영팀이 장애 발생 시 자동으로 sysdump를 수집하도록 구성:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cilium-sysdump-daily
spec:
  schedule: "0 3 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: sysdump
              image: cilium/cilium-sysdump:latest
              args: ["--output=/sysdump"]
          restartPolicy: OnFailure

```

### 🔹 보안 정책 자동 검증

GitOps + Hubble 연동으로 정책 변경 시 자동 검증:

- 정책 변경 → GitOps 배포
- Hubble에서 DROP 증가 감지 → Alert
- 운영팀이 즉시 대응

# 📝 최종 요약

GitOps 기반 Cilium 운영 자동화 템플릿은 다음 6개 계층으로 구성

1. **Cilium 설치/업그레이드 자동화**
2. **Helm values 기반 구성 자동화**
3. **네트워크 정책(CNP/CCNP) 자동화**
4. **Hubble 구성 및 관측 자동화**
5. **Egress Gateway / FQDN 정책 자동화**
6. **운영·보안·장애 대응 자동화**

이 템플릿을 적용하면:

- 운영자가 직접 kubectl을 거의 쓰지 않아도 되고
- 정책·구성·보안·관측이 모두 Git 기반으로 일관되게 유지되며
- 멀티클러스터·ML/AI 환경에서도 안정적인 운영이 가능