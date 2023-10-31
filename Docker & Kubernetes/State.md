
![[State.png]]

State는 App에 의해서 사용되지만, 잃으면 안되는 데이터다.

User가 만들어 내는 데이터 - DB, FS에 저장. 
app이 만들어 내는 데이터 - memory, tmp에 저장. 

둘 다 잃으면 안되기 때문에 volume을 사용한다.  

쿠버네티스로 오면서 볼륨은 매우 다양하고, 많아진다.  
pod에 속하게 되어 관리대상이 된다. 

pod에 속하긴 때문에 볼륨의 수명은 pod의 수명에 의존하게 된다. 


### 쿠버의 볼륨

쿠버의 볼륨은 다양한 드라이버와 유형이 있다. 도커의 볼륨은 호스트머신 어딘가에 생성되는 폴더 정도로 볼 수 있지만, 쿠버에선 다르다. 

파드 안에 컨테이너와 볼륨이 있다. 컨테이너와 별개로, 파드에 종속되며, 컨테이너에서 해당 볼륨을 쓰기위해서 마운트를 해줘야한다. 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story
          image: suhyeng/kub-data-demo:1
          volumeMounts: # 컨테이너 내부에서 파드에 있는 볼륨을 쓰기위해 마운트
          - mountPath: /app/story # 컨테이너 내부 경로
            name: story-volume # 볼륨을 마운트
      volumes:
        - name: story-volume
          emptyDir: {}  # 파드에 종속, 컨테이너가 재시작 되도 살아있다. 드라이버
                        # 도커 컴포즈에서 정의한 stories라는 명명 볼륨의 타입을 명시해준다.
```


### hostpath

노드를 기준으로 패스를 지정할 수가 있다.  물론 여러 호스트 환경에서 공유할 수는 없으나, emptyDir보다는 용이하다. 

호스트 패스를 통해 노드의 파일, 폴더를 공유할 수 있다.  바인드 마운트와 유사하다. 


파드 안 템플릿의 스펙.
```
spec:
      containers:
        - name: story
          image: suhyeng/kub-data-demo:1
          volumeMounts: # 컨테이너 내부에서 파드에 있는 볼륨을 쓰기위해 마운트
          - mountPath: /app/story # 컨테이너 내부 경로
            name: story-volume # 볼륨을 마운트
      volumes:
        - name: story-volume
          hostPath:
            path: /data  # 호스트 머신의 경로, 바인드 마운트와 유사하다.
            type: DirectoryOrCreate # 존재하면 바인드하고, 아니면 폴더를 생성한다
```

이것도 좋지만 여전히 노드에 종속되어서 클러스터 환경에서 사용하기엔 용이하지 않다.

뭐. 노드 별로 독릾시키고 싶다면 이렇게 해도 된다.


## CSI Volume

Container Storage Interface

파일시스템도 프로바이더에 따라 종류가 다양할 수 있다. 프로바이더의 종류를 
