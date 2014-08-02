<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Azure에서 Windows Server 2008 R2를 실행하는 가상 컴퓨터에 MySQL 설치
====================================================================

[MySQL](http://www.mysql.com)은 인기 있는 오픈 소스 SQL 데이터베이스입니다. [Azure 관리 포털](http://manage.windowsazure.com)을 사용하여 이미지 갤러리에서 Windows Server 2008 R2를 실행하는 가상 컴퓨터를 만들 수 있습니다. 그런 다음 가상 컴퓨터에서 MySQL 데이터베이스를 설치하고 구성할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

-   관리 포털을 사용하여 Windows Server 2008 R2를 실행하는 가상 컴퓨터를 만듭니다.

-   가상 컴퓨터에서 MySQL Community Server를 설치하고 실행합니다.

Windows Server 2008 R2를 실행하는 가상 컴퓨터 만들기
----------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

데이터 디스크 연결
------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

가상 컴퓨터에서 MySQL Community Server 설치 및 실행
---------------------------------------------------

MySQL Community Server를 설치, 구성 및 실행하려면 다음 단계를 따르십시오.

1.  원격 데스크톱을 사용하여 가상 컴퓨터에 연결한 후 **시작** 메뉴에서 **Internet Explorer**를 엽니다.

2.  오른쪽 위에 있는 **도구** 단추를 선택합니다. **인터넷 옵션**에서 **보안** 탭을 선택한 후 **신뢰할 수 있는 사이트** 아이콘을 선택하고 마지막으로 **사이트** 단추를 클릭합니다. 신뢰할 수 있는 사이트 목록에 *http://\*.mysql.com*을 추가합니다.

3.  [MySQL Community Server 다운로드](http://www.mysql.com/downloads/mysql/)(영문)로 이동합니다.

4.  **Platform** 드롭다운 메뉴에서 **Microsoft Windows**를 선택한 후 **Select**를 클릭합니다.

5.  최신 버전의 **Windows (x86, 64-bit), MSI Installer**를 찾아서 **Download**를 클릭합니다.

6.  **No thanks, just start my download!**를 선택합니다. 또는 계정에 등록합니다. 메시지가 표시되면 MySQL Installer를 다운로드할 미러 사이트를 선택하고 데스크톱에 설치 관리자를 저장합니다.

7.  데스크톱에서 설치 관리자 파일을 두 번 클릭하여 설치를 시작합니다.

8.  **Next**를 클릭합니다.

    ![MySQL 설치](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png)

9.  사용권 계약에 동의하고 **Next**를 클릭합니다.

    ![MySQL 설치](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png)

10. **Typical**을 클릭하여 일반 기능을 설치합니다.

    ![MySQL 설치](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png)

11. **Install**을 클릭합니다.

    ![MySQL 설치](./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png)

12. MySQL Configuration Wizard를 시작하고 **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png)

13. **Detailed Configuration**을 선택하고 Next를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png)

14. 서버에서 다른 응용 프로그램과 함께 MySQL을 실행하려는 경우 **Server Machine**을 선택하고 그렇지 않으면 요구에 가장 적합한 다른 옵션을 선택합니다. **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png)

15. **Multifunctional Database**를 선택하거나 요구에 가장 적합한 다른 옵션을 선택합니다. **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png)

16. 이전 단계에서 연결한 데이터 드라이브를 선택합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png)

17. **Decision Support (DSS)/OLAP**를 선택하거나 요구에 가장 적합한 다른 옵션을 선택합니다. **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png)

18. **Enable TCP/IP Networking** 및 **Add firewall exception for this port**를 선택합니다. 두 번째 옵션을 선택하면 Windows 방화벽에 이름이 **MySQL Server**인 인바운드 규칙이 만들어집니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png)

19. 다른 여러 언어로 텍스트를 저장해야 하는 경우 **Best Support For Multilingualism**을 선택하고 그렇지 않으면 요구에 가장 적합한 다른 옵션을 선택합니다. **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png)

20. **Install As Windows Service** 및 **Launch the MySQL Server automatically**를 선택합니다. 또한 **Include Bin Directory in Windows PATH**도 선택합니다. **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png)

21. 루트 암호를 입력합니다. **Enable root access from remote machines** 또는 **Create An Anonymous Account**는 선택하지 않습니다. **Next**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png)

22. **Execute**를 클릭하고 구성이 완료될 때까지 기다립니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png)

23. **Finish**를 클릭합니다.

    ![MySQL 구성](./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png)

24. **시작**을 클릭하고 **MySQL 5.x Command Line Client**를 선택하여 명령줄 클라이언트를 시작합니다.

25. 프롬프트에서 루트 암호(이전 단계에서 설정한 암호)를 입력하면 데이터베이스를 조작하는 SQL 문을 실행할 수 있는 프롬프트가 표시됩니다.

26. 새 MySQL 사용자를 만들려면 **mysql\>** 프롬프트에서 다음을 실행합니다.

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    줄 끝에 있는 세미콜론(;)은 명령을 끝내는 데 매우 중요한 역할을 합니다.

27. 데이터베이스를 만들어서 `mysqluser` 사용자 권한을 부여하려면 다음 명령을 실행합니다.

         mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    데이터베이스 사용자 이름과 암호는 스크립트를 데이터베이스에 연결하는 용도로만 사용되며, 데이터베이스 사용자 계정 이름에는 컴퓨터에 있는 실제 사용자 계정을 반영할 필요가 없습니다.

28. 다른 컴퓨터에서 로그인하려면 다음을 실행합니다.

         mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    여기서 `ip-address`는 MySQL에 연결할 컴퓨터의 IP 주소입니다.

29. MySQL 명령줄 클라이언트를 종료하려면 다음 명령을 실행합니다.

        quit

30. MySQL이 설치되면 MySQL에 원격으로 액세스할 수 있도록 끝점을 구성해야 합니다. [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다. Azure 포털에서 **가상 컴퓨터**, 새 가상 컴퓨터의 이름, **끝점**, **끝점 추가**를 차례로 클릭합니다.

    ![끝점](./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png)

31. **끝점 추가**를 선택하고 화살표를 클릭하여 계속합니다.

    ![끝점](./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png)

32. 이름이 "MySQL"이고 프로토콜이 **TCP**이며 **공용** 및 **개인** 포트를 "3306"로 설정한 끝점을 추가합니다. 확인 표시를 클릭합니다. 이렇게 하면 MySQL에 원격으로 액세스할 수 있습니다.

    ![끝점](./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png)

33. Azure의 가상 컴퓨터에서 실행되는 MySQL에 원격으로 연결할 수 있습니다. MySQL을 실행하는 로컬 컴퓨터에서 다음 명령을 실행하여 이전 단계에서 만든 **mysqluser** 사용자로 로그인합니다.

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    예를 들어 이 자습서에서 만든 가상 컴퓨터를 사용하는 경우 다음 명령을 사용합니다.

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

요약
----

이 자습서에서는 Windows 2008 R2 가상 컴퓨터를 만들어 원격으로 연결하는 방법에 대해 알아보았습니다. 또한 가상 컴퓨터에 MySQL을 설치하고 구성하여 데이터베이스 및 새 MySQL 사용자를 만드는 방법에 대해서도 알아보았습니다. MySQL에 대한 자세한 내용은 [MySQL 설명서](http://dev.mysql.com/doc/)(영문)를 참조하십시오.

