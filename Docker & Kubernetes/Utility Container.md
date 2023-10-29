![[utility container.png]]

- 특정 어플리케이션을 개발하려면, 각종 언어 및 툴을 설치하여 환경을 구성해야한다.. 근데 컨테이너로 환경만 제공해 준다면 어떨까


도커로 노드를 interactive, detach로 실행해보자
`docker run -it -d node`

### `exec` 커맨드

Dockerfile에 지정된 명령어 외에 추가로 실행이 가능하다.  즉 해당 환경 안으로 명령어를 exec할 수가 있다는 것.

`docker exec -it hopeful_hellman npm init`


### 유틸리티 컨테이너

바인드 마운트로 파일은 호스트 머신을 사용하는대신, 노드와 같은 개발환경은 컨테이너로 사용한다.


`Dockerfile` 에 ENTRYPOINT 는 이미지 시작 이후 명령어들을 추가할 수 있다.



