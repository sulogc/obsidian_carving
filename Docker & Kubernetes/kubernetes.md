
![[kube.png]]
공식 Docs에 따르면, K8s는 컨테이너화 된 애플리케이션을 자동으로 배포, 스케일링, 등 관리하기 위한 오픈소스 시스템이다. 

## 배경

### 수동 배포의 문제

![[kube1.png]]

1. 컨테이너가 충돌되서 내려가거나 재배포 하는 문제 -> 수동으로 관리해야함.
2. 트래픽 증가 시 컨테이너 인스턴스가 더 필요할 수 있다. 
3. 동일한 컨테이너에 대해 트래픽을 동등하게 분산시켜야한다.

### 왜 쿠버네티스인가

사실 AWS에서 제공하는 ECS로 세 가지 모두 커버가 가능하다.

하지만 특정 프로바이더의 서비스의 의존성이 매우 커진다는 것.
AWS에서 정의한 서비스, 옵션을 사용해야만 하는 특징이 있다.

쉽게 말하면, 프로바이더를 바꿀 경우 AWS ECS를 위하 작성한 config가 무용지물이 되는 것.
그리고 ECS에 대해서도 공부해야한다. 이 역시 프로바이더를 바꾸면 무용지물이 됨.

### 쿠버네티스란?

클라우드 서비스와는 독립적으로 컨테이너 관리가 가능하다.

컨테이너를 오케스트레이션하기 위한 사실상의 표준이라 할 수 있다.

![[kube2.png]]

컨테이너의 자동 배포, 스케일링 및 로드벨런싱, 관리가 가능하다

어느 클라우드 프로바이더나 리모트 머신에 Kubernetes Configuration을 적용 할 수 있다.

![[kube3.png]]

쿠버네티스는 여러 머신을 위한 도커 컴포즈로 볼 수 있다.


## 아키텍쳐
![[kube4.png]]
- pod : 컨테이너로 볼 수 있다. 가장 작은 단위.
- Worker node: 포드가 들어있는 가상 인스턴스로 볼 수 있다.
- Proxy : 워커 노드의 포드 네트워크 트래픽의 제어를 설정하는 다른 도구
 
- Contorl Plane: 워커 노드와 상호 작용하여 제어하는 컨트롤 센터

### Worker node

![[workernode.png]]
- 포드 자체는 마스터 노드에 의해 관리된다.
- 포드 안에 컨테이너를 여러 개 둘 수 있다.
- 각 포드에 볼륨을 구성함.
- docker 설치 해야함. 
- worker와 master간 통신을 위한 kubelet을 설치해야함. 마스터가 워커의 포드를 제어할 수 있다.
- io 트래픽을 처리하는 kube-proxy도 설치해야함.

### Master node

![[masternode.png]]

- API server : 워커 노드의 kubelet과의 통신을 담당한다.
- Cloud-Controller-Manager : 클라우드 프로바이저에게 뭘해야하는지 알려준다고함...

### 용어
![[compo.png]]
- 여기서 서비스는 독립적인 IP주소를 가진 포드 그룹이다.

### 명령적, 선언적

![[kuberun.png]]
- Docker compose와 유사하게 yml 파일을 통해 쿠버네티스를 돌릴 수 있다. 
- 

[[Kafka]]
[[Operator Pattern]]
[[Docker]]

[[kubectl]]
[[Control Plane Components]]
[[Node Components]]
[[cluster]]

[[Deployment object]]
[[Service]]

[[State]]
