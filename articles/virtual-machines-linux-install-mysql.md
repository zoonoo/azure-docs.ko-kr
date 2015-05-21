<properties
	pageTitle="Azure에 MySQL을 설치하는 방법"
	description="Azure에서 Linux VM(가상 컴퓨터)에 MySQL 스택을 설치하는 방법에 대해 알아봅니다. Ubuntu 또는 CentOS에 설치할 수 있습니다."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="mingzhan"/>


#Azure에 MySQL을 설치하는 방법


이 항목에서는 독자에게 Azure 계정이 이미 있다고 가정합니다. 그렇지 않을 경우 [Azure](http://azure.microsoft.com)를 방문하여 등록하는 것이 좋습니다.



##Microsoft Azure에 VM 이미지를 만들고 있습니다.
여기서는 Microsoft Azure 관리 포털에서 새 VM을 만듭니다.
###“SSH 인증 키” 생성
Azure 포털에 액세스하려면 SSH 키가 필요합니다.


- [여기](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 puttygen을 다운로드하여 설치합니다. 
- puttygen.exe를 실행합니다.
- "생성" 단추를 클릭하여 키를 생성합니다.


 ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p01.png)
 
- 키가 생성되면 다음과 같이 표시됩니다. 
 
 ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p02.png)

- 공개 키를 복사하여 “publicKey.key”라는 파일에 저장합니다. 저장된 공개 키의 형식은 원하는 공개 키와 다르므로 “공개 키 저장” 단추를 클릭하지 마세요.
- “개인 키 저장” 단추를 클릭하고 "privateKey.ppk"로 저장합니다. 

###Azure 포털 로그인

https://portal.azure.com/으로 이동하여 로그인합니다.

###Linux VM 만들기

왼쪽 아래에서 “새로 만들기”를 클릭하고 지침에 따라 요구에 맞는 Linux 이미지를 선택하여 이미지를 만듭니다. 예를 들어 여기서는 Ubuntu 14.04를 선택합니다.

  ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p03.png)

###호스트 이름 설정

"호스트 이름"의 경우 가상 컴퓨터에 액세스하는 데 사용할 수 있는 URL입니다. 예를 들어 DNS 이름을 "mysqlnode1"로 지정하면 Azure에서 "mysqlnode1.cloudapp.net"으로 URL을 생성합니다. "SSH 인증 키"의 경우 puttygen에서 생성하는 공개 키로, "publicKey.key" 파일 내용에서 복사해야 합니다.

  ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p04.png)
  

##MySQL 기본 포트 열기
Microsoft azure의 끝점은 공용 포트 및 개인 포트와 함께 프로토콜로 구성됩니다. 개인 포트는 서비스가 로컬 컴퓨터에서 수신 대기하는 포트입니다. 공용 포트는 서비스가 외부에서 수신 대기하는 포트입니다. 포트 3306은 MySQL에서 수신하게 되는 기본 포트 번호입니다. "찾아보기" ⇒ "가상 컴퓨터"를 클릭한 다음 만든 이미지를 클릭합니다.
 
   ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p05.png)


##만든 이미지에 연결
가상 컴퓨터에 연결하기 위한 SSH 도구를 선택할 수 있습니다. 예를 들어 여기서는 Putty를 사용합니다.
 

- 먼저 Putty를 다운로드합니다. Putty의 다운로드 URL은 여기에 있습니다.
- Putty를 다운로드한 후 실행 파일 “PUTTY.EXE”를 클릭합니다. 다음과 같이 설정합니다.


     The “Host Name (or IP address)” is the URL as “DNS NAME” when you create an image.
     
     The “Port” we can chose 22.  This is default port of SSH services.

   ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p06.png)
 
- 열기를 선택하기 전에 연결 \> SSH \> 인증 탭을 클릭하여 Puttygen에서 생성한 파일을 찾고 개인 키를 포함합니다. 채울 필드는 아래 스크린샷을 참조하세요.

   ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p07.png)
 
- 그런 다음 "열기"를 클릭합니다. 그러면 연결하는 컴퓨터가 연결하려는 컴퓨터가 아닐 수 있다는 경고가 메시지 상자에 표시될 수 있습니다. DNS 이름과 포트 번호를 올바르게 구성한 경우 “예”를 클릭합니다.
  
 ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p08.png)

- 그러면 다음과 같이 표시됩니다. 
 
 ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p09.png)


##가상 컴퓨터에 MySQL 설치
MySQL에는 이진 패키지, rpm 패키지 및 원본 패키지라는 세 가지 설치 방법이 지원됩니다. 예를 들어 이 문서에서는 성능을 고려하여 MySQL 5.6에 rpm 패키지를 사용합니다. MySQL5.6은 MySQL5.5에 비해 성능이 현저하게 향상되었습니다. 자세한 내용은 [여기](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)를 참조하세요.


###Ubuntu 또는 Debian에 MySQL5.6을 설치하는 방법
예를 들어 이 문서에서는 Ubuntu 14.04 LTS를 사용합니다.

- 1단계: MySQL Server 5.6 설치

    apt-get 명령을 사용하여 mysql-server 5.6 설치

              # azureuser@mysqlnode1:~$ sudo apt-get update
              # azureuser@mysqlnode1:~$ sudo apt-get -y install mysql-server-5.6

    설치하는 동안 MySQL 루트 암호를 설정하라는 팝업 대화 상자가 표시됩니다. 새 MySQL 사용자 루트 암호를 지정해야 합니다. 다음은 스크린샷입니다.

 ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p10.png)

    Confirm the password again when it is asked.

 ![이미지](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p11.png)
 
- 2단계: MySQL Server에 로그인

    MySQL 서버 설치가 완료되면 MySQL 서비스가 자동으로 시작됩니다. 사용자 루트로 MySQL 서버에 로그인할 수 있습니다. MySQL Server에 로그인하려면 아래 명령을 사용하세요. mysql server 설치 중에 설정한 mysql 루트 암호를 묻습니다.

             # azureuser@mysqlnode1:~$ mysql -uroot -p

- 3단계: VM에서 MySQL 서비스 확인
    
    로그온하여 MySQL 서비스가 실행되고 있는지 확인한 후 다음 명령을 사용하여 서비스를 시작/다시 시작할 수 있습니다.

    \(a\) MySQL 서비스의 상태를 가져옵니다.

             #sudo service mysql status

    \(b\) MySQL 서비스를 시작합니다.

             #sudo service mysql start

    \(c\) MySQL 서비스를 중지합니다.

             #sudo service mysql stop

    \(d\) MySQL 서비스를 다시 시작합니다.

             #sudo service mysql restart


###Redhat OS 제품군 또는 Oracle Linux에 MySQL을 설치하는 방법
- 1단계: MySQL Yum 리포지토리 추가 루트 권한을 가져오려면 다음 명령을 실행합니다. 

            #sudo su -
            #[root@azureuser ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm 
            #[root@azureuser ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm 

- 2단계: 릴리스 시리즈 선택
 
            #[root@azureuser ~]# vim /etc/yum.repos.d/mysql-community.repo

    파일에 있는 릴리스 시리즈의 하위 리포지토리에 대한 일반적인 항목입니다.

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- 3단계: Yum을 사용하여 MySQL 설치 다음 명령을 사용하여 MySQL을 설치합니다.

           #[root@azureuser ~]#yum install mysql-community-server 

    MySQL 서버에 대한 패키지뿐만 아니라 다른 필수 패키지를 설치합니다.

- 4단계: MySQL의 실행 상태 확인

    다음 명령을 사용하여 MySQL 서버의 상태를 확인할 수 있습니다.
   
           #[root@azureuser ~]#service mysqld status

    MySQL 서버의 기본 포트가 실행 중인지 여부를 확인할 수 있습니다.

           #[root@azureuser ~]#netstat  –tunlp|grep 3306

- 5단계: MySQL 서버 시작 및 중지

    다음 명령을 사용하여 MySQL 서버를 시작합니다.

           #[root@azureuser ~]#service mysqld start

    다음 명령을 사용하여 MySQL 서버를 중지합니다.

           #[root@azureuser ~]#service mysqld stop

    시스템이 부팅될 때 MySQL을 시작하도록 설정하려면 다음 명령을 실행합니다.

           #[root@azureuser ~]#chkconfig mysqld on


###Suse linux에 MySQL을 설치하는 방법

- 1단계: MySQL 서버 설치

    사용 권한을 상승시키려면 다음 명령을 실행합니다.

           #sudo su -

    다음 명령을 사용하여 MySQL을 설치합니다.

           #mysql-test:~ # zypper update

           #mysql-test:~ # zypper install mysql-server mysql-devel mysql

- 2단계: MySQL의 실행 상태 확인

    다음 명령을 사용하여 MySQL 서버의 상태를 확인할 수 있습니다.

           #mysql-test:~ # rcmysql status

    MySQL 서버의 기본 포트가 실행 중인지 여부를 확인할 수 있습니다.

           #mysql-test:~ # netstat  –tunlp|grep 3306

- 3단계: MySQL 서버 시작 및 중지

    다음 명령을 사용하여 MySQL 서버를 시작합니다.

           #mysql-test:~ # rcmysql start

    다음 명령을 사용하여 MySQL 서버를 중지합니다.

           #mysql-test:~ # rcmysql stop

    시스템이 부팅될 때 MySQL을 시작하도록 설정하려면 다음 명령을 실행합니다.

           #mysql-test:~ # insserv mysql
<!--HONumber=52-->
