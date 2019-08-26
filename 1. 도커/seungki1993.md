### Docker 정리

- 컨테이너화 된 응용프로그램을 개발 , 배포 및 실행할 수 있는 플랫폼
- 특징
  - 가상화 소프트웨어보다 가볍다
  - 이식성
  - 호스트 커널을 공유하여 사용
- Virtual machines와 차이점
  - virtual machine
    - virtual macihine은 Host os에 다시 guest os를 올려서 사용하는 방식
    - Host os의 리소스를 분할하여 guest os를 생성
    - Guest os에 있는 Application이 Host os에 자원을 활용하기 위해서는 Guest -> Host를 거쳐 야한다
  - docker
    - Guest os자체가 없다.
    - Host os에 docker engine이 올라간다.
    - Host OS kernel을 공유하여 사용
- 컨테이너 기술(LXC)
  - 프로세스 격리 기술
  - vm에 경우 머신 별로 독립적인 공간을 가지고 있기에 충돌하지 않는다. 리눅스에도 이와 동일한 역할을 하는 namespaces기능을 내장하고 있다.
  - namespaces
    - 하나의 시스템에서 프로세스를 격리 시킬 수 있는 기술
      - mnt 서로 다른 파일시스템 마운트 지점을 제공
      - pid  독립적인 프로세스 공간을 할당
      - net namespace간에 network 충돌방지
      - ipc 프로세스간의 독립적인 통신 통로 할당
      - uts 독립적인 hostname 할당
      - user 독립적인 사용자 할당
  - Cgroup
    - 자원에 대한 제어를 가능하게 하는 리눅스 커널의 기능
    - 메모리 , CPU ,I/O ,네트워크 리소스를 제어
- docker는 초창기에 LXC를 사용했으나 LXC의 문제점으로 인해 자체 개발한 RunC를 사용

#### Docker image

- 도커 컨테이너를 구성하는 파일 시스템과 실행할 애플리케이션 설정을 하나로 합친것

- 도커 이미지는 파일시스템들의 layer로 구성

  - 도커는 기본적으로 이미지는 해시값
  - docker inspect 도커이미지 이름하면 해당 이미지에 연관된 이미지들의 해시값을 알 수 있다.
  - 도커는 빠른 빌드를 위해 캐시를 사용한다. 즉 캐시된것을 가져다 사용한다.
    - 캐시를 사용하지 않고 모든 이미지를 받고 싶다면 --pull : true옵션을 사용
  - pull 받은 이미지들은 local에 저장이 된다.

- 태그를 달 수 있다.

  - 한태그당 하나의 이미지만 연결
  - 만약 설정 정보를 살짝 변경하고 같은 태그로 빌드시 이전에 있던 이미지는  태그가 <none>이 된다.
  - 태그명도 변경 가능

- 도커 이미지를 생성하기 위해서는 DockerFile을 작성

  ```
  From 도커 이미지의 바탕이 될 베이스 이미지 지정
  	 dockerfile로 이미지를 비들할때 from에 지정된 이미지를 내려 받는다.
  
  workdir 워크 디렉토리 
  
  COPY,ADD 파일과 디렉토리를 호스트에서 docker image로 카피
  	둘의 차이점은 add의 경우 url을 지원하며 , 압축파일의 경우 자동으로 해제
  	주의점 빌드 디렉토리 밖의 파일들은 copy할 수 없다.
  	
  Run 도커이미지를 실행할 때 컨테이너 안에서 실행할 명령어
      이미지를 빌드할때 수행
  
  Cmd 도커 컨테이너를 실행할 때 컨테이너 안에서 실행할 프로세스를 지정
      이미지를 실행할때 수행
      여러개가 있다면 마지막꺼만 수행
  
  Expose 도커 컨테이너가 실행 되었을 때 요청을 기다리고 있는 Listen
  
  entryPoint docker run docker start로 중지된 container을 시작할 때 실행되는 명령어
  		docker file내에 1번만 정의 가능
  		cmd와 다른점은 docker run으로 실행시 cmd는 파라미터 입력시 파리미터 값으로 변경되어 실행이 되나 entrypoint 해당 파라미터를 cmd로 받아서 처리하며 기존 entrypoint를 실행한다.
  ```

- docker image build -t <이름> <도커파일 경로>\
- docker images

#### Docker Container

- 도커 이미지를 기반으로 파일 시스템과 애플리케이션이 구체화 실행되는 상태
- docker container run <imageName>
- 백그라운드로도 실행 가능
- 도커 컨테이너를 실행할 때 컨테이너에 대한 이름을 명시할 수 있다.
  - 컨테이너 관련작업을 하기 위해서는 ID값을 알고 있어야하는데 해쉬값을 외우기는 힘드니깐 이름을 명시하여 이름을 통해 작업이 가능하다.
- 한번 파기된 container는 다시 재시작 할 수 없다.
- container을 중지하면 중지된 상태에서 디스크에 남아 있다.
- 컨테이너를 삭제하면 컨테이너 안에 있는 값들은 당연히 제거된다.
  - 이말은 만약 컨테이너로 db를 실행했다면 적절한 처리를 하지 않았다면 데이터들이 삭제된다.
  - 당연한것 같다
  - 이를 해결할 방법은 volume

```
docker container run <option> <이름>
docker container stop <이름>
docker container restart <이름>
docker container rm <이름>
docker container logs <이름>
docker container exec <이름>
docker container cp <이름>
docker container prune <이름>

```



### Docker Compose

- Compose는 yaml포맷인 설정파일로 여러 컨테이너의 실행을 한번에 관리 할 수 있다.
- compose를 사용하기 위해서는 설치를 해야한다.
- docker-compose up(실행)
- docker-compose down

```
# 만들어진 이미지 실행
version: "3" // compose version
services:
        echo:
                image: example/hi
                ports:
                        - 9000:8080

# 이미지 build 하고 이미지 실행
services:
        echo:
                build: .
                ports:
                        - 9000:8080


```

- compose를 통해서 컨테이너간의 의존설정도 가능하다

```
version: "3.7"
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres

## 의존관계시 redis , db를 먼저 실행시키고 web을 실행시킨다
## 정지는 반대로
```

- 위에서 설명했듯이 컨테이너의 데이터는 휘발성이 강하기 때문에 컨테이너가 삭제되면 해당 데이터들이 삭제된다. compose에서도 volume 설정이 가능하다.

```
version: "3"
services:
        echo:
                build: .
                volumes:
                        - .:/go  // 컨테이너의 go 폴더를 현재 폴더로 마운트
                ports:
                        - 9000:8080

```



### 컨테이너 이식성

- 도커는 호스트 운영체제와 커널 리서스를 공유한다. 즉 이말은 호스트가 특정 cpu , 운영체제에 종속된다.
- 라이브러리의 동적 링크에 문제점과 같은 문제점을 가지고 있다.


#### docker 환경 변수 및 실행인자
-   cmd , entrypoint에 실행인자를 받아서 처리 할 수 있다.
    -   실행인자가 많아지면 복잡해지거나 관리하기가 어렵다\

-   설정 파일 사용
    -   어플리케이션에서 사용하는 설정파일의 경우 컨테이너 실행시 전달하는 형태로 사용

-   도커에서 추천하는 방법은 환경 변수를 통해 어플리케이션을 제어하는 방법을 추천

#### 데이터 볼륨
-   위에서 설명시 컨테이너에 대한 추가 작업이 없다면 해당 컨테이너에서 만들어진 데이터 , 파일등은
    컨테이너가 파기 될때 함께 삭제된다.
-   데이터 볼륨이란 컨테이너가 사용하던 파일 및 디렉터리를 그대로 사용하다록 하는 기술
-   결국 호스트와 컨테이너 사잉의 디렉터리 공유를 하는건데 호스트에서 잘못 만지게 되면 컨테이너에 큰 영향을 줄 수 있다.
-   볼륨에 있는 데이터를 다른 호스트로 이전해야한다면 익스포트해야한다.

