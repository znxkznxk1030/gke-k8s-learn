# Kubernetes

## 쿠버네티스와 구글 쿠버네티스 엔진 (GKE) 시작하기

### Step 1 - 도커, 쿠버네티스 그리고 구글 쿠버네티스 엔진 시작하기

#### 컨테이너 오케스트레이션

- 수천개의 마이크로서비스의 수천개의 인스턴스 관리하기

#### 특징

1. 오토스케일링
2. 서비스 디스커버리
3. 로드 밸런싱
4. 자기 회복
5. 제로 타임 배포

#### 클라우드 중립

- 어떤 인프라 (AWS, GCP, Azure 등) 이던 간에 표준화된 플랫폼 제공

### Step 2 - 구글 클라우드 계정 생성하기

> <https://cloud.google.com/>

### Step 3 - GKE 쿠버네티스 클러스터 생성하기

#### 클러스터

- 마스터 노드(들): 클러스터를 관리
- 워커 노드(들): 어플리케이션 실행

1. <https://console.cloud.google.com/> 접속
2. Kubernetes Engine 화면 들어가기
3. "생성" 버튼 클릭 ( 최소 생성시, 몇 분 소요 )
4. 클러스터 "만들기" 버튼 클릭 ( 표준 선택 )

![cluster](./day1/k8s-cluster.png)
![k8s cluster config](./day1/k8s-cluster-config.png)

#### 노드 풀 & 노드

![node pool](./day1/node-pool-setting.png)
![node](./day1/node-setting.png)

#### Result

![result](./day1/result.png)

### Step 4 - 쿠버네티스 클러스터 검토 및 쿠버네티스에 관한 흥미로운 사실들

- 발음 [KOO - BER - NET - EEZ]
- 로고 : Helmsman (조타수)
- 클라우드에서 K8S : AKS (Azure), Amazon EKS and GKE (Google)

### Step 5 - 첫 번째 스프링 부트 어플리케이션 쿠버네티스 클러스터에 배치하기

#### CLI 이용하기

<https://shell.cloud.google.com/?hl=ko&fromcloudshell=true&show=terminal>

#### 연결하기

##### 1. 클러스터 > "연결"

![k8s-cli-1](./day1/k8s-cli-1.png)

##### 2. 명령줄로 액세스하기

![k8s-cli-2](./day1/k8s-cli-2.png)

##### 3. "kubectel"을 이용해서 명령하기

![k8s-cli-3](./day1/k8s-cli-3.png)

#### 배포하기

##### 1. 도커 이미지로 인스턴스 생성하기

```shell
kubectl create deployment hello-world-rest-api --image=in28min/hello-world-rest-api:0.0.1.RELEASE
```

![docker hub](./day1/docker-hub.png)
![docker image](./day1/docker-image.png)

##### 2. 인스턴스 노출시키기

```shell
kubectl expose deployment hello-world-rest-api --type=LoadBalancer --port=8080
```

![kubectl create & expose](./day1/kuberctl-create-expose.png)

##### 3. 엔드포인트 확인하기

![endpoint](./day1/endpoint.png)

- <http://34.134.85.98:8080/hello-world>

![hello world](./day1/hello-world.png)

#### 크레딧 아끼기

하루 단위로 수업을 모두 들으신 후 클러스터 노드 사이즈를 0으로 감소시키시면 됩니다.

```shell
gcloud container clusters resize --zone <name_of_zone> <name_of_your_cluster> --num-nodes=0
```

다시 시작할 준비가 되셨다면 노드의 숫자를 증가시키세요:

```shell
gcloud container clusters resize --zone <name_of_zone> <name_of_your_cluster> --num-nodes=3
```
