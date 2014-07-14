1.  권한을 높이려면 다음을 실행합니다.
    
         sudo -s
    
    암호를 입력합니다.

2.  MySQL Community Server 버전을 설치하려면 다음 명령을 실행합니다.
	
		# zypper install mysql-community-server
    
    MySQL이 다운로드되어 설치될 때까지 기다립니다.

3.  시스템이 부팅될 때 MySQL을 시작하도록 설정하려면 다음 명령을 실행합니다.

		# insserv mysql

4.  이제 다음 명령을 사용하여 MySQL 데몬(mysqld)을 수동으로 시작할 수 있습니다.

		# rcmysql start
    
    MySQL 데몬의 상태를 검사하려면 다음을 실행합니다.

		# rcmysql status
    
    MySQL 데몬을 중지하려면 다음을 실행합니다.

		# rcmysql stop

5.  경고! 설치한 후 MySQL 루트 암호는 기본적으로 비어있습니다. MySQL 보안에 도움이 되는 **mysql\_secure\_installation** 스크립트를 실행하는 것을 권장합니다. **mysql\_secure\_installation**을 실행하면 MySQL 루트 암호 변경, 익명 사용자 계정 삭제, 원격 루트 로그인 비활성화, 테스트 데이터베이스 제거, 권한 테이블 다시 로드 등의 작업을 수행하라는 메시지가 표시됩니다. 이러한 옵션에 yes로 답하고 루트 암호를 변경하는 것을 권장합니다. 스크립트를 실행하려면 다음 명령을 실행합니다.
    
         $ mysql_secure_installation

6.  스크립트를 실행하고 나면 MySQL에 로그인할 수 있습니다.
    
         $ mysql -u root -p
    
    MySQL 루트 암호(이전 단계에서 변경한 암호)를 입력하면 데이터베이스와 상호 작용하는 SQL 문을 실행할 수 있는 프롬프트가 표시됩니다.

7.  새 MySQL 사용자를 만들려면 **mysql>** 프롬프트에서 다음을 실행합니다.
    
         mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
    
    줄 끝에 있는 세미콜론(;)은 명령을 끝내는 데 매우 중요한 역할을 합니다.

8.  데이터베이스를 만들어서 `mysqluser` 사용자 권한을 부여하려면 다음 명령을 실행합니다.
    
         mysql> CREATE DATABASE testdatabase;
         mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
    
    데이터베이스 사용자 이름과 암호는 스크립트를 데이터베이스에 연결하는 용도로만 사용되며, 데이터베이스 사용자 계정 이름에 시스템에 있는 실제 사용자 계정을 반영할 필요는 없습니다.

9.  다른 컴퓨터에서 로그인하려면 다음을 실행합니다.
    
         mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip -address>' IDENTIFIED BY 'password';
    
    여기서 `ip-address`는 MySQL에 연결할 컴퓨터의 IP 주소입니다.

10. MySQL 데이터베이스 관리 유틸리티에서 나오려면 다음 명령을 실행합니다.
    
        quit

11. MySQL이 설치되면 MySQL에 원격으로 액세스할 수 있도록 끝점을 구성해야 합니다. [Azure 관리 포털][1]에 로그인합니다. Azure 포털에서 **Virtual Machines**를 클릭한 후 새로운 VM 이름과 **끝점**을 차례로 클릭합니다.
    
    ![끝점](./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpoint.png)

12. 페이지 맨 아래에 있는 **Add Endpoint**를 클릭합니다.
    ![끝점](./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpoint2.png)

13. 이름이 "MySQL"이고 프로토콜이 **TCP**이며 **Public** 및 **Private** 포트를 "3306"로 설정한 끝점을 추가합니다. 이렇게 하면 MySQL에 원격으로 액세스할 수 있습니다.
    ![끝점](./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png)

14. Azure 내의 OpenSUSE 가상 컴퓨터에서 실행되는 MySQL에 원격으로 연결하려면 로컬 컴퓨터에서 다음 명령을 실행합니다.
    
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
    
    예를 들어 이 자습서에서 만든 가상 컴퓨터를 사용하려면 다음 명령을 사용합니다.
    
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. 이제 성공적으로 MySQL을 구성하고 데이터베이스와 새 사용자를 만들었습니다. MySQL에 대한 자세한 정보는 [MySQL Documentation][2](영문)을 참조하십시오.



[1]: http://manage.windowsazure.com
[2]: http://dev.mysql.com/doc/