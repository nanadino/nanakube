
<img src="https://capsule-render.vercel.app/api?type=waving&color=ADEDFF&height=300&section=header&text=🐳Docker-Kubernetes🐳&fontSize=55&fontColor=FFFFFF&animation=fadeIn&width=1200" width="1200" />


<br>

## 📍 Outline
- [1️⃣ Contributors](#1%EF%B8%8F⃣-contributors)
- [2️⃣ Purpose](#2%EF%B8%8F⃣-purpose)
- [3️⃣ Contents](#3%EF%B8%8F⃣-contents)
- [4️⃣ Conclusion](#4%EF%B8%8F⃣-Conclusion)
  
<br>
<br>

## 1️⃣ Contributors
<br>

| <img src="https://avatars.githubusercontent.com/u/87555330?v=4" width="200px"> | <img src="https://avatars.githubusercontent.com/u/82265395?v=4" width="200px"> | <img src="https://github.com/JaeHee-devSpace.png" width="200px"> | <img src="https://avatars.githubusercontent.com/u/115103394?v=4" width="200px"> |
| :---: | :---: | :---: | :---: |
| [김민성](https://github.com/minsung159357) | [구민지](https://github.com/minjee83) | [박재희](https://github.com/JaeHee-devSpace) | [이현정](https://github.com/nanahj) |


<br>

## 2️⃣ Purpose

이 프로젝트는 **Spring Boot 애플리케이션**을 **Docker 컨테이너화**하고, 이를 **Kubernetes 클러스터에서 NodePort 및 LoadBalancer를 이용해 배포**하는 것을 목표로 합니다.

**주요 수행 작업:**
1. Spring Boot 애플리케이션을 Docker 이미지로 생성
2. Docker Hub에 이미지 푸시
3. Kubernetes에서 **NodePort 및 LoadBalancer 방식으로 Pod 2개 실행**
   
<br>

# 📌 프로젝트 개요

- **기술 스택:** Spring Boot, Docker, Kubernetes
- **배포 방식:** NodePort & LoadBalancer
- **Docker Hub 저장소:** `<dockerhub id>/javaapp`

<br>


## 3️⃣ Contents

<br>

## 📂 폴더 구조

```
Project
├── Dockerfile
├── myapp.jar
├── myapp-loadbalancer.yaml
└── myapp-nodeport.yaml
```

<br>

## ⚙️ 구성 요소

### 1️⃣ **Dockerfile** (Spring Boot 애플리케이션 컨테이너)

```dockerfile
# 1. 공식 OpenJDK 기반 이미지 사용
FROM openjdk:17

# 2. 작업 디렉토리 설정
WORKDIR /app

# 3. 빌드된 JAR 파일을 컨테이너로 복사
COPY ./myapp.jar myapp.jar

# 4. 실행 명령 설정 (JAR 파일 실행)
CMD ["java", "-jar", "myapp.jar"]
```

<br>

### 2️⃣ **myapp-nodeport.yaml** (NodePort 배포)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: <dockerhub id>/javaapp:latest  # 변경된 이미지
        ports:
        - containerPort: 8082 # 외부컨테이너 포트

---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 8087
      targetPort: 8082
      nodePort: 30081  # 30000~32767 사이의 값 선택 가능
  type: NodePort


```

<br>

### 3️⃣ **myapp-loadbalancer.yaml** (LoadBalancer 배포)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: <dockerhub id>/javaapp:latest  # 변경된 이미지
        ports:
        - containerPort: 8082

---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80       # External-ip 사용하는 서비스 포트
      targetPort: 8082 # 컨테이너 포트

```

<br>


## 🚀 Setup & Execution

### 1️⃣ Docker 이미지 빌드

```
docker build -t <dockerhub id>/javaapp:latest .
```


### 2️⃣ Docker Hub에 푸시

```
docker push <dockerhub id>/javaapp:latest
```

### 3️⃣ Kubernetes에서 배포

```
kubectl apply -f myapp-nodeport.yaml
kubectl apply -f myapp-loadbalancer.yaml
```

### 4️⃣ 포트 포워딩 설정

```sh
kubectl port-forward service/myapp-service 8087:8082
```

### 5️⃣ LoadBalancer 확인

```sh
kubectl get services
```
> EXTERNAL-IP가 할당되었는지 확인

```
ubuntu@miniserver:~/00.mission$ kubectl get svc
NAME            TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)        AGE
kubernetes      ClusterIP      10.96.0.1        <none>           443/TCP        29h
myapp-service   LoadBalancer   10.104.151.173   10.104.151.173   80:30081/TCP   15m
```


![image](https://github.com/user-attachments/assets/564d46f9-d8bd-4b05-a14a-906c6eeb24c7)


<br>

## 6️⃣ Troubleshooting

### ❌ Docker 이미지 Push 실패
#### 원인
- `tag` 미설정 또는 잘못된 태그 지정
#### 해결 방법
```sh
docker tag javaapp <dockerhub id>/javaapp:latest
docker push <dockerhub id>/javaapp:latest
```

---

## 7️⃣ Conclusion

본 프로젝트를 통해 Docker를 이용한 애플리케이션 컨테이너화 및 Kubernetes 배포를 실습했습니다. NodePort와 LoadBalancer 방식을 비교하며 다양한 배포 방식에 대한 이해를 넓힐 수 있었습니다.

<br>

<br>




<img src="https://capsule-render.vercel.app/api?type=waving&color=ADEDFF&height=150&section=footer" width="1000" />
