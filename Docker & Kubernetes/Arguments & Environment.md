

### env

도커는 빌드 타임 인수와 런타임 환경 변수를 지원한다. 

![[Arg&Env.png]]

`ENV PORT 80`
`EXPOSE $PORT`

`-env PORT=8000` 옵션으로 컨테이너 런할때 설정 가능하다. 

`-e` 로 함축 가능

또는 `.env` 파일에 설정해 두고 `--env-file ./.env` 로 호출 해서 사용해도 된다. 


### arg

`ARG DEFAULT_PORT=80` 를 정의하고 환경변수의 디폴트 값을 넣을 수 있다.

`ENV PORT $DEFAULT_PORT` 

그리고 이미지 빌드시 `--build-arg DEFAULT_PORT=8000` 옵션을 둘 수 있다.

이미지가 빌드되고 나면 ARG값은 잠긴다.

