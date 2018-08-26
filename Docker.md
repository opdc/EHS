

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


### APR과 APR-util 설치
Apache 2.4를 소스 코드로부터 설치하려면 [APR(Apache Portable Runtime)](http://apr.apache.org/)이 설치돼 있어야 한다.
APR은 OS간의 차이를 해결해주는 라이브러리이다. 리눅스만해도 다양한 종류가 있어 OS에 따라 다르게 제공하고 있는 기능이 많이 있다. 따라서 Apache는 APR 라이브러를 사용함으로써 APR의 사용하고 다양한 환경에서도 동작하게 해주는 라이브러리이다. 이전에는 Apache에 포함되어 있었지만 현재는 분리해서 프로젝트가 관리되고 있다. 

[APR 다운로드 페이지](http://apr.apache.org/download.cgi)에서 tar.gz의 소스코드를 다운로드해 설치한다. 현재 APR의 최신 버전은 1.6.3 이다.

```bash
# cd /usr/local/src
