
## [CentOS/RedHat) Apache HTTP Server Compile & Install

### 사전 준비
Apache 2.4를 소스 코드를 이용해 설치하려면 **gcc**, **make**, **pcre**, **pcre-devel** 라이브러리가 필요하다.
소스 코드 다운로드에 사용할 **wget** 명령어도 설치한다.

```bash
# yum install gcc make pcre pcre-devel wget
```

### APR과 APR-util 설치
Apache 2.4를 소스 코드로부터 설치하려면 [APR(Apache Portable Runtime)](http://apr.apache.org/)이 설치돼 있어야 한다.
APR은 OS간의 차이를 해결해주는 라이브러리이다. 리눅스만해도 다양한 종류가 있어 OS에 따라 다르게 제공하고 있는 기능이 많이 있다. 따라서 Apache는 APR 라이브러를 사용함으로써 APR의 사용하고 다양한 환경에서도 동작하게 해주는 라이브러리이다. 이전에는 Apache에 포함되어 있었지만 현재는 분리해서 프로젝트가 관리되고 있다. 

[APR 다운로드 페이지](http://apr.apache.org/download.cgi)에서 tar.gz의 소스코드를 다운로드해 설치한다. 현재 APR의 최신 버전은 1.6.3 이다.

```bash
# cd /usr/local/src
# wget http://apache.mirror.cdnetworks.com//apr/apr-1.6.3.tar.gz
# tar -xvzf apr-1.6.3.tar.gz
# cd apr-1.6.3
# ./configure --prefix=/opt/apr/apr-1.6.3
# make
# make test
# make install
```
이것으로 APR 설치를 마쳤습니다. APR은 **/opt/apr/apr-1.6.3**에 설치됐습니다.

다음으로 APR-util을 설치합니다. [APR 다운로드 페이지](http://apr.apache.org/download.cgi)에서 tar.gz 소스 코드 바이너리를 내려받아 설치합니다. 현재 시점의 최신 버전은 1.6.1이므로 여기서는 1.6.1 기준으로 설명합니다. 

```bash
# cd /usr/local/src
# wget http://apache.mirror.cdnetworks.com//apr/apr-util-1.6.1.tar.gz
# tar -xvzf apr-util-1.6.1.tar.gz
# cd apr-util-1.6.1
# ./configure --prefix=/opt/apr-util/apr-util-1.6.3 --with-apr=/opt/apr/apr-1.6.3
# make
# make test
# make install
```

이것으로 APR-util도 설치가 완료됐습니다. APR-util은 **/opt/apr-util/apr-util-1.6.1**에 설치됏습니다.

### Apache 2.4 설치하기 



