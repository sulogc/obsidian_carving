
pod 의 ip는 자주 바뀜. 클러스터 외부에서 pod에 접근 가능하게 해준다. 


![[Serviceob.png]]

`kubectl create service`로 생성이 가능하지만 `kubectl expose`라는 명령어로 구성이 가능하다.

service를 생성하여, deployment에 의해 생성된 pod를 노출한다.

`$ kubectl expose deployment first-app --type=NodePort --port=8080`

NodePort는 워커노드가 할당 받은 IP를 의미함.

`$ kubectl expose deployment first-app --type=LoadBalancer --port=8080`

LoadBalancer는 서비스에 대한 고유한 주소를 생성한다.  또한 service의 일부인 pod에 트래픽을 고르게 분산해준다. 

```
SSAFY@DESKTOP-BLHP263 MINGW64 ~/git_dog/Docker_practice/kub-action-01-starting-setup
$ kubectl expose deployment first-app --type=LoadBalancer --port=8080
service/first-app exposed

SSAFY@DESKTOP-BLHP263 MINGW64 ~/git_dog/Docker_practice/kub-action-01-starting-setup
$ kubectl get services
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
first-app    LoadBalancer   10.108.222.187   <pending>     8080:30968/TCP   12s
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP          123m
```

```
minikube service first-app

|-----------|-----------|-------------|---------------------------|
| NAMESPACE |   NAME    | TARGET PORT |            URL            |
|-----------|-----------|-------------|---------------------------|
| default   | first-app |        8080 | http://192.168.49.2:30968 |
|-----------|-----------|-------------|---------------------------|
🏃  first-app 서비스의 터널을 시작하는 중
|-----------|-----------|-------------|------------------------|
| NAMESPACE |   NAME    | TARGET PORT |          URL           |
|-----------|-----------|-------------|------------------------|
| default   | first-app |             | http://127.0.0.1:49681 |
|-----------|-----------|-------------|------------------------|
🎉  Opening service default/first-app in default browser...
❗  Because you are using a Docker driver on windows, the terminal needs to be open to run it.
```

