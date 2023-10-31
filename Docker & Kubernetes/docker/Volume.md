도커 컨테이너는 삭제되면, 안에 read-write 하던 임시 데이터 들이 지워진다. 영구 저장을 가능하게 하는 볼륨에 대해 알아보자.

볼륨은 컨테이너가 마운트 되어있는 호스트 머신의 폴더이다. 즉 컨테이너나 이미지에 있는 것이 아니다.

![[Volume.png]]
- 폴더와 폴더를 매핑해두는 개념. 즉 서로 접근이 가능하다...!

`VOLUME [ "/app/feedback" ]` 을 추가하여 볼륨을 설정해주자.

도커를 실행시키고 api를 호출하면... 안된다. 

```
docker logs fdv

...
cross-device link not permitted, rename '/app/temp/2f.txt' -> '/app/feedback/2f.txt'
...

```

도커는 실제로 컨테이너 파일을 파일 시스템 내부의 다른 폴더로 옮기지 않는다....
기존의 `rename` 을 `copyFile` 과 `unlink` 로 바꾸어 주자.
즉 옮기기 대신 복사 후 삭제를 하는 것!


## Anonymous/Named Volume & Bind Mounts

- 익명 볼륨

컨테이너 실행시 명명하지 않으면 컨테이너가 삭제될 때 볼륨도 삭제된다. 
컨테이너에 이미 존재하는 특정 데이터를 잠그는데 유용하다. 


- 명명 볼륨

컨테이너 삭제시에도 사라지지않고 영구 보관이 가능하다. 하지만 도커가 관리하기 때문에 로컬에서 접근 할 수가 없다. 

다른 컴퓨터에도 해당 명명한 이름으로 바인딩이 가능하다.
다음은 `feedback`이라고 볼륨명을 정해서 컨테이너를 런하는 코드. 

```
docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volumes
```

볼륨에는 익명 볼륨이 있다. 

다양한 컨테이너가 공유할 수 있다. 


- [[Bind Mounts]]

### Read Only Volume

사실  바인드 마운트를 했을 때, 컨테이너가 app 폴더에 쓸 수 있게 하는 것이 아니라. 로컬 파일을 변경할 수 없어야한다. 

컨테이너에서 실행 중인 애플리케이션은 본인 스스로를 변경해서는 안된다.

볼륨의 디폴트 권한은 read-write다. 

컨테이너를 런할 때, :ro 를 붙여서 권한도 같이 설정할 수 있다. 
물론 write가 가능한 폴더를 빼두어야한다. 

구체적인 하위 볼륨설정이 우선되기 때문에 예외처리가 가능하다. 

### 볼륨 관리

`docker volume ls`를 통해 볼륨 목록을 볼 수 있다.
바인트 마운트는 볼 수 없는데, 도커에 의해서 관리되는 볼륨이 아니기 때문.

`docker volume create`를 통해서 볼륨 생성도 가능.

`docker volume inspect name`으로 검사도 가능하다. 여기서 볼륨의 위치를 볼 수 있는데, 컨테이너의 os상의 위치이므로 호스트에서 접근 할 수 없다. 


[[State]]
