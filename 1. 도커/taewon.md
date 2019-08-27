## 2.1 컨테이너 이미지
- 컨테이너 이미지
    - 애플리케이션을 실행하기 위해 필요한 모든 파일을 **캡슐화한 바이너리 패키지**
    - 도커 이미지 포멧은 가장 많이 사용되고 있는 컨터이너 이미지 포맷
    - 계층화 가능
    - 컨테이너는 크게 두가지 범주로 구분
        ```
        - 시스템 컨테이너
            - 가상 시스템처럼 동작
        - 애플리케이션 컨테이너  
            - 단일 애플리케이션을 실행
        ```

## 2.2 도커를 활용한 어플리케이션 이미지 생성
- 도커 파일
- 이미지 보안
    - 비밀번호 하드코딩 금지
    - 상위 계층에서 파일을 삭제해도 하위 계층에 업데이트가 되지 않음
- 이미지 크기 최적화
    ```
    - 최상위 계층에 큰 파일 존재, 그 다음 계층에서 해당 파일 삭제
    - 최하위 계층에서 도커 이미지를 받을 경우 큰 파일에 접근할 수 없지만 네트워크를 통해 전송됨
    ```
    ```
    - 하위 계층에 더 자주 변경되는 라이브러리를 포함시켜야 됨
    - 상위 계층에 더 자주 변경되는 라이브러리를 포함시킬 경우 하위 계층에서도 자주 업데이트를 해줘야 하는 문제 발생
    ```

  
# docker 개요
## docker
- 컨테이너 기반의 오픈소스 가상화 플랫폼
- 컨테이너 기술을 이용하여 프로세스를 격리시키는 방식

![conatiner](https://www.docker.com/sites/default/files/d8/styles/large/public/2018-11/container-what-is-container.png?itok=vle7kjDj)


## container
### 운영 체제 수준 가상화
- 운영 체제의 커널이 하나의 사용자 공간 인스턴스가 아닌, 여러 개의 격리된 사용자 공간 인스턴스를 갖출 수 있도록 하는 서버 가상화 방식 (위키피디아)
- container 는 운영 체제 수준의 가상화를 지원하는 기술


### linux container
- 시스템의 나머지 부분과 격리된 하나 이상의 프로세스 세트
- 프로세스를 실행하는 데 필요한 모든 파일은 고유한 이미지에서 제공

![conatiner](https://www.redhat.com/cms/managed-files/what-is-a-container.png)


### 가상화 vs container 
- 가상화
    - 단일 하드웨어 시스템에서 여러 운영 체제(Windows 또는 Linux)가 동시에 실행
- container    
    - 동일한 운영 체제 커널을 공유하고 시스템의 나머지 부분으로부터 애플리케이션 프로세스를 격리
- 차이점
    - 가상화는 하이퍼바이저를 사용하여 하드웨어를 에뮬레이션하고 이를 통해 여러 운영 체제를 동시에 실행
        - 컨테이너를 사용하는 것만큼 경량화할 수 없음
    - Linux 컨테이너는 운영 체제에서 기본으로 실행되고 모든 컨테이너 전체에서 운영 체제를 공유하므로 애플리케이션과 서비스를 가볍게 유지할 수 있음
    
![conatiner](https://www.redhat.com/cms/managed-files/virtualization-vs-containers.png)


### docker container
- 0.9 버전 이후 부터 기존의 Linux container(LXC) 기반에서 libcontainer(opencontainers/runc 으로 변경 됨) 으로 변경

![conatiner](https://qjzgi1yq6h412dbb267gpl71-wpengine.netdna-ssl.com/wp-content/uploads/2017/03/linux-vs-docker-comparison-architecture-docker-lxc.png)
- runc
    - Open Container Initiative (OCI) 가 관리하고 있는 오픈소스  
    - OCI 가 container 관련 스펙을 관리하고 있는 것으로 보여짐
    - the Runtime Specification (runtime-spec) 와 the Image Specification (image-spec) 을 정의


### docker image
- 컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것
- Immutable
- Docker hub에 등록하거나 Docker Registry 저장소를 직접 만들어 관리할 수 있음


### docker engine?
- 출처 : https://stackoverflow.com/questions/41645665/how-containerd-compares-to-runc
    ```
    Docker Engine is the higher level component which manages a bunch of things such as orchestration, managing volumes, networking, scaling and so on
    ```
- 출처 : https://www.youtube.com/watch?v=wW9CAH9nSLs&feature=youtu.be
    ```
    it's isloated at the process level
    it has it and it has its own file system
    ...
    What docker does is a little demon 
    You drop on the server 
    Any server that has a little linux kernel that's modern enough and it runs processes for you
    But it runs them in a way that they're super isolated like a describe    
    ```

- docker 엔진은 docker container 를 관리하기 위한 엔진

![conatiner](https://i.stack.imgur.com/5aXF6.png)

- 기존 기술을 사용하기 쉽게 한 엔진으로 이해
 

### 장단점
- 장점
    - 빠른 시작과 종료 속도
        - 프로세스를 시작하는 것이기 때문에 매우 빠르게 시작할 수 있음
    - 높은 집적도
        - 실행되는 프로세스를 위한 메모리만 필요하기 때문에 낮은 사양의 환경에서 더욱 활용도가 높아짐
    - 낮은 오버헤드
        - 가상화를 위한 하드웨어 에뮬레이트 (emulate) 단계 없이, 분리된 공간을 만들기 때문에 오버 헤드가 줄어듬
-단점
    - HOST OS 에 종속적
        - Linux의 모든 배포판은 설치할 수 있지만, 실행중인 커널은 하나이기 때문에 엄밀하게는 컨테이너는 Host OS 의 커널을 사용
        - 예를 들어 Ubuntu Host OS에서 컨테이너에 CentOS를 설치한다고 해도 실행중인 커널은 Ubuntu 커널   
    - 컨테이너별 커널구성이 불가능
        
        
### 참조
- container
    - https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81_%EC%B2%B4%EC%A0%9C_%EC%88%98%EC%A4%80_%EA%B0%80%EC%83%81%ED%99%94    
    - https://www.docker.com/resources/what-container
    - https://www.redhat.com/ko/topics/containers/whats-a-linux-container
- docker container
    - https://stackoverflow.com/questions/34152365/difference-between-lxc-and-libcontainer
- docker
    - https://docs.docker.com/get-started/
    - https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html
    - http://wiki.rockplace.co.kr/pages/viewpage.action?pageId=3868344    
- https://en.wikipedia.org/wiki/Open_Container_Initiative    
- https://github.com/opencontainers/runc
- https://stackoverflow.com/questions/34152365/difference-between-lxc-and-libcontainer
- https://www.youtube.com/watch?v=wW9CAH9nSLs&feature=youtu.be