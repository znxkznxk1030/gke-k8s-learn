# Kubernetes

## Section 2. 쿠버네티스와 구글 쿠버네티스 엔진 (GKE) 시작하기

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
gcloud container clusters resize --zone us-central1-a in28minutes-cluster --num-nodes=0
```

다시 시작할 준비가 되셨다면 노드의 숫자를 증가시키세요:

```shell
gcloud container clusters resize --zone us-central1-a in28minutes-cluster --num-nodes=3
```

### Step 06 - 쿠버네티스 개념 간략한 소개 - 파드, 레플리카 세트, 배치

### 모든 파드, 레플리카 세트, 배치 가져오기

```shell
kubectl get events
kubectl get pods
kubectl get replicaset
kubectl get deployment
kubectl get service
```

### 강의에서 쓰이는 명령어들

<https://github.com/in28minutes/kubernetes-crash-course#commands-executed-during-the-course>

### Step 07 - 쿠버네티스의 파드 이해하기

- 쿠버네티스의 가장 작은 단위는 컨테이너가 아닌 파드이다.

```shell
kubectl get pods -o wide
kubectl explain pods # 문서
kubectl describe pod hello-world-rest-api-687d9c7bc7-4nbhf # 더 디테일하게
```

- 포드는 각자의 IP주소를 가진다.
- "1/1" 은 몇개의 컨테이너가 사용가능한지 보여준다.

#### QA와 개발 리소스 구분하기

- QA와 개발 리소스에 각자 다른 네임스페이스를 만들어 각자 특정한 네임스페이스와 반응하도록 한다.

### Step 08 - 쿠버네티스의 레플리카 세트 이해하기

```shell
kubectl get rs # <=> kubectl get replicaset
```

- 포드를 삭제해보기

```shell
kubectl get pods -o wide # get <pod-id>
kubectl delete pods <pod-id> # 
```

- 삭제후, 다시 포드를 확인해보면 새로운 파드가 시작됨을 알 수 있다.
- 즉, 포드를 죽이더라고 수초내에 다시 포드가 생성됨을 알 수 있다.

#### 레플리카셋에 더 많은 포드를 유지하도록 하기

![deployment structure](./day2/deployment.png)

```shell
kubectl scale deployment hello-world-rest-api --replicas=3

# 확인하기
kubectl get pods
kubectl get rs
kubectl get events --sort-by=.metadata.creationTimestamp # 시간대별로 이벤트 정렬하기
```

- pods 가 3개로 늘어남을 알 수 있다.

![replicaset](./day2/rs-pods-3.png)

- ScalingReplicaSet 이벤트를 통해 replica set이 확장됨을 알수 있다.

![rs evnet](./day2/events.png)

### Step 09 - 쿠버네티스의 배치 이해하기

#### 새로운 이미지로 배포하기 with 제로 다운 타임

```shell
kubectl set image deployment hello-world-rest-api hello-world-rest-api=DUMMY_IMAGE:TEST # 잘못된 이미지를 적용
```

- 이미지에 오류가 생겼지만, 앱이 작동함을 알수있음 ( 이전버전이 동작하고 있음 )
- 2개의 레플리카셋이 있음. 하지만 새이미지꺼는 오류가 있기 때문에 READY 된 파드가 0이고 이전 레플리카세트가 동작하고 있음.

![error image](./day2/rs-image-error.png)

#### 제대로된 이미지로 배포

```shell
kubectl set image deployment hello-world-rest-api hello-world-rest-api=in28min/hello-world-rest-api:0.0.2.RELEASE
```

### Step 11 - 쿠버네티스의 서비스 이해하기

- 서비스는 deployment를 expose시킬때 생성
- 파드가 삭제되고 생성될 때에도 같은 ip를 사용할 수 있도록 한다.
- 서비스는 Load Balancing 으로 구현된다.
- 쿠버네티스의 서비스는 각 클라우드 사 (AWS, Azure, GCP)의 로드밸런서와 완벽하게 동작한다.

![kubectl services](./day2/services.png)

- Cluster IP는 클러스내 내부에서만 사용하는 클러스터 서비스 (외부접근 불가능)

### Step 13 - 쿠버네티스 아키텍쳐 이해하기 - 마스터 노드와 일반 노드

![master node](./day2/kubenetes-architecture.png)

#### 마스터 노드

- API Server ( kube-apiserver ) : 구글 클라우드 콘솔과 kubectl이 통신하도록 함
- Distribute Database ( etcd ) : 원하는 상태 (Desired State)가 etcd에 저장 (3-5개 정도 구성하는 걸 추천)
- Scheduler ( kube-scheduler ) : 노드에 포드를 스케쥴링 하는 역할
- Controller Manager ( kube-controller-manager ) : 클러스터의 전반적인 상태를 관리, 쿠버네티스의 실제상태를 원하는 상태와 일치시키는 역할을 함.

#### 워커 노드

- Node Agent ( kubelet ) : 노드 안의 상황을 지켜보고 마스터 노드에 보고하는 일
- Networking Compoent ( kube-proxy ) : 노드 주변의 서비스와 포드를 노출시킴
- Container Runtime ( CRI - docker, rkt, ..etc) : 도커가 유명하지만 다른 oci로도 구성가능
- PODS ( Multiple pods running containers )

#### 마스터 노드 상태 보기

```shell
kubectl get componentstatuses
```

![component statuses](./day2/component-status.png)

### Step 14 - 구글 클라우드 지역과 지역대 이해하기

#### 여러 리전을 가지는 이유는 무엇일까?

1. 네트워크 지연( latency )을 줄이기 위해서
2. 가용성을 위해서 ( 특정 지역에 재난이 난다면, 다른 리전을 바로 이용할 수 있도록 하기 위해서 )
3. 몇몇 국가들은 국외에 국민들의 정보를 저장하는 것을 원치 않기 때문에

- Zone이란 각 리전에 존재하는 물리적 데이터 센터

## Section 3. GKE - 스프링 부트 Hello World REST API로 쿠버네티스와 도커 사용하기

### Step 1 - 첫 3개 스프링 부트 프로젝트 Eclipse로 불러오기

<https://github.com/in28minutes/kubernetes-crash-course>

#### Import / Existing Maven Projects

![eclipse - 1](./day3/eclipse-1.png)

#### 폴더 선택

![eclipse - 2](./day3/eclipse-2.png)

#### 01,02,03 project 선택

![eclipse - 3](./day3/eclipse-3.png)

#### 디펜던시가 다 다운로드 될 때까지 기다리기

![eclipse - 4](./day3/eclipse-4.png)

### Step 02 - 로컬 환경에 스프링 부트 Hello World 기반 REST API 01 설치하기
