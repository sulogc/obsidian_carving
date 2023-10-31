
쿠버네티스도 환경변수 파일을 지원한다. 

deployment yml의 컨테이너 부분을 수정하면 제공할 수 있다.


```
containers:
        - name: story
          image: suhyeng/kub-data-demo:1
          env:
            - name: STORY_FOLDER
              value: 'story'
          volumeMounts: # 컨테이너 내부에서 파드에 있는 볼륨을 쓰기위해 마운트
          - mountPath: /app/story # 컨테이너 내부 경로
            name: story-volume # 볼륨을 마운트
```

#### ConfigMaps

각각의 deployment 단위로 환경변수를 줄 수 있지만, 전역적으로 줄 수가 있다.  environment.yaml을 작성해보자.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: data-store-env
data:
  folder: 'story'
  # key : value..
```

그리고 쓰는 곳에서는..

```
containers:
        - name: story
          image: suhyeng/kub-data-demo:2
          env:
            - name: STORY_FOLDER
              valueFrom:
                configMapKeyRef:
                  name: data-store-env
                  key: folder
```

