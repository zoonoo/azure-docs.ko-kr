<properties
	pageTitle="Azure를 사용하여 LAMP 스택 만들기 | Microsoft Azure"
	description="Linux를 실행하는 Azure VM(가상 컴퓨터)에서 Microsoft Azure를 사용하여 LAMP 스택을 만드는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Microsoft Azure를 사용하여 LAMP 스택을 만드는 방법

"LAMP" 스택은 일반적으로 함께 설치되어 서버가 동적 웹 사이트와 웹 응용 프로그램을 호스트할 수 있게 해주는 오픈 소스 소프트웨어 그룹입니다. 이 용어는 실제로 Apache 웹 서버가 있는 Linux 운영 체제를 나타내는 약어입니다. 사이트 데이터는 MySQL 데이터베이스에 저장되고 동적 콘텐츠는 PHP에 의해 처리됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


이 가이드에서는 Linux 이미지에 설치된 LAMP 스택을 가져와서 Microsoft Azure에 배포합니다.

다음 내용을 배웁니다.

-	Azure 가상 컴퓨터를 만드는 방법
-	LAMP 스택에 대해 가상 컴퓨터를 준비하는 방법
-	LAMP 서버에 필요한 소프트웨어를 가상 컴퓨터에 설치하는 방법

독자에게 이미 Azure 구독이 있다고 가정합니다. 그렇지 않으면 [http://azure.microsoft.com](http://azure.microsoft.com)에서 무료 평가판을 등록할 수 있습니다. MSDN 구독이 있는 경우에는 [Microsoft Azure 특별 가격: MSDN, MPN 및 Bizspark 이점](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)을 참조하세요. Azure에 대한 자세한 내용은 [Azure가 무엇입니까?](http://azure.microsoft.com/overview/what-is-azure/)를 참조하세요.

가상 컴퓨터가 이미 있는 경우 다른 Linux 배포에 LAMP 스택을 설치하는 방법에 대한 기본 사항을 확인하려면 이 항목 외에 [Azure에서 Linux 가상 컴퓨터에 LAMP 스택 설치를 참조하세요.](virtual-machines-linux-install-lamp-stack.md).

Azure 마켓플레이스에서 미리 구성된 LAMP 이미지를 배포할 수도 있습니다. 다음 10분 비디오에서는 Azure 마켓플레이스에서 미리 빌드된 LAMP 이미지를 배포하는 방법을 소개합니다. [Azure VM의 LAMP 스택](https://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman).

##1단계: 이미지 만들기
이 단계에서는 Linux 이미지를 사용하여 Azure에 가상 컴퓨터를 만듭니다.

###1단계: SSH 인증 키 생성
SSH는 시스템 관리자에게 중요한 도구입니다. 그러나 사용자가 결정하는 암호를 통해 보안을 구현하는 것이 항상 적절한 것은 아닙니다. 강력한 SSH 키를 사용하면 암호에 대해 염려하지 않고 원격 액세스를 허용할 수 있습니다. 이 방법은 비대칭 암호화를 사용한 인증으로 구성됩니다. 사용자의 개인 키가 인증을 부여합니다. 사용자 계정을 잠가서 암호 인증을 완전히 허용하지 않을 수도 있습니다.

SSH 인증 키를 생성하려면 다음 단계를 수행합니다.

-	다음 위치에서 puttygen을 다운로드하여 설치합니다. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
-	puttygen.exe를 실행합니다.
-	**생성**을 클릭하여 키를 생성합니다. 프로세스에서 마우스를 창의 빈 영역 위로 이동하여 임의성을 늘릴 수 있습니다. ![][1]
-	생성 프로세스 후 puttygen.exe에서 생성된 키를 표시합니다. 예: ![][2]
-	**키**에서 공개 키를 선택하여 복사하고 **publicKey.pem** 파일에 저장합니다. 저장된 공개 키의 파일 형식은 원하는 공개 키와 다르므로 **공개 키 저장**을 클릭하지 마세요.
-	**개인 키 저장**을 클릭하고 **privateKey.ppk** 파일에 저장합니다.

###2단계: Azure 포털에서 이미지 만들기
[Azure 포털](https://portal.azure.com/)의 작업 표시줄에서 **새로 만들기**를 클릭한 후 다음 지침을 수행하고 요구에 따라 Linux 이미지를 선택하여 이미지를 만듭니다. 이 예제에서는 Ubuntu 14.04 이미지를 사용합니다.

![][3]

**호스트 이름**에서 인터넷 클라이언트가 이 가상 컴퓨터에 액세스하는 데 사용할 URL의 이름을 지정합니다. DNS 이름의 마지막 부분(예: LAMPDemo)을 정의하면 Azure에서 URL을 *lampdemo.cloudapp.net*으로 생성합니다.

**사용자 이름**에서 나중에 가상 컴퓨터에 로그인하는 데 사용할 이름을 선택합니다.

**SSH 인증 키**에서 puttygen에 의해 생성된 공개 키가 포함된 **publicKey.pem** 파일의 키 값을 복사합니다.

![][4]

필요에 따라 다른 설정을 구성하고 **만들기**를 클릭합니다.

##2 단계:LAMP 스택에 대해 가상 컴퓨터 준비
이 단계에서는 웹 트래픽에 대해 끝점을 구성한 다음 새 가상 컴퓨터에 연결합니다.

###1단계: 웹 액세스를 허용하도록 HTTP 포트 열기
Azure의 끝점은 프로토콜(TCP 또는 UDP)과 공용 및 개인 포트로 구성됩니다. 개인 포트는 가상 컴퓨터에서 서비스가 수신 대기하는 포트입니다. 공용 포트는 Azure 클라우드 서비스가 외부에서 인터넷 기반 트래픽을 수신 대기하는 포트입니다. 포트 번호가 동일한 경우도 있습니다.

Apache가 수신 대기하는 기본 포트 번호는 TCP 포트 80입니다. Azure 끝점에서 이 포트를 열면 사용자 및 다른 인터넷 클라이언트가 Apache 웹 서버에 액세스할 수 있습니다.

Azure 포털에서 **찾아보기 -> 가상 컴퓨터**를 클릭한 다음 직접 만든 가상 컴퓨터를 클릭합니다.

![][5]

가상 컴퓨터에 끝점을 추가하려면 **끝점** 확인란을 클릭합니다.

![][6]

**추가**를 클릭합니다. 새 가상 컴퓨터를 프로비전하는 경우 필요에 따라 끝점을 사용하거나 사용하지 않도록 설정할 수 있습니다.

끝점을 구성합니다.

1.	**끝점**에 끝점의 이름을 입력합니다.
2.	**공용 포트**에 80을 입력합니다. Apache의 기본 수신 대기 포트를 변경한 경우 개인 포트를 Apache 수신 대기 포트와 같도록 업데이트해야 합니다.
3.	**공용 포트**에 80을 입력합니다. 기본적으로 HTTP 트래픽은 포트 80을 사용합니다. 80으로 설정하는 경우 Apache 웹 서비스에 액세스할 수 있게 해주는 URL에 포트 번호를 포함할 필요가 없습니다. 예: http://lampdemo.cloudapp.net Apache 수신 대기 포트를 다른 값(예: 81)으로 설정하는 경우 Apache 웹 서비스에 액세스하기 위한 URL에 포트 번호를 추가해야 합니다. 예를 들면 http://lampdemo.cloudapp.net:81/과 같습니다.

![][7]

**확인**을 클릭하여 가상 컴퓨터에 끝점을 추가합니다.




###2단계: 만든 이미지에 연결
새 가상 컴퓨터에 연결하기 위한 SSH 도구를 선택할 수 있습니다. 이 예제에서는 Putty를 사용합니다.

먼저 Azure 포털에서 가상 컴퓨터의 DNS 이름을 가져옵니다. **찾아보기 -> 가상 컴퓨터 ->** 가상 컴퓨터의 이름 **-> 속성**을 클릭한 다음 **속성** 타일의 **도메인 이름** 필드를 확인합니다.

**SSH** 필드에서 SSH 연결의 포트 번호를 가져옵니다. 다음은 예제입니다.

![][8]

[여기](http://www.putty.org/)에서 Putty를 다운로드합니다.

다운로드한 후 실행 파일 PUTTY.EXE를 클릭합니다. 가상 컴퓨터의 속성에서 가져온 호스트 이름과 포트 번호를 사용하여 기본 옵션을 구성합니다. 다음은 예제입니다.

![][9]

왼쪽 창에서 **연결 -> SSH -> 인증**을 클릭한 다음 **찾아보기**를 클릭하여 1단계: 이미지 만들기에서 puttygen에 의해 생성된 개인 키가 포함된 **privateKey.ppk** 파일의 위치를 지정합니다. 다음은 예제입니다.

![][10]

**열기**를 클릭합니다. 메시지 상자에 경고 메시지가 표시될 수도 있습니다. DNS 이름과 포트 번호를 올바르게 구성한 경우 **예**를 클릭합니다.

![][11]


다음이 표시되어야 합니다.

![][12]

1단계: 이미지 만들기에서 가상 컴퓨터를 만들 때 지정한 사용자 이름을 입력합니다. 다음과 유사한 출력이 표시됩니다.

![][13]

##3 단계: LAMP 스택 설치

가상 컴퓨터를 만들 때 사용한 Linux 배포에 따라 여러 가지 방법으로 LAMP 스택을 설치할 수 있습니다. 다음 섹션에는 많이 사용되는 몇 가지 Linux OS에 대한 일반적인 단계가 포함되어 있습니다.

###Red Hat, CentOS 기준

####Apache 설치
Apache를 설치하려면 터미널을 열고 다음 명령을 실행합니다.

	sudo yum install httpd

설치가 완료되면 다음 명령을 사용하여 Apache를 시작합니다.

	sudo service httpd start

####Apache 테스트
Apache가 성공적으로 설치되었는지 확인하려면 Apache 서버의 DNS 이름을 찾습니다(이 문서의 예제 URL의 경우 http://lampdemo.cloudapp.net/)). 페이지에 "It works!"라는 단어가 표시되어야 합니다. ![][14]

####문제 해결
Apache가 실행되고 있지만 위의 Apache 기본 페이지가 표시되지 않는 경우 다음을 확인해야 합니다.

-	Apache 웹 서비스 수신 대기 주소/포트
	-	Azure 가상 컴퓨터에 대한 끝점 설정을 확인합니다. 끝점의 구성이 적절한지 확인합니다. 1 단계를 참조하세요:이 문서에서 이미지 지침을 작성합니다.
	-	/etc/httpd/conf/httpd.conf를 열고 문자열 "Listen"을 검색합니다. Apache 수신 대기 포트가 끝점에 대해 구성한 개인 포트와 동일한지 확인합니다. Apache의 기본 포트는 80입니다. 다음은 예제입니다.  

			……
			......
				# prevent Apache from glomming onto all bound IP addresses (0.0.0.0)
				#
				#Listen 12.34.56.78:80
				Listen 80
				#
				# Dynamic Shared Object (DSO) Support
			……
			……  

-	방화벽, iptables 구성 로컬 호스트에서 Apache 기본 페이지를 볼 수 있는 경우 Apache가 수신 대기 중인 포트가 방화벽에서 차단되었을 수 있습니다. w3m 도구를 사용하여 Apache 웹 페이지를 찾을 수 있습니다. 다음 명령은 w3m을 설치하고 Apache 기본 페이지로 이동합니다.

		sudo yum  install w3m w3m-img  
		w3m http://localhost

	방화벽 또는 iptables로 인해 문제가 발생한 경우 /etc/sysconfig/iptables에 다음 줄을 추가합니다.

		-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
		-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

	두 번째 줄은 https 트래픽에만 필요합니다.

	이러한 줄은 전체적으로 액세스를 제한하는 다음과 같은 줄 위에 있어야 합니다.

		-A INPUT -j REJECT --reject-with icmp-host-prohibited  

	새 설정을 적용하려면 다음 명령을 사용합니다.

		service iptables restart

####MySQL 설치
MySQL을 설치하려면 터미널을 열고 다음 명령을 실행합니다.

	sudo yum install mysql-server
	sudo service mysqld start

설치 중에 MySQL에서 사용자 승인을 두 번 묻습니다. 두 쿼리에 대해 모두 예를 선택하면 MySQL이 설치됩니다.

####MySQL 구성
설치가 완료된 후 다음을 사용하여 루트 MySQL 암호를 설정할 수 있습니다.

	sudo /usr/bin/mysql_secure_installation  

현재 루트 암호를 묻는 메시지가 표시됩니다.

방금 MySQL을 설치하여 암호가 없을 가능성이 크므로 Enter 키를 눌러 비워 둡니다.

	Enter current password for root (enter for none):
	OK, successfully used password, moving on...  

루트 암호를 설정하라는 메시지가 표시됩니다. Y를 선택하고 지침을 따릅니다.

CentOS에서는 일련의 yes 또는 no 질문을 통해 MySQL 설정 프로세스를 자동화합니다. 해당 질문은 아래에 나와 있습니다. 구성에 대해 Y 또는 N을 선택합니다. 완료되면 MySQL 다시 로드되고 새 변경 내용을 구현합니다.

	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.

	Remove anonymous users? [Y/n] y
	 ... Success!

	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.

	Disallow root login remotely? [Y/n] y
	... Success!

	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.

	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.

	Reload privilege tables now? [Y/n] y
	 ... Success!

	Cleaning up...

	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.

	Thanks for using MySQL!  

####PHP 설치
PHP는 동적 웹 페이지를 빌드하는 데 널리 사용되는 오픈 소스 웹 스크립팅 언어입니다.

가상 컴퓨터에 PHP를 설치하려면 터미널을 열고 다음 명령을 실행합니다.

	sudo yum install php php-mysql  

"y"를 선택하여 소프트웨어 패키지를 다운로드합니다. 그런 다음 GPG 키 0xE8562897 "CentOS-5 키(CentOS 5 공식 서명 키)" 가져오기에 대해 "y"를 선택합니다. PHP가 설치됩니다.

	warning: rpmts_HdrFromFdno: Header V3 DSA signature: NOKEY, key ID e8562897
	updates/gpgkey                                                                                                                                                                       | 1.5 kB     00:00
	Importing GPG key 0xE8562897 "CentOS-5 Key (CentOS 5 Official Signing Key) <centos-5-key@centos.org>" from /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
	Is this ok [y/N]: y  

###Debian, Ubuntu 기준
Ubuntu 14.04에서 테스트되었습니다.

Ubuntu는 Debian을 기반으로 합니다. Red Hat 시리즈와 동일한 방식으로 LAMP 스택을 설치할 수 있습니다. 단계를 간소화하려면 Tasksel 도구를 사용합니다.

Tasksel은 관련된 여러 패키지를 하나의 조정된 작업으로 시스템에 설치하는 Debian/Ubuntu 도구입니다. 자세한 내용은 [Tasksel - 커뮤니티 도움말 위키](https://help.ubuntu.com/community/Tasksel)를 참조하세요.

tasksel을 사용하여 LAMP 스택에 필요한 소프트웨어를 설치합니다.

- 리포지토리에서 패키지 목록을 다운로드한 후 업데이트하여 최신 버전의 패키지 및 해당 종속성에 대한 정보를 가져옵니다.  

		sudo apt-get update
-	Tasksel을 사용하여 Ubuntu LAMP 스택을 설치하려면 다음을 수행합니다.  

		sudo apt-get install tasksel
		sudo tasksel install lamp-server

마법사를 계속 진행하고 **MySQL 루트 암호**를 선택합니다.

![][15]


##서버에서 LAMP 테스트
빠른 PHP 정보 페이지를 만들어 LAMP 시스템을 테스트할 수 있습니다.

먼저 새 파일을 만듭니다.

	sudo nano /var/www/html/info.php  

다음 줄을 추가합니다.

	<?php
	phpinfo();
	?>  

저장한 후 종료합니다.

Apache를 다시 시작하여 변경 내용을 컴퓨터에 모두 적용합니다. 가상 컴퓨터의 OS가 CentOS인 경우 다음 명령을 사용하여 Apache를 다시 시작합니다.

	sudo service httpd restart

가상 컴퓨터의 OS가 Ubuntu인 경우 다음 명령을 사용하여 Apache를 다시 시작합니다.

	sudo service apache2 restart  

마지막으로 PHP 정보 페이지로 이동합니다(이 항목의 예제 웹 서버에서 URL은 http://lampdemo.cloudapp.net/info.php)).

브라우저가 다음과 유사하게 표시되어야 합니다.

![][16]

##추가 단계

일반적으로 웹 응용 프로그램 배포를 준비하기 위해 일부 기본 설정을 변경합니다.

###MySQL에 대한 원격 액세스 허용
MySQL과 함께 설치된 VM이 두 개 이상 있고 데이터를 교환해야 하는 경우 MySQL 원격 액세스를 사용하도록 설정하고 적절한 권한을 부여해야 합니다.

**명령 참조 형식:**

	grant [authority] on [databaseName].[tableName] to [username]@[login host] identified by "[passwd]"  

**예제:**

	grant select,insert,update,delete on studentManage.student to user1@"%" Identified by "abc";

또한 /etc/mysql/my.cnf 프로필을 변경해야 합니다. 다음과 유사한 줄이 있는 경우

	skip-networking
	bind-address = 127.0.0.1  

주석으로 처리하고(줄의 시작 부분에 # 추가) MySQL을 다시 시작해야 합니다.

원격 액세스를 허용하는 끝점을 추가하려면, -1 단계: 새로운 끝점을 만들기 위해 이미지 만들기-를 참조합니다. MySQL의 기본 원격 액세스 TCP 포트 번호는 3306입니다. 다음은 예제입니다.

![][17]

###Apache 서버에 웹 응용 프로그램 배포
LAMP 스택이 성공적으로 설정되면 Apache 웹 서버(가상 컴퓨터)에 기존 웹 응용 프로그램을 배포할 수 있습니다. 실제 웹 서버에 기존 웹 응용 프로그램을 배포하는 것과 동일한 단계입니다.

-	웹 서버의 루트는 **/var/www/html**에 있습니다. 이 폴더에 파일을 업로드해야 하는 사용자에게 권한을 부여해야 합니다. 다음 예제에서는 lampappgroup이라는 그룹에 쓰기 권한을 추가하는 방법을 보여 주며 azureuser 사용자 이름을 이 그룹에 넣습니다.  

		sudo groupadd lampappgroup                      # Create a group
		sudo gpasswd -a azureuser lampappgroup    # Add azureuser to lampappgroup
		sudo chgrp lampappgroup /var/www/html/  # Change the ownership to group lampappgroup
		sudo chmod g+w /var/www/html/                 # grant write permission to group lampappgroup

	>[AZURE.NOTE]/var/www/html/에서 파일을 수정하려는 경우 다시 로그인해야 할 수도 있습니다.
-	SFTP 클라이언트(예: FileZilla)를 사용하여 가상 컴퓨터의 DNS 이름(예: lampdemo.cloudapp.net)에 연결한 다음 /**var/www/html**로 이동하여 사이트를 게시합니다. ![][18]



##일반적인 문제 및 문제 해결

###인터넷에서 Apache 및 Moodle이 있는 가상 컴퓨터에 액세스할 수 없음

-	**증상** Apache가 실행되고 있지만 브라우저에서 Apache 기본 페이지를 볼 수 없습니다.
-	**가능한 근본 원인**
	1.	Apache 수신 대기 포트가 웹 트래픽에 대한 가상 컴퓨터 끝점의 개인 포트와 다릅니다.</br> 공용 포트 및 개인 포트 끝점 설정을 검사하고 개인 포트가 Apache 수신 대기 포트와 같은지 확인합니다. 가상 컴퓨터에 대한 끝점 구성 지침은 1단계: 이미지 만들기를 참조하세요.</br> Apache의 수신 대기 포트를 확인하려면 /etc/httpd/conf/httpd.conf(Red Hat 릴리스) 또는 /etc/apache2/ports.conf(Debian 릴리스)를 열고 "Listen" 문자열을 검색합니다. 기본 포트는 80입니다.

	2.	방화벽에서 Apache 수신 대기 포트를 사용하지 않도록 설정했습니다.</br> 로컬 호스트에서 Apache 기본 페이지를 볼 수 있는 경우 Apache가 수신 대기하는 포트가 방화벽에서 차단되었을 가능성이 큽니다. w3m 도구를 사용하여 웹 페이지를 찾을 수 있습니다. 다음 명령은 w3m을 설치하고 Apache 기본 페이지로 이동합니다.

			sudo yum  install w3m w3m-img
			w3m http://localhost

-	**솔루션**

	1.	Apache 수신 대기 포트가 가상 컴퓨터의 웹 트래픽에 대한 끝점 개인 포트와 다른 경우 끝점의 개인 포트를 Apache 수신 대기 포트와 동일하게 변경해야 합니다.
	2.	방화벽/iptables로 인해 문제가 발생한 경우 /etc/sysconfig/iptables에 다음 줄을 추가합니다.  

			-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
			-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT

		두 번째 줄은 https 트래픽에만 필요합니다.

		이 줄은 전체적으로 액세스를 제한하는 다음과 같은 줄 위에 있어야 합니다.

			-A INPUT -j REJECT --reject-with icmp-host-prohibited  

		iptables를 다시 로드하려면 다음 명령을 실행합니다.

			service iptables restart  

		CentOS 6.3에서 테스트되었습니다.

###/var/www/html/에 프로젝트 파일을 업로드할 때 권한이 거부됨  

-	**증상** SFTP 클라이언트(예: FileZilla)를 사용하여 가상 컴퓨터에 연결한 다음 /var/www/html로 이동하여 사이트를 게시하는 경우 다음과 유사한 오류 메시지가 표시됩니다.  

		status:	Listing directory /var/www/html
		Command:	put "C:\Users\liang\Desktop\info.php" "info.php"
		Error:	/var/www/html/info.php: open for write: permission denied
		Error:	File transfer failed

-	**가능한 근본 원인** /var/www/html 폴더에 액세스할 수 있는 권한이 없습니다.
-	**해결 방법** 루트 계정에서 권한을 받아야 합니다. 해당 폴더의 소유권을 루트에서 컴퓨터를 프로비전할 때 사용한 사용자 이름으로 변경할 수 있습니다. 다음은 azureuser 계정 이름을 사용한 예제입니다.  

		sudo chown azureuser -R /var/www/html  

	-R 옵션을 사용하여 디렉터리 내의 모든 파일에 대해서도 권한을 적용합니다.

	이 명령은 디렉터리에서도 작동합니다. -R 옵션은 디렉터리 내의 모든 파일과 디렉터리에 대해 권한을 변경합니다. 다음은 예제입니다.

		sudo chown -R username:group directory  

	이 명령은 디렉터리 내의 모든 파일과 디렉터리 및 디렉터리 자체의 소유권(사용자 및 그룹 둘 다)을 변경합니다.

	다음 명령은 폴더 디렉터리의 사용 권한만 변경하고 디렉터리 내의 파일과 폴더는 그대로 유지합니다.

		sudo chown username:group directory  

###서버의 정규화된 도메인 이름을 확인할 수 없음

-	**증상** 다음 명령 중 하나를 사용하여 Apache 서버를 다시 시작하는 경우  

		sudo /etc/init.d/apache2 restart  # Debian release  

	또는

		sudo /etc/init.d/httpd restart   # Red Hat release  

	다음 오류가 표시됩니다.

		Restarting web server apache2
		apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName
		... waiting apache2:
		Could not reliably determine the server's fully qualified domain name, using 127.0.1.1 for ServerName  

-	**가능한 근본 원인** Apache의 서버 이름을 설정하지 않았습니다.

-	**솔루션** /etc/apache2의 httpd.conf(Red Hat 릴리스) 또는 apache2.conf(Debian 릴리스)에 "ServerName localhost" 줄을 삽입하고 Apache를 다시 시작합니다. 알림이 사라집니다.



[1]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-01.png
[2]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-02.png
[3]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-03.png
[4]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-04.png
[5]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-05.png
[6]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-06.png
[7]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-07.png
[8]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-08.png
[9]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-09.png
[10]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-10.png
[11]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-11.png
[12]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-12.png
[13]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-13.png
[14]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-14.png
[15]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-15.png
[16]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-16.png
[17]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-17.png
[18]: ./media/virtual-machines-linux-create-lamp-stack/virtual-machines-linux-create-lamp-stack-18.jpg

<!---HONumber=AcomDC_1223_2015-->