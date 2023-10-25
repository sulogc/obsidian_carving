호스트 머신에 매핑을 시키는 것. 

컨테이너를 런할때, -v 옵션에 절대경로를 넣어서 바인드 마운트를 한다. 

`docker run -d -p 3000:80 --rm --name feedback-app -v feedback`

