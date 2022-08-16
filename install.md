# yum 을 이용해서  epel, Apache2.4, Resin4, MySQL5.7, PHP5.4, OpenJDK8 설치하기

## 1. 업데이트

	yum -y update

## 2. epel 설치
	yum list epel-release

	sudo yum -y install epel-release.noarch
	sudo yum -y install httpd

	실행 및 포트설정

	sudo systemctl start httpd
	sudo systemctl status httpd


## 3. jdk 설치

	yum list | grep jdk

	sudo yum -y install java-1.8.0-openjdk.x86_64

	sudo yum -y install java-1.8.0-openjdk-devel.x86_64


	환경변수 설정(재확인)

	which java

	readlink -f /usr/bin/java

	/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.332.b09-1.amzn2.0.2.x86_64/jre/bin/java
	

## 4. MySql 설치

	sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

	sudo yum localinstall -y https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

	sudo yum install -y mysql-community-server

	sudo systemctl enable mysqld
	sudo systemctl start mysqld

	mysql --version
	sudo netstat -plntu 
	sudo systemctl restart mysqld



## 5. resin 설치

caucho.com 에서 rpm 파일을 다운받은 뒤 설치한다.

	yum install resin-4.0.63-1.x86_64.rpm
pro 버전의 경우 아래와 같이 설치할 수 있다.

	rpm --import http://caucho.com/download/rpm/RPM-GPG-KEY-caucho
	yum install http://caucho.com/download/rpm/4.0.30/x86_64/resin-pro-4.0.30-1.x86_64.rpm


## 6. PHP 설치

	yum list php
	sudo yum -y install php.x86_64


### * 확인

	yum repolist //현재 등록된 repolist 확인
	yum list installed //설치된 전체 패키지

