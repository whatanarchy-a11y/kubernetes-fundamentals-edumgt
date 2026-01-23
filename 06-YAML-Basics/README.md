# YAML 기본 문법

## Step-01: 주석과 Key-Value
- 콜론 뒤에는 **공백**이 있어야 키/값이 구분됩니다.
```yml
# 단순 key-value 정의
name: kalyan
age: 23
city: Hyderabad
```

## Step-02: Dictionary / Map
- 하나의 키 아래 여러 속성을 묶어서 표현합니다.
- 동일한 깊이의 항목은 **같은 들여쓰기**가 필요합니다.
```yml
person:
  name: kalyan
  age: 23
  city: Hyderabad
```

## Step-03: Array / List
- `-` 기호가 배열 요소를 의미합니다.
```yml
person: # Dictionary
  name: kalyan
  age: 23
  city: Hyderabad
  hobbies: # List
    - cycling
    - cookines
  hobbies: [cycling, cooking]   # 다른 표기 방식의 List
```

## Step-04: 중첩 리스트
- 리스트 안에 딕셔너리를 넣어 구조화할 수 있습니다.
```yml
person: # Dictionary
  name: kalyan
  age: 23
  city: Hyderabad
  hobbies: # List
    - cycling
    - cooking
  hobbies: [cycling, cooking]   # 다른 표기 방식의 List
  friends:
    - name: friend1
      age: 22
    - name: friend2
      age: 25
```

## Step-05: Pod 템플릿 예시
- Kubernetes 매니페스트는 보통 `apiVersion`, `kind`, `metadata`, `spec`로 구성됩니다.
```yml
apiVersion: v1 # String
kind: Pod  # String
metadata: # Dictionary
  name: myapp-pod
  labels: # Dictionary
    app: myapp
spec:
  containers: # List
    - name: myapp
      image: stacksimplify/kubenginx:1.0.0
      ports:
        - containerPort: 80
          protocol: "TCP"
        - containerPort: 81
          protocol: "TCP"
```

## 추가 설명
- YAML은 들여쓰기에 민감하므로 탭 대신 **스페이스**를 사용합니다.
- Kubernetes 매니페스트에서는 라벨(labels)과 셀렉터(selectors)가 매우 중요합니다.
- 배열/딕셔너리 구조를 정확히 맞추면 `kubectl apply` 시 검증 오류를 줄일 수 있습니다.
