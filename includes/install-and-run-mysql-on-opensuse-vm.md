
1. 권한을 높이려면 다음을 입력합니다.
   
        sudo -s
   
    암호를 입력합니다.
2. MySQL Community Server 버전을 설치하려면 다음을 입력합니다.
   
        zypper install mysql-community-server
   
    MySQL이 다운로드되어 설치될 때까지 기다립니다.
3. 시스템이 부팅될 때 MySQL을 시작하도록 설정하려면 다음을 입력합니다.
   
        insserv mysql
4. 다음 명령을 사용하여 MySQL 데몬(mysqld)을 수동으로 시작합니다.
   
        rcmysql start
   
    MySQL 데몬의 상태를 검사하려면 다음을 입력합니다.
   
        rcmysql status
   
    MySQL 데몬을 중지하려면 다음을 입력합니다.
   
        rcmysql stop
   
   > [!IMPORTANT]
   > 설치한 후 MySQL 루트 암호는 기본적으로 비어있습니다. MySQL 보안에 도움이 되는 **mysql\_secure\_installation** 스크립트를 실행하는 것이 좋습니다. 스크립트에서 MySQL 루트 암호 변경, 익명 사용자 계정 삭제, 원격 루트 로그인 비활성화, 테스트 데이터베이스 제거, 권한 테이블 다시 로드 등의 작업을 수행하라는 메시지를 표시합니다. 이러한 옵션에 yes로 답하고 루트 암호를 변경하는 것이 좋습니다.
   > 
   > 
5. MySQL 설치 스크립트를 실행하려면 다음을 입력합니다.
   
        mysql_secure_installation
6. MySQL에 로그인합니다.
   
        mysql -u root -p
   
    MySQL 루트 암호(이전 단계에서 변경한 암호)를 입력하면 데이터베이스와 상호 작용하는 SQL 문을 실행할 수 있는 프롬프트가 표시됩니다.
7. 새 MySQL 사용자를 만들려면 **mysql>** 프롬프트에서 다음을 실행합니다.
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    줄 끝에 있는 세미콜론(;)은 명령을 끝내는 데 매우 중요한 역할을 합니다.
8. 데이터베이스를 만들고 `mysqluser` 사용자에게 사용 권한을 부여하려면 다음 명령을 실행합니다.
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    데이터베이스 사용자 이름과 암호는 스크립트를 데이터베이스에 연결하는 용도로만 사용되며,  데이터베이스 사용자 계정 이름에 시스템에 있는 실제 사용자 계정을 반영할 필요는 없습니다.
9. 다른 컴퓨터에서 로그인하려면 다음을 입력합니다.
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    여기서 `ip-address` 는 MySQL에 연결할 컴퓨터의 IP 주소입니다.
10. MySQL 데이터베이스 관리 유틸리티에서 나오려면 다음을 입력합니다.
    
        quit

## <a name="add-an-endpoint"></a>끝점 추가
1. MySQL이 설치되면 MySQL에 원격으로 액세스하도록 끝점을 구성해야 합니다. [Azure 클래식 포털][AzurePortal]에 로그인합니다. **Virtual Machines**를 클릭하고 새 가상 컴퓨터의 이름을 클릭한 다음 **끝점**을 클릭합니다.
2. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
3. 프로토콜이 **TCP**이고 **공용** 및 **개인** 포트가 "3306"으로 설정된 "MySQL"이라는 끝점을 추가합니다.
4. 컴퓨터에서 가상 컴퓨터에 원격으로 연결하려면 다음을 입력합니다.
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    예를 들어 이 자습서에서 만든 가상 컴퓨터를 사용하여 다음 명령을 입력합니다.
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
