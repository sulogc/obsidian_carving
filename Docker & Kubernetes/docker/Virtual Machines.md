

- VM이란

SW동작에 모든 것이 보유된 캡슐화된 환경이다.
![[virtual machine.png]]
Host OS위에 VM을 설치. VM이 OS를 포함하기 때문에 OS를 또 설치하는 격.

가상 운영체제를 쓰는 여러 VM 운용시 Overhead가 크다는 것이 가장 큰 문제이다.
매번 새로운 리소스를 설치해야 된다. 


- 장점 : 분리된 환경 제공, 환경별 구성을 제공. 안정적 공유와 재생산
- 단점 : 중복에 의한 공간 낭비, 호스트 시스템위에 추가 시스템이 실행되고 있기 때문에 성능이 저하됨,  모든 시스템에 VM을 설정해야됨. -> 배포할 때도 설정을 또 해야된다. 

