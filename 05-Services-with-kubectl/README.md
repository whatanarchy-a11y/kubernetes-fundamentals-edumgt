# Kubernetes - Service 다루기

## Step-01: Service 소개
- **Service 유형**
  1. ClusterIP
  2. NodePort
  3. LoadBalancer
  4. ExternalName
- 이 섹션에서는 ClusterIP와 NodePort를 자세히 다룹니다.
- LoadBalancer는 클라우드 제공자마다 동작이 달라, 해당 환경별 섹션에서 다룹니다.
- ExternalName은 명령형 생성이 없어 YAML로 정의해야 하며, 필요 시 추가 설명합니다.

## Step-02: ClusterIP Service - Backend 애플리케이션 구성
- Backend(Spring Boot REST) Deployment 생성
- Backend를 위한 ClusterIP Service 생성
```
# Backend REST 앱 Deployment 생성
kubectl create deployment my-backend-rest-app --image=stacksimplify/kube-helloworld:1.0.0
kubectl get deploy

# Backend REST 앱 ClusterIP Service 생성
kubectl expose deployment my-backend-rest-app --port=8080 --target-port=8080 --name=my-backend-service
kubectl get svc
Observation: 기본값이 ClusterIP이므로 --type=ClusterIp 옵션을 생략해도 됩니다.
```
- **중요:** 컨테이너 포트(8080)와 서비스 포트(8080)가 동일하면 `--target-port`는 생략 가능합니다.

- **Backend HelloWorld 소스** [kube-helloworld](../00-Docker-Images/02-kube-backend-helloworld-springboot/kube-helloworld)

## Step-03: NodePort Service - Frontend 애플리케이션 구성
- 기존에도 NodePort를 사용해봤지만, ClusterIP와 함께 전체 아키텍처 관점에서 다시 구성합니다.
- Frontend(Nginx Reverse Proxy) Deployment 생성
- Frontend를 위한 NodePort Service 생성
- **중요:** Nginx reverse proxy 설정에서 backend 서비스 이름 `my-backend-service`가 정확해야 합니다.

### Nginx 설정 예시
```conf
server {
    listen       80;
    server_name  localhost;
    location / {
    # Backend ClusterIP Service 이름과 포트 지정
    # proxy_pass http://<Backend-ClusterIp-Service-Name>:<Port>;
    proxy_pass http://my-backend-service:8080;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
- **Docker 이미지 위치:** https://hub.docker.com/repository/docker/stacksimplify/kube-frontend-nginx
- **Frontend 소스** [kube-frontend-nginx](../00-Docker-Images/03-kube-frontend-nginx)
```
# Frontend Nginx Proxy Deployment 생성
kubectl create deployment my-frontend-nginx-app --image=stacksimplify/kube-frontend-nginx:1.0.0
kubectl get deploy

# Frontend Nginx Proxy NodePort Service 생성
kubectl expose deployment my-frontend-nginx-app --type=NodePort --port=80 --target-port=80 --name=my-frontend-service
kubectl get svc

# 접근 정보 확인
kubectl get svc
kubectl get nodes -o wide
http://<node1-public-ip>:<Node-Port>/hello

# Backend를 10개로 스케일링
kubectl scale --replicas=10 deployment/my-backend-rest-app

# 다시 접근해 로드밸런싱 확인
http://<node1-public-ip>:<Node-Port>/hello
```

## 추가 설명
- ClusterIP는 내부 통신의 기본이며, 마이크로서비스 간 안정적인 서비스 디스커버리를 제공합니다.
- NodePort는 고정 포트를 열기 때문에 보안 그룹/방화벽 규칙도 함께 확인해야 합니다.

## 추가 학습 예정
- 클라우드별 기능 차이로 아래 항목은 추후 섹션에서 다룹니다.
  - LoadBalancer
  - ExternalName
