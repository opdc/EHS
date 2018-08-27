

## [CentOS/RedHat] Docker 

### 서버 환경 준비
Docker를 사용하는 환경을 준비합니다. 
여기서는 AWS 환경(Amazon Linux 2)에 Docker를 설치하는 방법을 설명합니다.

### 도커 환경 구성
yum 리포지터리 설정을 통해 Docker CE를 설정합니다. 

#### 도커를 설치하기 위한 리포지토리 설정

우선 yum-util과 devicemapper 패키지를 설치합니다. 
(Amazon Linux 2는 이미 설치되어 잇습니다) 

```bash
# yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

Docker CE를 설치하기 위해 Docker CE yum 리포지토리를 추가합니다. 

```bash
# yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
grabbing file https://download.docker.com/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo

# ll /etc/yum.repos.d/
total 12
-rw-r--r-- 1 root root  982 Jun 26 00:04 amzn2-core.repo
-rw-r--r-- 1 root root  763 Aug 11 02:03 amzn2-extras.repo
-rw-r--r-- 1 root root 2424 Aug 23 20:27 docker-ce.repo

```
docker-ce.repo가 추가되어 있응 것을 확인할 수 있습니다. 

#### 도커 설치

Docker를 설치합니다. 

```bash
# yum install docker-ce

Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                            | 2.4 kB  00:00:00
docker-ce-stable                                                                                                      | 2.9 kB  00:00:00
docker-ce-stable/x86_64/primary_db                                                                                    |  15 kB  00:00:00
Resolving Dependencies
--> Running transaction check
---> Package docker-ce.x86_64 0:18.06.1.ce-3.el7 will be installed
--> Processing Dependency: container-selinux >= 2.9 for package: docker-ce-18.06.1.ce-3.el7.x86_64
--> Processing Dependency: libcgroup for package: docker-ce-18.06.1.ce-3.el7.x86_64
--> Processing Dependency: libltdl.so.7()(64bit) for package: docker-ce-18.06.1.ce-3.el7.x86_64
--> Running transaction check
---> Package docker-ce.x86_64 0:18.06.1.ce-3.el7 will be installed
--> Processing Dependency: container-selinux >= 2.9 for package: docker-ce-18.06.1.ce-3.el7.x86_64
---> Package libcgroup.x86_64 0:0.41-13.amzn2 will be installed
---> Package libtool-ltdl.x86_64 0:2.4.2-22.2.amzn2.0.2 will be installed
--> Finished Dependency Resolution
Error: Package: docker-ce-18.06.1.ce-3.el7.x86_64 (docker-ce-stable)
           Requires: container-selinux >= 2.9
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest

```

Requires: container-selinux >= 2.9

container-selinux 패키지 버전이 낮아서 에러가 발생했습니다. 

container-selinux 최신 패키지를 설치하려면 CentOS 리포지토리에서 rpm 패키지를 내려받아 설치합니다. 
- CentOS 리포지토리 : http://mirror.centos.org/centos/7/extras/x86_64/Packages/ 
- rpm 파일 내려 받아 설치 하기 : container-selinux-2.42-1.gitad8f0f7.el7.noarch.rpm 패키지 설치 
(2.42 상위 버전은 추가적으로 의존성 패키지를 설치해야 되서 2.42 버전을 설치함)

```bash
# sudo yum install -y http://mirror.centos.org/centos/7/extras/x86_64/Packages/container-selinux-2.42-1.gitad8f0f7.el7.noarch.rpm
```

다시 Docker를 설치합니다. 

```bash
# yum install docker-ce
```

이제 Docker CE버전이 설치됐습니다. 다음 명령어를 사용해  설치 여부를 확인하빈다. 

```bash
# yum list installed | grep docker-ce
docker-ce.x86_64                      18.06.1.ce-3.el7               @docker-ce-stable

```

#### 도커 서비스 시작하기

Docker 서비스를 시작합니다. 

```bash
# systemctl start docker
```

Docker 서비스를 시작 여부를 확인합니다.  

```bash
# systemctl status docker
```

다음 명령어를 사용해 서비스 자동실행설정을 하면 머신 재기동시 Docker 서비스가 자동으로 시작합니다.

```bash
# systemctl enable docker
```

다음 명령어를 사용해 Docker 아

```bash
# docker info
```

### 도커 컨테이너 기본 사용법
공개되어 있는 도커 이미지를 사용해 컨테이너의 사용법을 알아봅니다. 
우선 공개된 도커 이미지를 내려받아 컨테이너를 기동하고 기동 여부가 확인되면 컨테이너를 정지, 삭제, 도커 이미지 삭제를 하여 기본적인 도커 이미지와 컨테이너의 라이프사이클을 살펴봅니다. 

#### 초기 상태 확인
다음 명령어로 로컬 도커 이미지의 현황을 확이할 수 있습니다.
```bash
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```
현재는 헤더만 출력되고 아무것도 나타나지 않습니다. 
[Doker Hub](https://hub.docker.com/explore/)라고 하는 공개된 Docker 이미지를 관리하는 저장서에서 내려받거나 자체 도커 이미지를 작성한 현황이 나타납니다. 

다음 명령어를 사용하면 로컬 환경의 도커 컨테이너의 현황을 확일 할 수 있습니다. (도커 컨테이너란 도커 이미지를 기반으로 기동된 컨테이너를 말합니다)
```bash
# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
아직 컨테니너를 기동하지 않았으므로 아무것도 표시되지 않습니다. 

#### hello-world 이미지를 사용해 컨테이너를 기동하기
다음 명령어를 사용해 공개되어 있는 hello-world 라는 도커 이미지를 내려받아 컨테이너를 기동시켜봅니다. 
```bash
# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9db2ca6ccae0: Pull complete
Digest: sha256:4b8ff392a12ed9ea17784bd3c9a8b1fa3299cac44aca35a85c90c5e3c7afacdc
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/

```

결과를 보니 이미지를 다운로드 하고 'Hello form Docker!'와 추가적인 메시지가 출력된 것을 확인할 수 있습니다. 


