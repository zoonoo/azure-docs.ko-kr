<properties 
	pageTitle="SSMS를 사용하여 SQL 데이터베이스 관리 | Microsoft Azure" 
	description="SQL Server Management Studio를 사용하여 SQL 데이터베이스 서버 및 데이터베이스를 관리하는 방법에 대해 알아봅니다." 
	services="sql-database" 
	documentationCenter=".net" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="jeffreyg"/>


# SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리 


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

SSMS(SQL Server Management Studio)를 사용하여 Azure SQL 데이터베이스 논리 서버 및 데이터베이스를 관리할 수 있습니다. 이 항목에서는 SSMS로 수행하는 일반적인 작업에 대해 설명합니다. 시작하기 전에 Azure SQL 데이터베이스에서 논리 서버 및 데이터 베이스가 생성되어 있어야 합니다. 시작하려면 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 읽은 다음 다시 돌아오세요.

Azure SQL 데이터베이스를 사용하여 작업할 경우 최신 버전의 SSMS를 사용하는 것이 좋습니다. 다운로드하려면 [SQL Server Management Studio 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 방문하세요.


## SQL 데이터베이스 논리 서버에 연결

SQL 데이터베이스에 연결하려면 Azure의 서버 이름을 알아야 합니다. 이 정보를 얻기 위해 포털에 등록해야 할 수도 있습니다.

1.  [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2.  왼쪽 창에서 **SQL 데이터베이스**를 클릭합니다.

3.  SQL 데이터베이스 홈 페이지에서 페이지 위쪽의 **서버**를 클릭하여 구독과 연결된 서버를 모두 나열합니다. 연결할 서버의 이름을 찾아 클립보드로 복사합니다.

	다음으로 로컬 컴퓨터로부터의 연결을 허용하도록 SQL 데이터베이스 방화벽을 구성합니다. 방화벽 예외 목록에 로컬 컴퓨터 IP 주소를 추가하면 연결이 허용됩니다.

1.  SQL 데이터베이스 홈 페이지에서 **서버**를 클릭한 후 연결할 서버를 클릭합니다.

2.  페이지 위쪽에서 **구성**을 클릭합니다.

3.  현재 클라이언트 IP 주소에 있는 IP 주소를 복사합니다.

4.  구성 페이지의 **허용된 IP 주소**에는 처음 값과 마지막 값인 규칙 이름과 IP 주소 범위를 지정할 수 있는 상자가 세 개 포함되어 있습니다. 규칙 이름에 컴퓨터 이름을 입력할 수 있습니다. 시작 및 끝 범위에는 사용자 컴퓨터의 IP 주소를 두 상자에 붙여넣은 후 나타나는 확인란을 클릭합니다.

	규칙 이름은 고유해야 합니다. 이 컴퓨터가 개발 컴퓨터이면 IP 범위 시작 상자와 IP 범위 끝 상자 둘 다에 IP 주소를 입력할 수 있습니다. 그렇지 않은 경우 조직에 추가 컴퓨터로부터의 연결을 허용하기 위해 더욱 넓은 범위의 IP 주소를 입력해야 할 수도 있습니다.
 
5. 페이지 아래쪽에서 **저장**을 클릭합니다.

    **참고:** 방화벽 설정의 변경 내용이 적용되기까지는 5분 정도의 시간이 소요될 수 있습니다.

	이제 Management Studio를 사용하여 SQL 데이터베이스에 연결할 준비가 되었습니다.

1.  작업 표시줄에서 **시작**을 클릭하고 **모든 프로그램**을 가리키고 **Microsoft SQL Server 2014**를 가리킨 후 **SQL Server Management Studio**를 클릭합니다.

2.  **서버에 연결**에서 *serverName*.database.windows.net과 같이 정규화된 서버 이름을 지정합니다. Azure에서 이 서버 이름은 영숫자로 구성되었으며 자동으로 생성된 문자열입니다.

3.  **SQL Server 인증**을 선택합니다.

4.  **로그인** 상자에 서버를 만들 때 포털에서 지정한 SQL Server 관리자 로그인을 입력합니다.

5.  **암호** 상자에 서버를 만들 때 포털에서 지정한 암호를 입력합니다.

8.  **연결**을 클릭하여 연결합니다.

최신 업데이트가 적용된 SQL Server 2014 SSMS는 Azure SQL 데이터베이스 만들기 및 수정과 같은 작업을 지원합니다. Transact-SQL 문을 사용하여 이러한 작업을 수행할 수도 있습니다. 아래 단계에서는 이러한 문의 예를 제공합니다. 지원되는 명령에 대한 세부 정보를 포함하여 SQL 데이터베이스에서의 Transact-SQL 사용에 대한 자세한 내용은 [Transact-SQL 참조(SQL 데이터베이스)](http://msdn.microsoft.com/library/bb510741.aspx)를 참조하세요.

## Azure SQL 데이터베이스 만들기 및 관리

**master** 데이터베이스에 연결되어 있는 동안에는 서버에 새 데이터베이스를 만들고 기존 데이터베이스를 수정하거나 삭제할 수 있습니다. 아래 단계에서는 Management Studio를 통해 여러 일반 데이터베이스 관리 작업을 수행하는 방법에 대해 설명합니다. 이러한 작업을 수행하려면 서버를 설치할 때 만든 서버 수준 보안 주체 로그인으로 **master** 데이터베이스에 연결되어 있어야 합니다.

Management Studio에서 쿼리 창을 열려면 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

-   새 데이터베이스를 만들려면 **CREATE DATABASE** 문을 사용하세요. 자세한 내용은 [CREATE DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요. 아래 문은 **myTestDB**라는 데이터베이스를 새로 만들고 기본 최대 크기가 250GB인 Standard S0 Edition 데이터베이스로 지정합니다.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

**실행**을 클릭하여 쿼리를 실행합니다.

-   예를 들어 데이터베이스의 이름 및 버전을 변경하려는 경우처럼 기존 데이터베이스를 수정하려면 **ALTER DATABASE** 문을 사용합니다. 자세한 내용은 [ALTER DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms174269.aspx)를 참조하세요. 아래 문은 이전 단계에서 만든 데이터베이스 버전을 Standard S1로 변경하도록 수정합니다.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   기존 데이터베이스를 삭제하려면 **DROP DATABASE** 문을 사용하세요. 자세한 내용은 [DROP DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms178613.aspx)를 참조하세요. 아래 문은 **myTestDB** 데이터베이스를 삭제하지만 다음 단계에서 로그인을 만드는 데 사용되므로 지금 삭제하지는 마세요.

        DROP DATABASE myTestBase;

-   master 데이터베이스에는 모든 데이터베이스에 관한 세부 정보를 볼 수 있는 **sys.databases** 뷰가 있습니다. 기존 데이터베이스를 모두 보려면 다음 문을 실행하세요.

        SELECT * FROM sys.databases;

-   SQL 데이터베이스에서 **USE** 문은 데이터베이스 간 전환을 지원하지 않습니다. 대신 대상 데이터베이스에 직접 연결해야 합니다.

>[AZURE.NOTE]데이터베이스를 만들거나 수정하는 많은 Transact-SQL 문은 자신의 일괄 처리 안에서 실행되어야 하며 다른 Transact-SQL 문과 함께 그룹화될 수 없습니다. 자세한 내용은 위에 나열된 링크에서 사용할 수 있는 문 관련 정보를 참조하세요.

## 로그인 만들기 및 관리

**master** 데이터베이스는 로그인을 추적하여 데이터베이스 또는 다른 로그인을 만들 수 있는 권한이 있는 로그인을 추적합니다. 서버를 설치할 때 만든 서버 수준 보안 주체 로그인으로 **master** 데이터베이스에 연결하여 로그인을 관리하세요. 전체 서버에서 로그인을 관리할 master 데이터베이스에 대해 **CREATE LOGIN**, **ALTER LOGIN** 또는 **DROP LOGIN** 문을 사용하여 쿼리를 실행할 수 있습니다. 자세한 내용은 [SQL 데이터베이스에서 데이터베이스 및 로그인 관리](http://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.


-   서버 수준 로그인을 새로 만들려면 **CREATE LOGIN** 문을 사용하세요. 자세한 내용은 [CREATE LOGIN(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms189751.aspx)을 참조하세요. 아래 문은 **login1**이라는 새 로그인을 만듭니다. **password1**을 원하는 암호로 바꾸세요.

        CREATE LOGIN login1 WITH password='password1';

-   데이터베이스 수준의 권한을 부여하려면 **CREATE USER** 문을 사용하세요. 모든 로그인은 **master** 데이터베이스에서 만들어져야 하지만, 다른 데이터베이스에 연결하기 위한 로그인은 해당 데이터베이스에서 **CREATE USER** 문을 사용하여 데이터베이스 수준의 권한을 부여 받아야 합니다. 자세한 내용은 [CREATE USER(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.

-   **myTestDB**라는 데이터베이스에 login1 권한을 부여하려면 다음 단계를 완료하세요.

 1.  개체 탐색기를 새로 고쳐 방금 만든 **myTestDB** 데이터베이스를 표시하려면 개체 탐색기에서 서버 이름을 마우스 오른쪽 단추로 클릭한 후 **새로 고침**을 클릭합니다.  

     연결을 닫았으면 파일 메뉴에서 **개체 탐색기 연결**을 선택하여 다시 연결할 수 있습니다.

 2. **myTestDB** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

    3.  myTestDB 데이터베이스에 대해 다음 문을 실행하여 서버 수준 로그인인 **login1**에 해당하는 데이터베이스 사용자를 **login1User**라는 이름으로 만듭니다.

            CREATE USER login1User FROM LOGIN login1;

-   데이터베이스에서 적절한 수준의 권한을 사용자 계정에 부여하려면 **sp\_addrolemember** 저장 프로시저를 사용하세요. 자세한 내용은 [sp\_addrolemember(Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx)를 참조하세요. 아래 문은 **db\_datareader** 역할에 **login1User**를 추가하여 데이터베이스에 **login1User** 읽기 전용 권한을 부여합니다.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   예를 들어 로그인 암호를 변경하려는 경우처럼 기존 로그인을 수정하려면 **ALTER LOGIN** 문을 사용하세요. 자세한 내용은 [ALTER LOGIN(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms189828.aspx)을 참조하세요. **ALTER LOGIN** 문은 **master** 데이터베이스에 대해 실행되어야 합니다. 해당 데이터베이스에 연결되어 있는 쿼리 창으로 전환합니다.

    아래 문은 암호를 다시 설정하도록 **login1** 로그인을 수정합니다. **newPassword**는 원하는 암호로 바꾸고, **oldPassword**는 현재 로그인 암호로 바꾸세요.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   기존 로그인을 삭제하려면 **DROP LOGIN** 문을 사용하세요. 서버 수준에서 로그인을 삭제하면 연결된 데이터베이스 사용자 계정도 삭제됩니다. 자세한 내용은 [DROP DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms178613.aspx)를 참조하세요. **DROP LOGIN** 문은 **master** 데이터베이스에 대해 실행되어야 합니다. 아래 문은 **login1** 로그인을 삭제합니다.

        DROP LOGIN login1;

-   master 데이터베이스에는 로그인을 볼 수 있는 **sys.sql\_logins** 뷰가 있습니다. 기존 로그인을 모두 보려면 다음 문을 실행하세요.

        SELECT * FROM sys.sql_logins;

## 동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링</h2>

SQL 데이터베이스는 개별 데이터베이스를 모니터링할 수 있는 여러 동적 관리 뷰를 지원합니다. 아래는 이러한 뷰를 통해 검색할 수 있는 모니터 데이터 유형의 몇몇 예입니다. 전체 세부 정보 및 그 밖의 사용 예는 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링](https://msdn.microsoft.com/library/azure/ff394114.aspx)을 참조하세요.

-   동적 관리 뷰를 쿼리하려면 **VIEW DATABASE STATE** 권한이 있어야 합니다. 특정 데이터베이스 사용자에게 **VIEW DATABASE STATE** 권한을 부여하려면 서버 수준 보안 주체 로그인으로 관리하려는 데이터베이스에 연결하고 그 데이터베이스에 대해 다음 문을 실행하세요.

        GRANT VIEW DATABASE STATE TO login1User;

-   **sys.dm\_db\_partition\_stats** 뷰를 사용하여 데이터베이스 크기를 계산합니다. **sys.dm\_db\_partition\_stats** 뷰는 데이터베이스에 있는 모든 파티션의 페이지 및 행 개수 정보를 반환하며, 데이터베이스 크기를 계산할 수 있습니다. 다음 쿼리는 데이터베이스 크기(MB)를 반환합니다.

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   현재 사용자 연결 및 데이터베이스와 연결된 내부 작업에 대한 정보를 검색하려면 **sys.dm\_exec\_connections** 및 **sys.dm\_exec\_sessions** 뷰를 사용하세요. 다음 쿼리는 현재 연결에 대한 정보를 반환합니다.

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   캐시된 쿼리 계획의 성능 통계를 집계하려면 **sys.dm\_exec\_query\_stats** 뷰를 사용하세요. 다음 쿼리는 평균 CPU 시간별로 상위 5개의 쿼리에 대한 정보를 반환합니다.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
 
 

<!---HONumber=Oct15_HO3-->