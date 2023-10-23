
- Docker 란?
[[Container]]를 생성하고 관리하기 위한 도구, Container technology 다.  핵심은 컨테이너다.
[[Virtual Machines]] 에서 Host OS위에 각 OS를 설치하는 것과 달리, 도커 엔진위에서 컨테이너를 돌릴 수 있다.

![[Docker.png]]


- Docker Engine

- 도커 실습
1. 해당 레포에서 도커 이미지를 빌드한다.
`Docker build .` 

```
도커 빌드 에러

~/.docker/config.json 에서 
credsStore -> credStore로 바꾸니 잘 돌아감.

{
        "auths": {},
        "credStore": "desktop",
        "currentContext": "default"
}


```


2. 이미지의 ID를 찾고, run한다.
`docker image ls` 
`docker run -p 3000:3000 ID` 포트를 열어줘야 컨테이너와 통신 할 수 있다.

```
 도커 포트 에러 
 docker: Error response from daemon: Ports are not available: exposing port TCP 0.0.0.0:3000 -> 0.0.0.0:0: listen tcp 0.0.0.0:3000: bind: An attempt was made to access a socket in a way forbidden by its access permissions.
 
 net stop winnat으로 해결함. 
```




3. 도커 ps로 이름을 찾고 stop한다.
`docker ps`
```
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS          PORTS                    NAMES
3e218254e45d   0bfd37f8f793   "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    0.0.0.0:3000->3000/tcp   dazzling_johnson
2a7785ddb9ed   0bfd37f8f793   "docker-entrypoint.s…"   48 minutes ago   Up 48 minutes   3000/tcp                 jolly_darwin

```


### 데이터

도커는 다음 3가지로 데이터를 구분할 수 있다.
![[Data.png]]


[[Docker Hub]]
[[Docker Compose]]
[[Volume]]
