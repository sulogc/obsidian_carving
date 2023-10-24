도커 컨테이너는 삭제되면, 안에 read-write 하던 임시 데이터 들이 지워진다. 영구 저장을 가능하게 하는 볼륨에 대해 알아보자.

볼륨은 컨테이너가 마운트 되어있는 호스트 머신의 폴더이다. 즉 컨테이너나 이미지에 있는 것이 아니다.

![[Volume.png]]
- 폴더와 폴더를 매핑해두는 개념. 즉 서로 접근이 가능하다...!

`VOLUME [ "/app/feedback" ]` 을 추가하여 볼륨을 설정해주자.

도커를 실행시키고 api를 호출하면... 안된다. 

```
docker f

```
cross-device link not permitted, rename '/app/temp/2f.txt' -> '/app/feedback/2f.txt'