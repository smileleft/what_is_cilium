# 부록 8 - Cilium 운영 체크리스트

# 📘 Cilium 운영 체크리스트 (Production-Grade Template)

## 🟦 1. **일일(Daily) 점검 체크리스트**

운영자가 매일 확인해야 하는 기본 상태 점검 항목.

### 🔹 Cilium Agent 상태

- `cilium status`에서 모든 노드가 **Ready**인지
- 데이터패스(eBPF) 로드 실패 여부
- Envoy(L7 proxy) 상태 정상 여부
- IPAM 할당 상태 정상 여부

### 🔹 Hubble 상태

- Hubble Relay가 정상 동작하는지
- Hubble UI에서 흐름(flow)이 정상적으로 수집되는지
- `hubble observe`에서 DROP 이벤트가 비정상적으로 증가하지 않았는지

### 🔹 정책 적용 상태

- 최근 변경된 CiliumNetworkPolicy가 의도대로 적용됐는지
- L7 정책이 Envoy에서 정상 처리되는지
- Clusterwide 정책(CCNP)이 충돌 없이 적용되는지

### 🔹 노드 리소스

- Cilium Agent CPU 사용량
- Envoy CPU/메모리 사용량
- eBPF map 사용량(특히 대규모 클러스터)

## 🟩 2. **주간(Weekly) 점검 체크리스트**

주간 점검은 성능·보안·관측성 중심.

### 🔹 네트워크 성능

- 패킷 드랍률 증가 여부
- 노드 간 지연(latency) 변화
- L7 프록시(Envoy) 지연 증가 여부
- BIG TCP/BBR 활성화 여부 확인

### 🔹 보안 정책 검증

- Zero Trust 정책이 의도대로 동작하는지
- Egress 정책(FQDN)이 외부 접근을 제대로 제한하는지
- 암호화(IPsec/WireGuard) 상태 정상 여부
- 정책 충돌 여부(Hubble에서 확인)

### 🔹 운영 로그 점검

- Cilium Agent 로그에서 경고/오류 증가 여부
- Operator 로그에서 IPAM/ENI 문제 여부
- Envoy 로그에서 L7 필터 오류 여부

## 🟥 3. **변경(Change) 시점 체크리스트**

정책 변경, 업그레이드, 구성 변경 시 반드시 확인해야 하는 항목.

### 🔹 변경 전

- 변경 영향 범위 분석
- 정책 충돌 가능성 확인
- Hubble로 기존 흐름 baseline 확보
- 커널 버전·Cilium 버전 호환성 확인
- kube-proxy replacement 모드 영향 확인

### 🔹 변경 중

- 롤링 업데이트가 정상적으로 진행되는지
- 노드별 Cilium Agent 재시작 시 네트워크 중단 없는지
- L7 프록시 재시작 시 API 지연 없는지

### 🔹 변경 후

- `cilium status` 전체 노드 정상 여부
- 정책이 의도대로 적용됐는지
- Hubble에서 DROP 증가 여부
- 외부 API 호출(Egress) 정상 여부
- 암호화(IPsec/WireGuard) 정상 동작 여부

## 🟧 4. **장애(Troubleshooting) 시점 체크리스트**

네트워크 장애 발생 시 가장 먼저 확인해야 하는 항목.

### 🔹 1) 트래픽 DROP 여부

- `hubble observe --verdict DROPPED`
- 어떤 정책이 DROP을 발생시키는지
- L7 프록시에서 차단된 것인지 확인

### 🔹 2) 데이터패스(eBPF) 문제

- eBPF map 부족 여부
- 커널 기능 미지원 여부
- XDP 충돌 여부
- `cilium bpf map list`로 상태 확인

### 🔹 3) Envoy(L7) 문제

- Envoy CPU spike 여부
- L7 필터 오류
- HTTP/gRPC 요청이 Envoy에서 실패하는지

### 🔹 4) Egress 문제

- FQDN 정책이 DNS를 제대로 매핑했는지
- Egress Gateway가 정상 동작하는지
- 외부 API 호출 지연 증가 여부

### 🔹 5) 노드 문제

- NIC 드라이버 오류
- 커널 업데이트 후 eBPF 호환성 문제
- 노드 리소스 부족(CPU/메모리)

### 🔹 6) sysdump 수집

- `cilium sysdump`로 전체 상태 수집
- 장애 분석 및 외부 지원 요청 시 필수

## 🟨 5. **보안(Security) 체크리스트**

Zero Trust 운영을 위한 필수 항목.

### 🔹 정책

- Clusterwide default deny 유지
- 서비스 간 최소 허용 정책 적용
- L7 정책으로 API 보호
- Egress 정책으로 외부 접근 제한

### 🔹 암호화

- WireGuard/IPsec 정상 동작
- 키 회전 자동화 확인
- 암호화된 흐름이 정상인지 Hubble로 확인

### 🔹 멀티클러스터

- Cluster Mesh 연결 상태
- 클러스터 간 정책 일관성
- cross-cluster 암호화 상태

## 🟫 6. **성능(Performance) 체크리스트**

대규모 트래픽·ML/AI 워크로드에서 필수.

### 🔹 eBPF 튜닝

- map 크기 조정
- CT map 최적화
- XDP 사용 여부

### 🔹 L7 프록시 튜닝

- Envoy CPU/메모리 조정
- L7 필터 최적화
- 고부하 API 경로 분리

### 🔹 네트워크 최적화

- BIG TCP 활성화
- BBR 혼잡 제어
- Bandwidth Manager 활성화
- kube-proxy replacement 모드 최적화

## 🟪 7. **운영 자동화 체크리스트**

GitOps 기반 운영을 위한 항목.

### 🔹 GitOps

- Cilium 설치/업그레이드 GitOps 관리
- 정책 변경 GitOps 적용
- 멀티클러스터 정책 자동 배포

### 🔹 Alerting

- DROP 증가 알림
- Envoy 지연 증가 알림
- 암호화 실패 알림
- Egress 실패 알림

### 🔹 Observability

- Hubble Relay 고가용성
- Hubble UI 접근 제어
- 흐름 저장 기간 관리

# 📝 최종 요약

이 운영 체크리스트는 다음 7개 영역으로 구성된다:

1. **일일 점검** – 상태·정책·리소스
2. **주간 점검** – 성능·보안·로그
3. **변경 시점 점검** – 업그레이드·정책 변경
4. **장애 대응** – DROP·eBPF·Envoy·Egress
5. **보안 운영** – Zero Trust·암호화
6. **성능 운영** – eBPF·Envoy·BIG TCP·BBR
7. **운영 자동화** – GitOps·Alerting·Hubble

이 템플릿은 **실제 프로덕션 운영팀이 그대로 가져다 쓸 수 있는 수준**으로 구성되어 있어.