
Dissolver의 개념.. 
이미지는 템플릿, 컨테이너의 청사진이다. 모든 코드와 환경에 대한 변수를 저장하는 일종의 패키지로 보면된다. 
[[Container]]는 실제 App이 돌아가는 인스턴스이다(run을 통해서 생성됨). 즉 이미지를 정의하면 여러 복수의 컨테이너를 다른 서버에서 실행시킬 수 있다. 

pre-built 된 이미지를 가져오거나 생성해서 사용하면 된다. 
[[Docker Hub]]에 많음. 

### 이미지 실행 해보기

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
