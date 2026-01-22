# Kubernetes - Deployment 다루기

## 다루는 주제
1. Deployment 생성
2. Deployment 스케일링
3. Deployment를 Service로 노출
4. Deployment 업데이트
5. Deployment 롤백
6. 롤링 재시작(Rolling Restart)
7. Deployment 일시정지/재개
8. 카나리 배포(Declarative 섹션에서 다룸)

## 추가 설명
- **Deployment란?**
  - ReplicaSet을 관리하는 상위 리소스입니다.
  - 선언형 업데이트(롤링 업데이트), 롤백, 스케일링을 지원합니다.
- **왜 Deployment를 쓰는가?**
  - ReplicaSet을 직접 관리하지 않아도 되어 운영이 단순해집니다.
  - 배포 실패 시 빠른 롤백이 가능해 안정성이 높습니다.
