<properties 
	pageTitle="Azure에서 MySQL을 실행하는 가상 컴퓨터 만들기" 
	description="Windows Server 2012 R2를 실행하는 Azure 가상 컴퓨터를 만든 다음 해당 가상 컴퓨터에서 MySQL 데이터베이스를 설치 및 구성하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="kathydav"/>


# Azure에서 Windows Server 2012 R2를 실행하는 가상 컴퓨터에 MySQL 설치


[MySQL](http://www.mysql.com)은 인기 있는 오픈 소스 SQL 데이터베이스입니다. [Azure 관리 포털](http://manage.windowsazure.com)을 사용하여 이미지 갤러리에서 Windows Server 2012 R2를 실행하는 가상 컴퓨터를 만들 수 있습니다. 그런 다음 해당 가상 컴퓨터를 설치하고 MySQL Server로 구성할 수 있습니다.


이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- 관리 포털을 사용하여 Windows Server 2012 R2를 실행하는 가상 컴퓨터를 만듭니다.

- 커뮤니티 버전의 MySQL 5.6.23을 가상 컴퓨터에 설치하고 MySQL Server로 실행합니다.


## Windows Server를 실행하는 가상 컴퓨터 만들기

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## 데이터 디스크 연결

가상 컴퓨터가 만들어진 후에 선택적으로 추가 데이터 디스크를 연결할 수 있습니다. 이는 프로덕션 작업에 권장되며, 현재 127GB로 제한되고 운영 체제를 포함하는 OS 드라이브(C:)의 공간 부족을 방지합니다.

[Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](storage-windows-attach-disk.md) 을 참조하고 빈 디스크 연결 지침을 따르세요. 호스트 캐시 설정을 **없음** 또는 **읽기 전용**으로 설정합니다.

## 가상 컴퓨터에 로그온

다음에는 MySQL을 설치할 수 있게 가상 컴퓨터에 로그온합니다.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]
 
## 가상 컴퓨터에서 MySQL Community Server 설치 및 실행

커뮤니티 버전의 MySQL Server를 설치, 구성 및 실행하려면 다음 단계를 따르세요.

> [AZURE.NOTE] 다음 단계는 5.6.23.0 커뮤니티 버전의 MySQL 및 Windows Server 2012 R2에 적용됩니다. 다른 버전의 MySQL 또는 Windows Server에서는 작업 단계가 다를 수 있습니다.

1.	원격 데스크톱을 사용하여 가상 컴퓨터에 연결한 후 시작 화면에서 **Internet Explorer**를 클릭합니다.
2.	오른쪽 위에 있는 **도구** 단추(톱니바퀴 아이콘)를 선택하고 **인터넷 옵션**을 클릭합니다. **보안** 탭, **신뢰할 수 있는 사이트** 아이콘, **사이트** 단추를 클릭합니다. 신뢰할 수 있는 사이트 목록에 **http://*.mysql.com**을 추가합니다. **닫기**를 클릭한 후 **확인**을 클릭합니다.
3.	Internet Explorer 주소 표시줄에 **http://dev.mysql.com/downloads/mysql/**을 입력합니다.
4.	MySQL 사이트를 사용하여 최신 버전의 Windows용 MySQL Installer를 찾아서 다운로드합니다. MySQL Installer를 선택한 경우 전체 파일 집합이 있는 버전(예: 파일 크기가 282.4MB인 mysql-installer-community-5.6.23.0.msi)을 다운로드하고 설치 관리자 파일을 Windows 바탕 화면에 저장합니다.
5.	바탕 화면에서 설치 관리자 파일을 두 번 클릭하여 설치를 시작합니다.
6.	**사용권 계약** 페이지에서 사용권 계약에 동의하고 **다음**을 클릭합니다.
7.	**설치 유형** 페이지에서 원하는 설치 유형을 클릭한 후 **다음**을 클릭합니다. 다음 단계에서는 **서버만** 설치 유형을 선택한 것으로 가정합니다.
8.	**설치** 페이지에서 **실행**을 클릭합니다. 설치가 완료되면 **다음**을 클릭합니다.
9.	**제품 구성** 페이지에서 **다음**을 클릭합니다.
10.	**유형 및 네트워킹** 페이지에서 원하는 구성 유형 및 연결 옵션을 지정합니다(필요한 경우 TCP 포트 포함). **고급 옵션 표시**를 클릭한 후 **다음**을 클릭합니다.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_TypeNetworking.png)
 
11.	**계정 및 역할** 페이지에서 강력한 MySQL 루트 암호를 지정합니다. 필요에 따라 추가 MySQL 사용자 계정을 추가하고 **다음**을 클릭합니다.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AccountsRoles_Filled.png)
 
12.	**Windows 서비스** 페이지에서 필요에 따라 Windows 서비스로 실행 중인 MySQL Server에 대한 기본 설정을 변경하고 **다음**을 클릭합니다.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_WindowsService.png)
 
13.	**고급 옵션** 페이지에서 필요에 따라 로깅 옵션을 변경하고 **다음**을 클릭합니다.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AdvOptions.png)
 
14.	**서버 구성 적용** 페이지에서 **실행**을 클릭합니다. 구성 단계가 완료되면 **마침**을 클릭합니다.
15.	**제품 구성** 페이지에서 **다음**을 클릭합니다.
16.	**설치 완료** 페이지에서 **클립보드에 로그 복사**를 클릭(나중에 검사하려는 경우)하고 **마침**을 클릭합니다.
17.	시작 화면에서 **mysql**을 입력하고 **MySQL 5.6 명령줄 클라이언트**를 클릭합니다.
18.	11단계에서 지정한 루트 암호를 입력하면 명령을 실행하여 MySQL을 구성할 수 있는 프롬프트가 표시됩니다. 명령 및 구문에 대한 자세한 내용은 [MySQL 참조 설명서](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)를 참조하세요.

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_CommandPrompt.png)
 
19.	또한 **C:\Program Files (x86)\MySQL\MySQL Server 5.6\my-default.ini** 파일의 항목을 사용하여 기본/데이터 디렉터리 및 드라이브와 같은 서버 구성 기본 설정을 구성할 수 있습니다. 자세한 내용은 [5.1.2 서버 구성 기본값](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)을 참조하세요.


MySQL 클라이언트 컴퓨터에서 인터넷을 통해 MySQL Server 서비스를 사용할 수 있도록 하려면 MySQL Server 서비스가 수신 대기 중인 TCP 포트의 끝점을 구성하고 추가 Windows 방화벽 규칙을 만들어야 합니다. 유형 및 네트워킹 페이지(위 절차의 10단계)에서 다른 포트를 지정하지 않은 경우 이는 TCP 포트 3306입니다. 


> [AZURE.NOTE] 이 경우 인터넷상의 모든 컴퓨터가 MySQL Server 서비스를 사용할 수 있으므로 보안에 주의해야 합니다. ACL(액세스 제어 목록)에서 끝점을 사용할 수 있는 원본 IP 주소 집합을 정의할 수 있습니다. 자세한 내용은 [가상 컴퓨터로 끝점을 설정하는 방법](virtual-machines-set-up-endpoints.md)을 참조하세요.


MySQL Server 서비스의 끝점을 구성하려면

1.	Azure 관리 포털에서 **가상 컴퓨터**를 클릭하고 MySQL 가상 컴퓨터의 이름을 클릭한 다음 **끝점**을 클릭합니다.
2.	명령 모음에서 **추가**를 클릭합니다.
3.	**가상 컴퓨터에 끝점 추가** 페이지에서 오른쪽 화살표를 클릭합니다.
4.	기본 MySQL TCP 포트 3306을 사용하는 경우 **이름**에서 **MySQL**을 클릭한 후 확인 표시를 클릭합니다.
5.	다른 TCP 포트를 사용하는 경우 **이름**에 고유한 이름을 입력합니다. 프로토콜에서 **TCP**를 선택하고 **공용 포트**와 **개인 포트** 둘 다에 포트 번호를 입력한 후 확인 표시를 클릭합니다.

인터넷에서의 MySQL 트래픽을 허용하는 Windows 방화벽 규칙을 추가하려면 MySQL Server 컴퓨터의 관리자 수준 Windows PowerShell 명령 프롬프트에서 이 명령을 실행합니다.

	New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound -Protocol TCP -LocalPort 3306 -Action Allow -Profile Public

Azure 가상 컴퓨터에서 실행되는 MySQL Server 서비스에 대한 원격 연결을 테스트하려면 먼저 MySQL Server를 실행 중인 가상 컴퓨터가 포함된 클라우드 서비스에 해당하는 DNS 이름을 확인해야 합니다. 

1.	Azure 관리 포털에서 **가상 컴퓨터**를 클릭하고 MySQL Server 가상 컴퓨터의 이름을 클릭한 다음 **대시보드**를 클릭합니다.
2.	가상 컴퓨터 대시보드에서 **빠른 보기** 섹션에 있는 **DNS 이름** 값을 적어 둡니다. 다음은 예제입니다. 

	![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_DNSName.png)
 
3.	MySQL을 실행 중인 로컬 컴퓨터 또는 MySQL 클라이언트에서 다음 명령을 실행하여 MySQL 사용자로 로그인합니다.

		mysql -u <yourMysqlUsername> -p -h <yourDNSname>
	
	예를 들어 MySQL 사용자 이름이 **dbadmin3**이고, 가상 컴퓨터의 DNS 이름이 **testmysql.cloudapp.net**인 경우 다음 명령을 실행합니다.

		mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## 리소스

MySQL에 대한 자세한 내용은 [MySQL 설명서](http://dev.mysql.com/doc/)를 참조하세요.



<!--HONumber=47-->
