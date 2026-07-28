# 부록 4 - Egress Gateway 예시

### 1) 기본적인 Egress Gateway 구성 예시

```yaml
apiVersion: cilium.io/v2
kind: CiliumEgressGatewayPolicy
metadata:
  name: team-a-egress-gateway
spec:
  endpointSelector:
    matchLabels:
      team: a
  destinationCIDRs:
    - "0.0.0.0/0"
  egressGateway:
    nodeSelector:
      matchLabels:
        role: egress-gateway
    fallbackIP: "203.0.113.10"

```

**의미**

- **endpointSelector**: `team=a` 라벨을 가진 Pod들이 대상
- **destinationCIDRs**: 외부로 나가는 모든 트래픽(`0.0.0.0/0`)을 이 정책에 포함
- **egressGateway.nodeSelector**: `role=egress-gateway` 라벨을 가진 노드를 Egress Gateway로 사용
- **fallbackIP**: 이 노드에서 외부로 나갈 때 사용할 SNAT IP(고정 출발지 IP)

### 2) 특정 외부 네트워크만 Gateway로 우회시키는 예시

```yaml
apiVersion: cilium.io/v2
kind: CiliumEgressGatewayPolicy
metadata:
  name: s3-egress-gateway
spec:
  endpointSelector:
    matchLabels:
      app: data-pipeline
  destinationCIDRs:
    - "52.216.0.0/15"   # 예: S3 CIDR 대역
  egressGateway:
    nodeSelector:
      matchLabels:
        role: egress-gateway
    fallbackIP: "198.51.100.20"

```

**의미**

- `data-pipeline` 앱에서 S3 대역으로 나가는 트래픽만 Egress Gateway를 거치게 함
- S3로 나가는 트래픽은 항상 `198.51.100.20` IP로 보이도록 SNAT
- 방화벽·감사·규제 대응에 유리

### 3) 팀별로 다른 Gateway 노드를 쓰는 패턴

```yaml
apiVersion: cilium.io/v2
kind: CiliumEgressGatewayPolicy
metadata:
  name: team-b-egress
spec:
  endpointSelector:
    matchLabels:
      team: b
  destinationCIDRs:
    - "0.0.0.0/0"
  egressGateway:
    nodeSelector:
      matchLabels:
        role: egress-gateway-team-b
    fallbackIP: "203.0.113.30"

```

**의미**

- `team=b` 트래픽은 `role=egress-gateway-team-b` 노드를 통해서만 외부로 나감
- 팀별로 서로 다른 NAT IP를 사용해서, “어떤 팀이 어떤 외부 서비스에 접근했는지”를 명확히 추적 가능