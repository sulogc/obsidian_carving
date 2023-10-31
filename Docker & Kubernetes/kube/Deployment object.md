
Pod의 생성 및 생명 주기 등 전반적인 것을 관리한다.

![[Deploymentob.png]]


pod 와 pod의 컨테이너를 모니터링하며, 실패하면 pod를 재시작한다. 

코드를 바꾸고 반영하려면 

1. 다시 빌드한다.  이미지를 빌드할 때 테그가 달라야 반영된다.
`$ docker build -t suhyeng/kub-first-app:2 .`

그리고 `docker push suhyeng/kub-first-app:2` 으로 푸쉬하자.

2. deploymet가 있는지 확인
```
$ kubectl get deployments
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
first-app   3/3     3            3           110m
```

3. 이미지를 세팅

`$ kubectl set image deployment/first-app kub-first-app=suhyeng/kub-first-app:2`

```
$ kubectl rollout status deployment/first-app
deployment "first-app" successfully rolled out
```

###  Rollout fail

```
$ kubectl rollout status deployment/first-app
Waiting for deployment "first-app" rollout to finish: 1 old replicas are pending termination...


SSAFY@DESKTOP-BLHP263 MINGW64 ~/git_dog/Docker_practice/kub-action-01-starting-setup
$ kubectl get pods
NAME                         READY   STATUS             RESTARTS   AGE
first-app-6b5bdb8dd5-9chkm   1/1     Running            0          7m19s
first-app-866777f7b4-9mw66   0/1     ImagePullBackOff   0          56s
```


history를 통해 디플로이먼트 변경 기록을 볼 수 있다.
```
$ kubectl rollout history deployment/first-app
deployment.apps/first-app 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>
```

그리고 undo 하고 돌아갈 수 있다.

```
$ kubectl rollout undo deployment/first-app --to-revision=1
deployment.apps/first-app rolled back
```

#### 동적 객체
deployment가 생성 후 pod수를 확장 할 때, pod는 이미 존재하는 deployment에 의해 자동으로 관리된다. 

`selector` `matchLabels` 를 통해 deployment에 의해 관리될 pod를 명시할 수 있다.


```
apiVersion: apps/v1
# deployment에 대한 object, pod에 대한 object 들을 설정해줌.
# 생성하려는 항목을 쿠버네티스에게 줘야함.

kind: Deployment
metadata:
  name: second-app-deployment
  labels:
    group: example

spec:
  replicas: 1 # 0 으로하면 실행이 안됨.
  selector:
    matchLabels:
      app: second-app
      tier: backend
    # matchExpressions:
    #   # 앱 레이블이 이범위의 값을 갖는 모든 포드를 선택한다.
    #   - {key: app, operator: In , values: [second-app, first-app]}

  template:   # deployment template는 pod에 대한 설정이므로 kind가 필요 없다.
    metadata:
      labels:
        app: second-app
        tier: backend
    spec: # 상위 spec은 deployment에 대한 것이고, 이것은 pod에 대한 spec

      containers:
        - name: second-node # 워커 노드같은게 아니라 노드앱
          image: suhyeng/kub-first-app:2
          imagePullPolicy: Always # 태그를 붙이지 않아도 항상 이미지를 새로 받아온다. 같은 태그로 재배포 하여도 pull해온다.
          livenessProbe: # Pod의 생명주기중 Running 상태에서의 동작 체크, 응답이 없으면 재시작 한다.

            httpGet:
              path: /
              port: 8080
            periodSeconds: 10 # 몇 초 주기로 체크
            initialDelaySeconds: 5 # 시작하고 몇 초 후
        # - name: ...
        #  image:
```



[[pod]]


