<properties 
	pageTitle="SQL 데이터베이스를 관리하는 방법" 
	description="Azure SQL 데이터베이스를 관리하는 방법에 대해 알아봅니다." 
	headerExpose="" 
	footerExpose="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="" 
	services="sql-database" 
	documentationCenter=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>


## 개요

이 문서에서는 Azure SQL 데이터베이스에서 간단한 관리 작업을 수행하는 방법을 보여 줍니다. 

## 방법: Management Studio를 사용하여 Azure의 SQL 데이터베이스에 연결

Management Studio는 단일 작업 영역에서 여러 SQL Server 인스턴스 및 서버를 관리할 수 있는 관리 도구입니다. 온-프레미스 SQL Server 인스턴스가 이미 있는 경우 온-프레미스 인스턴스 및 Azure의 논리 서버에 대한 연결을 모두 열어 작업을 병렬로 처리할 수 있습니다.

Management Studio에는 구문 검사기, 다시 사용하기 위해 스크립트 및 명명된 쿼리를 저장하는 기능과 같이 현재 관리 포털에서 사용할 수 없는 기능이 포함되어 있습니다. SQL 데이터베이스는 단순히 TDS(Tabular Data Stream) 끝점입니다. Management Studio를 비롯하여 TDS를 사용하여 작업하는 도구는 SQL 데이터베이스 작업에 사용할 수 있습니다. 온-프레미스 서버용으로 개발하는 스크립트는 SQL 데이터베이스 논리 서버에서 실행됩니다. 

다음 단계에서는 Management Studio를 사용하여 Azure의 논리 서버에 연결합니다. 이 단계를 수행하려면 SQL Server Management Studio 버전 2008 R2 또는 2012가 필요합니다. Management Studio를 다운로드하거나 연결하는 데 도움이 필요한 경우에는 이 사이트에서 [Management Studio를 사용하여 SQL 데이터베이스 관리][]를 참조하세요.

연결하려면 먼저 로컬 시스템에서 포트 1433에 대한 아웃바운드 요청을 허용하는 방화벽 예외를 만들어야 하는 경우가 있습니다. 기본적으로 안전한 컴퓨터의 경우 일반적으로 포트 1433이 열려 있지 않습니다. 

## 온-프레미스 서버용 방화벽 구성

1. 고급 보안이 포함된 Windows 방화벽에서 새 아웃바운드 규칙을 만듭니다.

2. **포트**를 선택하고 TCP 1433과 **연결 허용**을 지정한 후 **공용** 프로필이 선택되었는지 확인합니다.

3. 규칙에 대해 *WindowsAzureSQLDatabase (tcp-out) port 1433* 등의 의미 있는 이름을 지정합니다. 


## 논리 서버에 연결

1. Management Studio의 서버에 연결에서 데이터베이스 엔진이 선택되어 있는지 확인한 후 논리 서버 이름을 *servername*.database.widnows.net 형식으로 입력합니다.

	관리 포털, 서버 대시보드, MANAGE URL에서 정규화된 서버 이름을 가져올 수도 있습니다.

2. 인증에서 **SQL Server 인증**을 선택한 후 논리 서버를 구성할 때 만든 관리자 로그인을 입력합니다.

3. **옵션**을 클릭합니다. 

4. 데이터베이스에 연결에서 **master**를 지정합니다.


## 온-프레미스 서버에 연결

1. Management Studio의 서버에 연결에서 데이터베이스 엔진이 선택되어 있는지 확인한 후 로컬 인스턴스 이름을 *servername**instancename* 형식으로 입력합니다. 서버가 로컬이며 기본 인스턴스인 경우 *localhost*를 입력합니다.

2. 인증에서 **Windows 인증**을 선택한 후 sysadmin 역할의 멤버인 Windows 계정을 입력합니다.


## 방법: Azure SQL 데이터베이스에 로그인 및 사용자 추가

데이터베이스를 배포한 후에는 로그인을 구성하고 권한을 할당해야 합니다. 이 단계에서는 두 개의 스크립트를 실행합니다.

첫 번째 스크립트의 경우 마스터에 연결하여 로그인을 만드는 스크립트를 실행합니다. 로그인을 사용하여 읽기 및 쓰기 작업을 지원하고 'sa' 권한 없이 시스템 쿼리를 실행하는 기능과 같은 운영 작업을 위임합니다.

만드는 로그인은 SQL Server 인증 로그인이어야 합니다. Windows 사용자 ID 또는 클레임 ID를 사용하는 스크립트가 이미 만들어져 있는 경우 해당 스크립트는 SQL 데이터베이스에 대해 작동하지 않습니다.

두 번째 스크립트는 데이터베이스 사용자 권한을 할당합니다. 이 스크립트의 경우 이미 Azure에 로드된 데이터베이스에 연결됩니다.

## 로그인 만들기

1. Management Studio에서 Azure의 논리 서버에 연결하고 데이터베이스 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭하여 **새 쿼리**를 선택합니다.

2. 다음 Transact-SQL 문을 사용하여 로그인을 만듭니다. 암호를 올바른 암호로 바꿉니다. 각 항목을 개별적으로 선택한 후 **실행**을 클릭합니다. 나머지 로그인에 대해 위 절차를 반복합니다.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## 데이터베이스 사용자 만들기

1. 데이터베이스 폴더를 확장하고 **school**을 마우스 오른쪽 단추로 클릭한 후 **새 쿼리**를 선택합니다.

2. 다음 Transact-SQL 문을 사용하여 데이터베이스 사용자를 추가합니다. 암호를 올바른 암호로 바꿉니다. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## 로그인 보기 및 테스트

1. 새 쿼리 창에서 **master**에 연결하고 다음 문을 실행합니다. 

        SELECT * from sys.sql_logins;

2. Management Studio에서 **school** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

3. 쿼리 메뉴에서 **연결**을 가리킨 후 **연결 변경**을 클릭합니다.

4. 그런 다음 *sqlreader*로 로그인합니다.

5. 다음 문을 복사하여 실행합니다. 개체가 존재하지 않는다는 오류가 나타납니다.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. 두 번째 쿼리 창을 열고 연결 컨텍스트를 *sqlwriter*로 변경합니다. 동일한 쿼리가 이제 성공적으로 실행됩니다.

이제 몇 가지 로그인을 만들고 테스트했습니다. 자세한 내용은 [SQL 데이터베이스에서 데이터베이스 및 로그인 관리][] 및 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링][]을 참조하세요.

[SQL 데이터베이스에서 데이터베이스 및 로그인 관리]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[Management Studio를 사용하여 SQL 데이터베이스 관리]: http://www.windowsazure.com/develop/net/common-tasks/sql-azure-management/






<!--HONumber=47-->
 