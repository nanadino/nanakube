
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

<br>

# 📌 프로젝트 개요



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

### 2️⃣ **myapp-nodeport.yaml**

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
        image: minjee9/javaapp:latest  # 변경된 이미지
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

### 3️⃣ **myapp-loadbalancer.yaml**

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
        image: minjee9/javaapp:latest  # 변경된 이미지
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


## 🚀 실행 방법

### 1️⃣ 이미지 생성

```
docker build -t ...
```


### 2️⃣ docker hub에 push

```
docker push ...
```

### 3. yaml파일 실행해 pods 생성

```
kubectl apply -f myapp-nodeport.yaml
kubectl apply -f myapp-loadbalancer.yaml
```

### 4. port fowarding


### 5. loadbalancer 확인



<br>

## 4️⃣ TroubleShooting

docker push fail
tag 문제
``
docker tag javaapp minjee9/javaapp:latest
``

## 4️⃣ Conclusion



<br>

<br>




<img src="https://capsule-render.vercel.app/api?type=waving&color=ADEDFF&height=150&section=footer" width="1000" />
