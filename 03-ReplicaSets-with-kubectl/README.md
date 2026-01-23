# Kubernetes - ReplicaSet 다루기

## Step-01: ReplicaSet 소개
- **ReplicaSet이란?**
  - 지정된 수의 Pod 복제본을 항상 유지하도록 보장하는 리소스입니다.
  - Pod가 삭제되거나 장애가 발생하면 자동으로 새 Pod를 생성합니다.
- **ReplicaSet 사용의 장점**
  - 고가용성(High Availability) 확보
  - 확장성(Scale-out) 자동화
  - 선언형으로 원하는 복제 수를 유지

## 추가 설명
- ReplicaSet은 Pod 수를 유지하는 데 집중하며, 배포 전략(롤링 업데이트/롤백)은 Deployment가 담당합니다.
- 운영 환경에서는 직접 ReplicaSet을 만들기보다 Deployment로 생성하는 패턴이 일반적입니다.

## Step-02: ReplicaSet 생성

### ReplicaSet 생성
- ReplicaSet 매니페스트를 적용합니다.
```
kubectl create -f replicaset-demo.yml
```
- **replicaset-demo.yml** 예시
```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-helloworld-rs
  labels:
    app: my-helloworld
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-helloworld
  template:
    metadata:
      labels:
        app: my-helloworld
    spec:
      containers:
      - name: my-helloworld-app
        image: stacksimplify/kube-helloworld:1.0.0
```

### ReplicaSet 목록 확인
```
kubectl get replicaset
kubectl get rs
```

### ReplicaSet 상세 확인
```
kubectl describe rs/<replicaset-name>

kubectl describe rs/my-helloworld-rs
[or]
kubectl describe rs my-helloworld-rs
```

### Pod 목록 확인
```
# Pod 목록
kubectl get pods
kubectl describe pod <pod-name>

# Pod IP 및 노드 정보 포함
kubectl get pods -o wide
```

### Pod의 소유자(Owner) 확인
- Pod YAML의 **ownerReferences.name**에서 소속 ReplicaSet을 확인합니다.
```
kubectl get pods <pod-name> -o yaml
kubectl get pods my-helloworld-rs-c8rrj -o yaml
```

## Step-03: ReplicaSet을 Service로 노출
- NodePort Service를 통해 외부에서 접근할 수 있도록 구성합니다.
```
# ReplicaSet을 Service로 노출
kubectl expose rs <ReplicaSet-Name> --type=NodePort --port=80 --target-port=8080 --name=<Service-Name-To-Be-Created>
kubectl expose rs my-helloworld-rs --type=NodePort --port=80 --target-port=8080 --name=my-helloworld-rs-service

# Service 정보 확인
kubectl get service
kubectl get svc

# 워커 노드 Public IP 확인
kubectl get nodes -o wide
```
- **접근 예시**
```
http://<node1-public-ip>:<Node-Port>/hello
```

## Step-04: ReplicaSet의 고가용성 테스트
- Pod를 강제로 삭제해도 ReplicaSet이 자동 복구하는지 확인합니다.
```
# Pod 목록 확인
kubectl get pods

# Pod 삭제
kubectl delete pod <Pod-Name>

# 자동 복구 확인 (새 Pod 이름/시간 확인)
kubectl get pods
```

## Step-05: ReplicaSet 스케일링 테스트
- `replicaset-demo.yml`의 **replicas** 값을 3에서 6으로 변경합니다.
```
# 변경 전
spec:
  replicas: 3

# 변경 후
spec:
  replicas: 6
```
- ReplicaSet 업데이트
```
# 변경사항 적용
kubectl replace -f replicaset-demo.yml

# 새 Pod 생성 확인
kubectl get pods -o wide
```

## Step-06: ReplicaSet 및 Service 삭제

### ReplicaSet 삭제
```
# ReplicaSet 삭제
kubectl delete rs <ReplicaSet-Name>

# 예시
kubectl delete rs/my-helloworld-rs
[or]
kubectl delete rs my-helloworld-rs

# 삭제 확인
kubectl get rs
```

### Service 삭제
```
# Service 삭제
kubectl delete svc <service-name>

# 예시
kubectl delete svc my-helloworld-rs-service
[or]
kubectl delete svc/my-helloworld-rs-service

# 삭제 확인
kubectl get svc
```

## 추가 학습 포인트
- **Labels & Selectors**
  - ReplicaSet이 어떤 Pod를 관리할지 결정하는 핵심 개념입니다.
  - YAML로 매니페스트를 작성하는 섹션에서 더 자세히 다룹니다.
