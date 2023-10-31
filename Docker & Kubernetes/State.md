
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

뭐. 노드 별로 독립시키고 싶다면 이렇게 해도 된다.

## CSI Volume

Container Storage Interface

파일시스템도 프로바이더에 따라 종류가 다양할 수 있다. 모든 종류에 대해 볼륨의 타입을 정의 놓을 수는 없기 때문에, 인터페이스를 정의해 두었다. 


## Persistent Volumes 

파드에 노드에 독립적인 볼륨을 보자.  

독립 스토리지 그 이상의 개념이다. 
핵심은 볼륨이 파드에서 분리되는 것인데,  파드의 수명주기와 무관해지는 것.

deployment 와 파드에서 계속 정의할 필요 없이, 한 번 만들어 놓고 갖다가 쓰면 된다. 

![[pvol.png]]


![[pvclaim.png]]

영구 볼륨을 사용하기 위해서는 pv claim을 노드 안에 파드옆에 붙여서 써야한다. 이렇게 하면 파드안에 컨테이너가 해당 볼륨을 쓸 수 있는지 claim을 걸 수가 있다.

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem  # 파일시스템과 블록이 있다.
  accessModes:
    - ReadWriteOnce     # 볼륨이 단일 노드에 의해 읽기/쓰기 볼륨으로 마운트
    # - ReadOnlyMany      # 읽기 전용이지만 여러 노드에서 요청가능.
    # - ReadWriteMany     #
  hostPath:
    path: /data
    type: DirectoryOrCreate
```

볼륨을 만들고 그에 맞는 클래임을 만들 수 있는데, 재밌는건 클레임을 만들고 클레임이 나 어떤 볼륨 줘 라고 하는 동적 볼륨 프로비저닝 이라는 토픽이 있다. 

근데 그건 너무 고급 관리라 잘 안 씀. 그냥 정적 볼륨 프로비저닝 쓰자. 

그리고 엑세스모드도 설정이 가능한데, 특정 파드에 대해 리드만 할지 쓰기까지 할지 설정이 가능하다. 

그리고 리소스를 적어서 뭘 얻고싶은지 명시하게 한다.

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
spec:
  volumeName: host-pv
  accessMode:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

그리고 볼륨의 타입을 설정하다.

```
volumes:
        - name: story-volume
          persistentVolumeClaim:
            claimName: host-pvc
```


