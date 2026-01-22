# Kubernetes 아키텍처

## Step-01: 왜 Kubernetes인가?
- **문제 배경**
  - 마이크로서비스가 늘어나면서 컨테이너 수가 많아지고, 수동 운영이 어려워집니다.
  - 장애 복구, 스케일링, 배포 롤백 같은 작업을 자동화할 필요가 있습니다.
- **Kubernetes의 역할**
  - 컨테이너 오케스트레이션: 컨테이너 스케줄링, 상태 관리, 자동 복구
  - 서비스 디스커버리/로드밸런싱: 안정적 서비스 노출
  - 선언형 운영: 원하는 상태를 정의하면 시스템이 맞춰줌

## Step-02: Kubernetes 아키텍처
- **Control Plane (관리 영역)**
  - API Server: 모든 요청의 진입점이며 인증/인가/어드미션을 처리합니다.
  - Scheduler: 어떤 노드에 Pod를 배치할지 결정합니다.
  - Controller Manager: ReplicaSet/Deployment 등 컨트롤러 루프를 실행합니다.
  - etcd: 클러스터의 모든 상태를 저장하는 분산 키-값 저장소입니다.
- **Node (작업 노드)**
  - kubelet: 노드에서 Pod 실행을 책임지는 에이전트입니다.
  - Container Runtime: 컨테이너 실행(예: containerd)
  - kube-proxy: Service 트래픽 라우팅/로드밸런싱을 담당합니다.

## 추가 설명
- **Kubernetes vs AWS EKS**
  - EKS는 Control Plane을 AWS에서 관리해주므로 운영 부담이 줄어듭니다.
  - 노드(EC2)는 사용자가 관리하거나, Managed Node Group으로 관리합니다.
- **핵심 개념 정리**
  - Pod: 컨테이너를 실행하는 최소 단위
  - ReplicaSet: Pod 복제/복구를 담당
  - Deployment: 롤링 업데이트/롤백을 지원하는 상위 리소스
  - Service: 네트워크 접근을 제공하는 추상화
