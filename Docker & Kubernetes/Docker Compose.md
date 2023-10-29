
- 도커 컴포즈
`docker build` 와 `docker run` 을 대체할 수 있는 도구이다. 단순히 대체하는 것 뿐 아니라 여러개의 이미지를 빌드하고 여러개의 컨테이너를 런할 수 있다. 

물론, run 뿐만 아니라 stop도 가능. 다중 컨테이너 애플리케이션을 시작하거나 관리 할 수 있다.

![[Dockercompose.png]]

특징
- 커스텀 이미지인 Dockerfile 을 대체하진 않는다. (같이 사용함.)
- 도커 컴포즈는 이미지나 컨테이너를 대체하진 않는다. (설정 및 관리)
- 다수의 호스트(여러 서버)에서 다중 컨테이너를 관리하는데 적합하진 않다. (이건 쿠버가 함)

그럼 뭘 해줌? 포트, 볼륨, 네트워크, 환경변수 등 이전에 직접한걸 미리 설정가능
![[Dockercompose2.png]]

### 어떻게 만드는가

백엔드와 프론트 폴더와 동일한 디렉토리에 docker-compose.yaml 파일을 생성한다.

yaml은 들여쓰기를 통해 구성 옵션 간의 종속성을 표현하는 특정한 텍스트 포맷이다. 

- 이미지가 없는 경우 docker-compose.yml 의 상대 경로로 하위 Dockerfile의 path를 알려주면 된다.

- docker-compose.yml
```
# 들여쓰기로 직계 후손
# 도커 컴포즈에서 -d 와 --rm 은 디폴트다
# 도커 컴포즈는 포함된 service들을 모두 동일한 네트워크로 간주하고 관리하게 해준다.
# 같은 컴포즈에서 동일한 볼륨을 쓰면 공유하게된다. 명명 볼륨만 적음. 익명과 바인드는 적을 필요가 없다.

# 도커 컴포즈의 버젼
# 사용가능한 구문 구분
version: "3.8"

# 서비스 키
services:
    mongodb:
        image: 'mongo'
        volumes:
            - data:/data/db
        env_file:
            - ./env/mongo.env
      # environment:
      #   MONGO_INITDB_ROOT_USERNAME: max
      #   MONGO_INITDB_ROOT_PASSWORD: secre
        # - MONGO_INITDB_ROOT_USERNAME=max 으로 표현해도 된다.
        # networks:
        #     - goals-net # 특정 네트워크에 추가 가능.

    backend:
        build: "./backend"
        # build:
        #     context: ./backend # context는 Dockerfile이 이미지로 복사할 경로를 포함해야 한다.
        #     dockerfile: Dockerfile # Dockerfile이면 위에 처럼 경로만 알려줘도 되지만, 이름이 다를경우 지정가능하다.
        #     args:
        #         some-arg: 1
        ports:
            - '80:80'
        volumes:
            - logs:/app/logs # 명명 볼륨
            - ./backend:/app # 바운드 마운트를 상대경로를 통해 지정할 수가 있다.
            - /app/node_modules
        env_file:
            - ./env/backend.env
        depends_on: # 다른 컨테이너에 영향을 받는 경우. 즉 컨테이너 실행에 순서가 있는 경우.
            - mongodb
            
    frontend:
        build: ./frontend
        ports:
            - '3000:3000'        
        volumes:
            - ./frontend/src:/app/src
        stdin_open: true # 이 두 가지가 -it 옵션이다.
        tty: true
        depends_on:
            - backend

# 볼륨에 대한 키를 추가
volumes:
  data:
  logs:
```

이후 `docker compose up`을 하면, 
다음과 같이 기본 네트워크 및 볼륨을 생성해 준다.
```
[+] Running 3/3
 ✔ Network compose-01-starting-setup_default      Created 0.8s 
 ✔ Volume "compose-01-starting-setup_data"        Created 0.0s 
 ✔ Container compose-01-starting-setup-mongodb-1  Created        
```

`-d` 옵션으로 detach 실행을 해주자.

`docker compose down`으로 멈출 수 있다. 다만 볼륨은 삭제되지 않는데, 
`down`에 `-v` 옵션도 넣어주면 된다.

컨테이너 이름은 더 길게 할당되는데, 서비스 이름을 통해 통신이 가능하다.

