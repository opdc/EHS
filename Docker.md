

## [CentOS/RedHat] Docker 

### 서버 환경 준비
Docker를 사용하는 환경을 준비합니다. 
여기서는 AWS 환경(Amazon Linux 2)에 Docker를 설치하는 방법을 설명합니다.

### 도커 환경 구성
yum 리포지터리 설정을 통해 Docker CE를 설정합니다. 

#### 도커를 설치하기 위한 리포지토리 설정

```bash
# yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

### APR과 APR-util 설치
Apache 2.4를 소스 코드로부터 설치하려면 [APR(Apache Portable Runtime)](http://apr.apache.org/)이 설치돼 있어야 한다.
APR은 OS간의 차이를 해결해주는 라이브러리이다. 리눅스만해도 다양한 종류가 있어 OS에 따라 다르게 제공하고 있는 기능이 많이 있다. 따라서 Apache는 APR 라이브러를 사용함으로써 APR의 사용하고 다양한 환경에서도 동작하게 해주는 라이브러리이다. 이전에는 Apache에 포함되어 있었지만 현재는 분리해서 프로젝트가 관리되고 있다. 

[APR 다운로드 페이지](http://apr.apache.org/download.cgi)에서 tar.gz의 소스코드를 다운로드해 설치한다. 현재 APR의 최신 버전은 1.6.3 이다.

```bash
# cd /usr/local/src
