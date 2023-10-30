
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


[[pod]]
