
[[Pod-internal]]

## Pod Internal 활용 

초안

![[podinternal.png]]


- Service에 IP를 고정할 수 있다. 

서비스의 상세(spec)의 타입은 3가지가 가능하다. 
ClusterIP로 할 경우 클러스터 내부에서만 접근이 가능하다. 
NordPort  는 노드 IP를 사용한다. 근데 별로 좋지는 않다. 왜냐하면 레플리카의 경우 다른 노드에 생길 수 있기 때문에 노드의 IP를 서비스에 귀속시키는 것은 클러스터 환경에서 추천되지 않는다. 

가장 유용한 것은 LoadBalancer 타입이다.

외부에서 서비스와 pods에 접근하기 가장 좋은 타입이다.
외부에서 접근 가능한 IP 주소를 생성한다. 실행되는 노드에 관계없이, 모든 포트에 들어오는 요청을 자동으로 분산함. 

ClusterIP에 대해..
쿠버네티스는 서비스에 속한 각각의 Pod에 들어오는 요청을 자동으로 분산 시키는데, 클라우드 프로바이더가 제공하는 전용 로드 밸런싱 서비스를 사용하지 않는다면, IP 주소의 마주하는 외부 세계가 없다는 것을 의미.

물론 내부에선 매우 유용, 하지만 LoadBalancer는 외부 세계를 향한 뷰를 제공함. 

디폴트로 TCP protocol은 있어야하며 port와 targetport를 설정해 줘야한다.

```
apiVersion: v1
kind: Service
metadata:
  name: users-services
spec:
  selector:
    app: users
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8080 # 외부
      targetPort: 8080 # 컨테이너 내부
```



### Pod 간 통신

user의 경우는 외부로 노출하기 위해서 LoadBalance로 설정했지만, 

auth의 경우 클러스터 외부에서 들어오게 할 필요가 없어서 
ClusterIP로 설정하고, kubectl apply로 deployment와 service를 올리고 IP를 받아서 받은놈을 user의 환경변수에 등록해주었다. 

그러나 귀찮다. 수동으로 가져오는 것이. 

쿠버네티스는 자동으로 생성되는 환경변수를 제공한다....?

다른 서비스의 IP주소를 자동으로  가져오게 할 수 있다 .

서비스 명이 auth-service 인 경우

AUTH_SERVICE_SERVICE_HOST라는 환경변수가 자동으로 생성되고 IP로 사용하여 통신에 활용 할 수 있다. 


자동으로 도메인 네임이 생성될 때 네임스페이스는 default에 속하게 된다. 
그리고 호스트 대신 도메인 네임으로 대신할 수 있다 . 디폴트라는 네임스페이스를 붙여줘서 사용할 수 있다 .


```
spec:
      containers:
        - name: users
          image: suhyeng/kub-demo-users:latest
          env:
            - name: AUTH_ADDRESS
              # value: "10.104.57.186"
              value: "auth-service.default"
```



