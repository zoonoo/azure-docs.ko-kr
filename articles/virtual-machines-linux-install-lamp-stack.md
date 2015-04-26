<properties 
	pageTitle="Linux 가상 컴퓨터에 LAMP 스택 설치" 
	description="Azure에서 Linux VM(가상 컴퓨터)에 LAMP 스택을 설치하는 방법에 대해 알아봅니다. Ubuntu 또는 CentOS에 설치할 수 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="szark"/>



#Azure에서 Linux 가상 컴퓨터에 LAMP 스택 설치

LAMP 스택은 다음과 같은 여러 요소로 구성됩니다.

- **L**inux - 운영 체제
- **A**pache - 웹 서버
- **M**ySQL - 데이터베이스 서버
- **P**HP - 프로그래밍 언어


##Ubuntu에 설치

다음 패키지를 설치해야 합니다.

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

`apt-get update`를 실행하여 로컬 패키지 목록을 업데이트한 후에는 단일 `apt-get install` 명령을 사용하여 이러한 패키지를 설치할 수 있습니다.

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

위의 명령을 실행하고 나면 이러한 패키지와 그 밖의 많은 종속성을 설치할 것인지를 묻는 메시지가 표시됩니다.   'y' and then 'Enter' 키를 눌러 계속 진행하고 화면에 표시되는 메시지에 따라 MySQL에 대한 관리 암호를 설정합니다.

이렇게 하면 PHP와 MySQL을 함께 사용하는 데 필요한 최소한의 PHP 확장이 설치됩니다. 다음 명령을 실행하여 패키지로 사용 가능한 다른 PHP 확장을 살펴봅니다.

	# apt-cache search php5


##CentOS 및 Oracle Linux에 설치

다음 패키지를 설치해야 합니다.

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

이러한 패키지는 단일 `yum install` 명령을 사용하여 설치할 수 있습니다.

	# sudo yum install httpd mysql mysql-server php php-mysql

위의 명령을 실행하고 나면 이러한 패키지와 그 밖의 많은 종속성을 설치할 것인지를 묻는 메시지가 표시됩니다.   'y' and then 'Enter' 키를 눌러 계속합니다.

이렇게 하면 PHP와 MySQL을 함께 사용하는 데 필요한 최소한의 PHP 확장이 설치됩니다. 다음 명령을 실행하여 패키지로 사용 가능한 다른 PHP 확장을 살펴봅니다.

	# yum search php


## SUSE Linux Enterprise Server에 설치

다음 패키지를 설치해야 합니다.

- apache2
- mysql
- apache2-mod_php53
- php53-mysql

이러한 패키지는 단일 `zypper install` 명령을 사용하여 설치할 수 있습니다.

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

위의 명령을 실행하고 나면 이러한 패키지와 그 밖의 많은 종속성을 설치할 것인지를 묻는 메시지가 표시됩니다.   'y' and then 'Enter' 키를 눌러 계속합니다.

이렇게 하면 PHP와 MySQL을 함께 사용하는 데 필요한 최소한의 PHP 확장이 설치됩니다. 다음 명령을 실행하여 패키지로 사용 가능한 다른 PHP 확장을 살펴봅니다.

	# zypper search php


설치
----------

1. **Apache** 설치

	- 다음 명령을 실행하여 Apache 웹 서버를 시작합니다.

		- Ubuntu 및 SLES: `sudo service apache2 restart`

		- CentOS 및 Oracle: `sudo service httpd restart`

	- Apache는 기본적으로 포트 80에서 수신 대기합니다. Apache 서버에 원격으로 액세스하려면 끝점을 열어야 할 수도 있습니다.  자세한 지침은 [끝점 구성](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/)을 참조하세요.

	- 이제 Apache가 실행 중이며 콘텐츠를 제공하고 있는지 확인할 수 있습니다. 브라우저를 `http://[MYSERVICE].cloudapp.net`으로 전환합니다. 여기서 **[MYSERVICE]**는 가상 컴퓨터가 상주하는 클라우드 서비스의 이름입니다. 일부 배포의 경우 "It works!"라고만 표시된 기본 웹 페이지가 나타날 수도 있습니다. 다른 배포의 경우 Apache 서버의 구성을 위한 추가 설명서 및 콘텐츠 링크가 포함된 좀 더 완전한 웹 페이지가 나타날 수 있습니다.

2. **MySQL** 설치

	- 이 단계는 Ubuntu에서는 수행할 필요가 없으며 mysql-server 패키지를 설치할 때 사용한 MySQL `root` 암호를 묻는 메시지가 표시됩니다.

	- 다른 배포의 경우 다음 명령을 실행하여 MySQL의 루트 암호를 설정합니다.

			# mysqladmin -u root -p password yourpassword

	- 그런 다음 `mysql` 또는 `mysqladmin` 유틸리티를 사용하여 MySQL을 관리할 수 있습니다.


##추가 참고 자료

Ubuntu에 LAMP 스택을 설치하는 데 도움이 되는 여러 리소스가 있습니다.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

<!--HONumber=45--> 
