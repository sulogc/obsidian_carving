![[Pod.png]]

kubectl get pods를 통해 상태를 알 수 있다. 

error가 나도 자동으로 재시작 된다. 


### Replica

pod의 복제본 수이다. 

```
SSAFY@DESKTOP-BLHP263 MINGW64 ~/git_dog/Docker_practice/kub-action-01-starting-setup
$ kubectl scale deployment/first-app --replicas=3
deployment.apps/first-app scaled

$ kubectl get pods
NAME                         READY   STATUS    RESTARTS        AGE
first-app-6c94c7fbb5-7spfq   1/1     Running   0               16s
first-app-6c94c7fbb5-bjj5t   1/1     Running   0               16s
first-app-6c94c7fbb5-qbbh8   1/1     Running   2 (3m46s ago)   103m

```

`--replicas=1` 로 하면 나머지 2개는 멈추고, 곧 사라진다. 



