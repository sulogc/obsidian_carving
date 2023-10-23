
Dissolver의 개념.. 
이미지는 템플릿, 컨테이너의 청사진이다. 모든 코드와 환경에 대한 변수를 저장하는 일종의 패키지로 보면된다. 
[[Container]]는 실제 App이 돌아가는 인스턴스이다(run을 통해서 생성됨). 즉 이미지를 정의하면 여러 복수의 컨테이너를 다른 서버에서 실행시킬 수 있다. 

pre-built 된 이미지를 가져오거나 생성해서 사용하면 된다. 
[[Docker Hub]]에 많음. 

## 이미지 실행 해보기

1. 허브에서 다운로드. 
`docker run node` 하면 다운된다. 

도커 내부에서 인터렉티브 쉘이 돌아가고있다. 그런데 도커는 독립적으로 작동하기 때문에 노출되진 않았음. 

-  it 플래그
`docker run -it node` -it 플래그는 interactice의 줄임말이다.  도커 컨테이너 내부로부터 호스팅 머신으로 대화형 세션을 노출하게 된다. 


```
깃 배쉬로 할 시 에러가 나는 이슈.
$ docker run -it node
the input device is not a TTY.  If you are using mintty, try prefixing the command with 'winpty'

# winpty를 붙여야 함. 

$ winpty docker run -it node
Welcome to Node.js v21.0.0.
Type ".help" for more information.
> 1 + 1
2
>

```

2. 도커 파일로 직접 이미지 만들어보기.

`DockerFile` 은 도커 데몬에 의해 관리된다. 

`FROM`은 베이스 이미지에 본인의 이미지를 구축할 수 있다. 도커 허브 상이나 로컬에 있는 이미지를 넣는다.
처음 도커허브에서 실행해보면 캐시되어서 로컬에도 존재하게된다. 

`WORKDIR /app` 컨테이너 내부에서 명령어가 실행될 위치를 지정할 수 있다.

`COPY . ./` 
첫 번째 경로는 컨테이너의 외부, 이미지의 외부 경로로 이미지로 복사되어야 할 파일들이 있는 곳. 
예시에서는 Dockerfile이 존재하는 디렉토리로 볼 수 있다. 
두 번째 경로는 데이터가 복사되는 컨테이너 내부의 경로이다.  `./`는 작업 디렉토리를 의미함. 

`RUN npm install` 로 복사한 후 종속성을 유지하면 된다. 

`EXPOSE` 를 통해 컨테이너를 특정 포트로 노출 시켜야 통신이 가능하다.

`CMD` 는 이미지가 생성될 때 실행되지 않고, 이미지를 기반으로 컨테이너가 생성될 때 실행된다. 이미지는 청사진이기 때문에 빌드될 때 굳이 서버를 실행할 이유가 없다. 
```
FROM node

WORKDIR /app  

COPY . ./

RUN npm install

EXPOSE 80

CMD ["node", "server.js"]
```


하지만 그냥 런하면 호스트 머신의 포트어느 포트가 해당 도커포트와 연결되는지 알 수가 없으므로 run 할 때, 포트를 지정해줘야한다. 

`$ docker run -p 3000:80 84d1016c860a`


그리고 코드를 수정 후 적용을 하려면, 해당 코드를 다시 이미지로 빌드하고 컨테이너를 생성해 줘야 반영이 된다 .


## Layer 기반 아키텍쳐

이미지를 빌드 할 때, 변경된 부분과 이후의 모든 명령이 재평가 된다. 
uging cache.

도커는 기본적으로 모든 명령어에 대해 캐쉬한다. 다시 빌드할 필요가 없으면 캐시된 결과를 사용한다. 
![[Layer.png]]

이미지는 다시 빌드하지 않는 이상 Read-only다. 
CMD 전까지는 별개의 레이어이다. 

소스 코드를 수정하면, 모든 파일의 COPY를 다시해야 한다. 그리고 후속 레이어도 다시 빌드를 하게 된다. 

그래서 소스 코드가 바뀌어도  굳이
`RUN npm install`를 다시 하게된다. 이를 최적화 할 수 있는데, 

```
FROM node

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 80

CMD ["node", "server.js"]
```
위 상황에서, npm install에 필요한 package.json만 Copy 한 후 npm install을 먼저 해도 된다. 이렇게 하면 오래 걸리는 npm install이 변경이 일어난 COPY . /app 부분 후속 레이어로 오지 않게 되므로 시간을 단축 시킬 수 있다.
```
FROM node

WORKDIR /app

COPY package.json /app

RUN npm install

COPY . /app

EXPOSE 80

CMD ["node", "server.js"]
```


```
[+] Building 0.9s (10/10) FINISHED                                           docker:default
 => [internal] load build definition from Dockerfile                                   0.0s
 => => transferring dockerfile: 152B                                                   0.0s 
 => [internal] load .dockerignore                                                      0.0s 
 => => transferring context: 2B                                                        0.0s 
 => [internal] load metadata for docker.io/library/node:latest                         0.8s 
 => [1/5] FROM docker.io/library/node@sha256:bf718fc580177cd927173c8617cf7f527a1b7f62  0.0s
 => [internal] load build context                                                      0.0s 
 => => transferring context: 1.14kB                                                    0.0s 
 => CACHED [2/5] WORKDIR /app                                                          0.0s 
 => CACHED [3/5] COPY package.json /app                                                0.0s 
 => CACHED [4/5] RUN npm install                                                       0.0s 
 => [5/5] COPY . /app                                                                  0.0s 
 => exporting to image                                                                 0.0s 
 => => exporting layers                                                                0.0s 
 => => writing image sha256:f1bbbd303ca2a880f6fd9bd4dc4cc0db21b2d15879a9fb9b290a93397  0.0s
```

컨테이너는 이미지 위에 얇게 추가된 레이어이다. 


[[Layer Architecture]]


## 이미지 분석 및 관리

`docker images` 를 통해 image 리스트를 볼 수 있다 .

```
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
<none>       <none>    570ff0d02af0   29 minutes ago   1.02GB
<none>       <none>    e4ea10d8dc9c   30 minutes ago   1.02GB
<none>       <none>    f1bbbd303ca2   2 hours ago      1.11GB
<none>       <none>    26780a9f1f65   2 hours ago      1.11GB
<none>       <none>    b05633d8e512   2 hours ago      1.11GB
<none>       <none>    d62f7d0b3cd2   3 hours ago      1.11GB
```

노드 뿐만아니라 리눅스 운영체제까지 포함된 사이즈이다.

컨테이너는 `docker rm`  이미지는 `docker rmi` 로 지울 수 있다. 
해당 이미지를 기반으로하는 컨테이너가 모두 중지되어야만 지울 수 있다. 

`docker image prune` 을 통해서 사용되지 않는 이미지를 모두 지울 수 있다. 

`docker image inspect ID` 를 하면 이미지에 대한 정보를 볼 수 있다.



### 이미지에 이름 과 태그를 지정하기

`node` 와 같이 이미지에 이름을 특정할 수 있다.
그리고 tag를 통해 이름 그룹내에서 이미지의 특정 버젼을 지칭 할 수 있다. 

`docker build -t name:tags .` 를 통해서 name과 tag를 지정할 수 있다. 




