---
layout: post
current: post
cover:  assets/images/covers/redis-cover.jpg
navigation: True
title: Installing Redis 4.0.9 on Ubuntu 16.04
date: 2018-03-27 15:29 +0900
tags: [Redis, Getting started]
class: post-template
subclass: 'post tag-getting-started'
author: youngsup
---
# Ubuntu 16.04에 Redis 4.0.9 설치하기

## Prerequisites

Redis 소스를 컴파일하고 설치하기 위해 필요한 패키지들을 설치합니다.

```shell
$ sudo apt-get update -y
$ sudo apt-get install gcc make -y
```

## Installing Redis

Redis 공식 사이트에서는 소스를 컴파일하여 설치하는 방법을 추천하고 있습니다.
이는, Linux 배포판의 패키지 관리자를 사용하여 설치할 경우 일반적으로 사용 가능한 버전이 최신이 아닐 수 있기 때문입니다.
Redis는 GCC 컴파일러와 libc 외에 다른 의존성이 없기 때문에 컴파일하여 설치하기 수월합니다.

Redis를 컴파일하여 설치하기 위해 다음과 같이 작업합니다 (글 작성시의 Redis 버전은 `4.0.9`입니다):

```shell
$ wget http://download.redis.io/redis-stable.tar.gz
$ tar xvzf redis-stable.tar.gz
$ cd redis-stable
$ make
```

컴파일이 정상적으로 완료되면 *src* 디렉토리 내에 다음과 같은 실행가능한 바이너리들이 있는것을 확인할 수 있습니다:

* **redis-server**: Redis 서버입니다. 서버를 구동할 때 사용합니다.
* **redis-sentinel**: Redis 서버의 상태를 모니터링하고 장애 발생 시 복구하는 감시 프로그램입니다.
* **redis-cli**: Redis를 사용하기 위한 Command Line Interface(CLI) 유틸입니다.
* **redis-benchmark**: Redis의 성능을 테스트하기 위해 사용하는 프로그램입니다.
* **redis-check-aof** and **redis-check-dump**: 가끔 발생하는 데이터 손상시 유용합니다.

다음 명령어를 통해 위의 바이너리 파일들을 설치(복사) 합니다:

```shell
$ sudo make install
```

설치되는 디렉토리는 기본값이 `usr/local/bin`이며 PREFIX 값을 통해 원하는 곳에 설치 가능합니다:

```shell
$ make PREFIX=/some/other/directory install
```

## Starting Redis

`redis-server` 바이너리를 실행하여 서버가 정상적으로 동작하는지 확인합니다:

```shell
$ redis-server
9308:C 28 Mar 05:09:25.302 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
9308:C 28 Mar 05:09:25.303 # Redis version=4.0.9, bits=64, commit=00000000, modified=0, pid=9308, just started
9308:C 28 Mar 05:09:25.303 # Configuration loaded
9309:M 28 Mar 05:09:25.305 * Increased maximum number of open files to 10032 (it was originally set to 1024).
... more logs ...
```

`redis-server` 실행 시 아무 옵션을 주지 않으면 모든 설정값이 기본값을 사용하며, `redis-server /etc/redis.conf`와 같이 설정 파일의 전체 경로를 첫번째 인자로 실행하면 설정 파일 내의 값들을 사용합니다.
Redis 소스 코드 내에 redis.conf 파일을 복사하여 서버에 맞게 설정하고 사용할 수 있습니다.

## Check if Redis is working

외부 프로그램들은 TCP 소켓과 Redis 프로토콜을 사용하여 Redis 서버와 통신할 수 있습니다.
Redis 프로토콜은 각 프로그래밍 언어에 맞게 라이브러리로 제공되며, 간단히 `redis-cli`를 사용하여 Redis 서버로 명령을 보낼 수 있습니다.

`redis-cli`를 사용하여 현재 서버가 동작하고, 연결 가능한지 확인합니다:

```shell
$ redis-cli ping
PONG
```

명령어 실행후 **PONG** 메시지를 받으면 서버가 정상적으로 동작하고 있는겁니다.

`redis-cli`는 실행 인자가 없을 시, Redis 호스트는 localhost를, 포트는 6379를 기본값으로 사용합니다.
이는 `-h <hostname>`, `-p <port>` 인자를 통해 설정할 수 있으며 더 자세한 옵션은 `redis-cli --help`를 통해 확인해볼 수 있습니다.

## Installing Redis more properly

Redis를 실행하기 위해서 `redis-server`를 직접 실행할 수 있지만, 시스템 부팅시 매번 직접 실행 해줘야하는 번거로움이 있습니다.
보다 나은 방법으로, init script를 통해 매 시스템 부팅시 자동으로 실행되게 할 수 있습니다.

* 우선 Redis 설정과 데이터를 저장하기 위한 디렉토리를 생성합니다:

```shell
$ sudo mkdir /etc/redis
$ sudo mkdir /var/redis
```

* 다운받은 Redis 소스의 **utils** 디렉토리에 있는 init script를 /etc/init.d 디렉토리에 복사합니다. 파일 이름은 사용하는 포트 번호를 포함하는 것이 좋습니다:

```shell
$ sudo cp utils/redis_init_script /etc/init.d/redis_6379
```

* 필요한 경우 init script 내의 항목을 수정합니다:

```shell
$ sudo vi /etc/init.d/redis_6379
```

* 다음으로, 다운받은 Redis 소스에서 설정 템플릿 파일을 /etc/redis/ 디렉토리에 포트번호를 이름으로 지정하여 복사합니다:

```shell
$ sudo cp redis.conf /etc/redis/6379.conf
```

* Redis 인스턴스가 사용하는 디렉토리를 생성합니다:

```shell
$ sudo mkdir /var/redis/6379
```

* 설정 파일(**/etc/redis/6379.conf**)에서 다음 항목들을 수정합니다:
  * **bind** 0.0.0.0 # 서버 외부에서 접근하려는 경우
  * **daemonize** yes
  * **pidfile** /var/run/redis_6379.pid
  * **port** 6379 (원하는 포트로 변경)
  * **logfile** /var/log/redis_6379.log
  * **dir** /var/redis/6379

* 마지막으로, Redis init script를 등록합니다:

```shell
$ sudo update-rc.d redis_6379 defaults
```

* 이제 다음 명령을 통해 Redis 서버를 실행 가능하며, 재부팅시에도 자동으로 Redis 인스턴스가 실행됩니다:

```shell
$ sudo /etc/init.d/redis_6379 start
```

## Performance tips for Redis Cache Server

Redis 서버의 성능을 높이기 위해서는, 설치 후 몇가지 설정을 해주어야합니다.
Redis를 설치하고 바로 실행하면 3개의 경고(WARNING) 메시지가 출력되며 설정을 변경할 것을 제안합니다.

### Raise somaxconn above 511

> \# WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.

`somaxconn`은 'Socket Max Connection'의 약어로 최대 연결 개수를 뜻합니다.
리눅스의 경우 메모리가 128MB 보다 작을 경우 128이 기본이고, 클 경우 1024가 기본입니다.

동시에 너무 많은 요청이 올 경우, 예를 들어, somaxconn 값이 128일 경우 그 이상 요청이 오는 것에 대해서는 연결을 거부하게 됩니다.

해당 값을 변경하기 위해서, 다음과 같이 수행할 수 있습니다 (설정은 재부팅 후 적용됩니다):

```shell
$ sudo sed -i -e '$i \sysctl -w net.core.somaxconn=511\n' /etc/rc.local
```

**CentOS**

```shell
$ sudo sed -i -e '$i \net.core.somaxconn = 511\n' /etc/sysctl.conf
```

---

**NOTE**

**docker-compose**를 사용하는 경우, `docker-compose.yml`의 Redis 설정에 다음 내용을 추가하여 반영 가능합니다:

```yaml
redis:
  image: redis
  sysctls:
    net.core.somaxconn: 511
```

**docker run**을 통해 Redis 서버를 실행하는 경우, 실행시 `--sysctl net.core.somaxconn=511` 인자를 추가하여 실행합니다:

```shell
$ docker run --name some-redis --sysctl net.core.somaxconn=511 -d redis
```

---

### vm.overcommit_memory = 1

> \# WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.

해당 경고에 대해서는 다음과 같이 수행하여 설정값을 변경해줍니다:

```shell
$ sudo sed -i '$a\ \n# ensure redis background save issue\nvm.overcommit_memory = 1\n\nsystctl vm.overcommit_memory=1' /etc/sysctl.conf
```

### Disable THP

> \# WARNING you have Transparent Huge Pages (THP) support enabled in your kernel.
> This will create latency and memory usage issues with Redis.
> To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot.
> Redis must be restarted after THP is disabled.

Transparent Hug Pages (THP)는 대량의 메모리를 관리하기 위한 한 방법으로, 페이지 크기를 확대(2MB or 1GB)하여 사용하는 방법입니다.
THP는 성능 개선을 목적으로하며, 대부분의 시스템 설정의 성능을 향상시키지만 DB와 같은 특정 상황에 대해서는 오히려 성능이 악화될 수 있습니다.

Redis는 실행 시 THP가 enabled 되어 있는 경우 경고 메시지를 출력하며, 해당 기능을 끄도록 제안하고 있습니다.

해당 기능은 아래와 같은 명령을 수행하는 것으로 끌 수 있습니다:

```shell
$ echo never | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
$ echo never | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
```

시스템 시작시 마다 해당 명령이 수행되도록 하기 위해 **/etc/rc.local** 파일에 명령을 추가해 놓을 수 있습니다:

```shell
$ sudo sed -i -e '$i \echo never > /sys/kernel/mm/transparent_hugepage/enabled' /etc/rc.local
$ sudo sed -i -e '$i \echo never > /sys/kernel/mm/transparent_hugepage/defrag\n' /etc/rc.local
```

**CentOS**

```bash
$ sudo sed -i -e '$i \echo never > /sys/kernel/mm/transparent_hugepage/enabled' /etc/rc.d/rc.local
$ sudo sed -i -e '$i \echo never > /sys/kernel/mm/transparent_hugepage/defrag\n' /etc/rc.d/rc.local
```

---

**NOTE**

Docker를 사용하는 경우 **/etc/rc.local** 파일이 `Read-only`로 설정되어 있어서 설정을 변경할 수 없습니다.
하지만 설정 확인(`cat /sys/kernel/mm/transparent_hugepage/enabled`) 시 `madvise`로 되어 있고, 메모리 사용 확인(`cat /proc/meminfo`)시 HugePages를 사용하고 있지 않은 것 같습니다.

---

## 참고

* [Redis Quick Start](https://redis.io/topics/quickstart)
* [Performance tips for Redis Cache Server](https://www.techandme.se/performance-tips-for-redis-cache-server/)
* [THP(Transparent Huge Pages) 기능과 설정 방법](http://allthatlinux.com/dokuwiki/doku.php?id=thp_transparent_huge_pages_%EA%B8%B0%EB%8A%A5%EA%B3%BC_%EC%84%A4%EC%A0%95_%EB%B0%A9%EB%B2%95)
* [How to install Redis 3.2 on CentOS 6 and 7](https://www.hugeserver.com/kb/install-redis-centos/)
