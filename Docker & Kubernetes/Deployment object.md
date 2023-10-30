
Pod의 생성 및 생명 주기 등 전반적인 것을 관리한다.

![[Deploymentob.png]]


pod 와 pod의 컨테이너를 모니터링하며, 실패하면 pod를 재시작한다. 

코드를 바꾸고 반영하려면 

1. 다시 빌드한다. 
`$ docker build -t suhyeng/kub-first-app .`

그리고 `docker push suhyeng/kub-first-app` 으로 푸쉬하자.

2. deploymet가 있는지 확인
```
$ kubectl get deployments
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
first-app   3/3     3            3           110m
```

3. 이미지를 세팅

`$ docker set image deployment/first-app kub-first-app=suhyeng/kub-first-app`





[[pod]]
