---
layout: post
current: post
cover:  assets/images/covers/logstash-cover.png
navigation: True
title: Installing Logstash
date: 2018-03-30 15:27 +0900
tags: [Logstash, Getting started]
class: post-template
subclass: 'post tag-getting-started'
author: youngsup
---
# Logstash 설치하기

## Installing Oracle JAVA 1.8 JDK

우선 Logstash 설치에 앞서, Logstash 실행에 필요한 자바를 설치합니다.

`java -version` 명령어를 통해 현재 자바 버전을 확인하고, 만약 1.7 버전이 설치되어 있다면 다음 명령을 통해 삭제합니다:

```shell
$ sudo yum remove java-1.7.0-openjdk.x86_64 -y
```

그 뒤, 1.8 버전의 자바를 설치합니다:

```shell
$ sudo yum install java-1.8.0-openjdk-devel.x86_64 -y
```

설치 완료 후 자바 버전을 확인하면, 다음과 유사하게 출력되야 합니다:

```shell
$ java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
```

## Installing Logstash from Package Repositories

### APT

Public Signing Key를 다운로드 받아 설치합니다:

```shell
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

Debian의 경우 뒤의 과정을 하기 전에, apt-transport-https 패키지의 설치가 필요할 수 있습니다:

```shell
$ sudo apt-get install apt-transport-https
```

Elastic 저장소 정보를 '/etc/apt/sources.list.d/elastic-6.x.list'에 추가합니다:

```shell
$ echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
```

apt-get을 사용하여 Logstash를 설치합니다:

```shell
$ sudo apt-get update && sudo apt-get install logstash
```

Logstash 실행에 관한 내용은 [Runing Logstash](https://www.elastic.co/guide/en/logstash/6.2/running-logstash.html)를 참고하세요.

### YUM

Public Signing Key를 다운로드 받아 설치합니다:

```shell
$ sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

다음 내용을 /etc/yum.repos.d/ 디렉토리 내에 .repo 확장자를 가지는 파일(예: logstash.repo)에 저장합니다:

```text
[logstash-6.x]
name=Elastic repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

yum을 사용하여 Logstash를 설치합니다:

```shell
$ sudo yum install logstash
```

Logstash 실행에 관한 내용은 [Runing Logstash](https://www.elastic.co/guide/en/logstash/6.2/running-logstash.html)를 참고하세요.

## 참고

* [Installing Logstash](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)
