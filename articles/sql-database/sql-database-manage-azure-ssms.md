---
title: SSMS를 사용하여 SQL 데이터베이스 관리 | Microsoft Docs
description: SQL Server Management Studio를 사용하여 SQL 데이터베이스 서버 및 데이터베이스를 관리하는 방법에 대해 알아봅니다.
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2016
ms.author: sstein

---
# SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리
> [!div class="op_single_selector"]
> * [Azure 포털](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-command-line-tools.md)
> 
> 

SSMS(SQL Server Management Studio)를 사용하여 Azure SQL 데이터베이스 서버 및 데이터베이스를 관리할 수 있습니다. 이 항목에서는 SSMS로 수행하는 일반적인 작업에 대해 설명합니다. 시작하기 전에 Azure SQL 데이터베이스에서 서버 및 데이터베이스가 생성되어 있어야 합니다. 자세한 내용은 [첫 번째 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 및 [SSMS를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.

Azure SQL 데이터베이스를 사용하여 작업할 경우 최신 버전의 SSMS를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> Azure 및 SQL 데이터베이스에 대한 최신 업데이트에서 작동되도록 지속해서 개선되고 있으므로 항상 최신 버전의 SSMS를 사용하세요. 최신 버전을 다운로드하려면 [SQL Server Management Studio 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.
> 
> 

## Azure SQL 데이터베이스 만들기 및 관리
**master** 데이터베이스에 연결되어 있는 동안에는 서버에 데이터베이스를 만들고 기존 데이터베이스를 수정하거나 삭제할 수 있습니다. 다음 단계에서는 Management Studio를 통해 여러 일반 데이터베이스 관리 작업을 수행하는 방법에 대해 설명합니다. 이러한 작업을 수행하려면 서버를 설치할 때 만든 서버 수준 보안 주체 로그인으로 **master** 데이터베이스에 연결되어 있어야 합니다.

Management Studio에서 쿼리 창을 열려면 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

* 데이터베이스를 만들려면 **CREATE DATABASE** 문을 사용하세요. 자세한 내용은 [CREATE DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요. 다음 문은 **myTestDB**라는 데이터베이스를 만들고 기본 최대 크기가 250GB인 Standard S0 Edition 데이터베이스로 지정합니다.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

**실행**을 클릭하여 쿼리를 실행합니다.

* 예를 들어 데이터베이스의 이름 및 버전을 변경하려는 경우처럼 기존 데이터베이스를 수정하려면 **ALTER DATABASE** 문을 사용합니다. 자세한 내용은 [ALTER DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms174269.aspx)를 참조하세요. 다음 문은 이전 단계에서 만든 데이터베이스 버전을 Standard S1로 변경하도록 수정합니다.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* 기존 데이터베이스를 삭제하려면 **DROP DATABASE** 문을 사용하세요. 자세한 내용은 [DROP DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms178613.aspx)를 참조하세요. 다음 문은 **myTestDB** 데이터베이스를 삭제하지만 다음 단계에서 로그인을 만드는 데 사용되므로 지금 삭제하지는 마세요.
  
      DROP DATABASE myTestBase;
* master 데이터베이스에는 모든 데이터베이스에 관한 세부 정보를 볼 수 있는 **sys.databases** 뷰가 있습니다. 기존 데이터베이스를 모두 보려면 다음 문을 실행하세요.
  
      SELECT * FROM sys.databases;
* SQL 데이터베이스에서 **USE** 문은 데이터베이스 간 전환을 지원하지 않습니다. 대신 대상 데이터베이스에 직접 연결해야 합니다.

> [!NOTE]
> 데이터베이스를 만들거나 수정하는 많은 Transact-SQL 문은 자신의 일괄 처리 안에서 실행되어야 하며 다른 Transact-SQL 문과 함께 그룹화될 수 없습니다. 자세한 내용은 문 관련 정보를 참조하세요.
> 
> 

## 로그인 만들기 및 관리
**master** 데이터베이스는 데이터베이스 또는 다른 로그인을 만들 수 있는 권한이 있는 로그인을 포함합니다. 서버를 설치할 때 만든 서버 수준 보안 주체 로그인으로 **master** 데이터베이스에 연결하여 로그인을 관리하세요. 전체 서버에서 로그인을 관리할 master 데이터베이스에 대해 **CREATE LOGIN**, **ALTER LOGIN** 또는 **DROP LOGIN** 문을 사용하여 쿼리를 실행할 수 있습니다. 자세한 내용은 [SQL 데이터베이스에서 데이터베이스 및 로그인 관리](http://msdn.microsoft.com/library/azure/ee336235.aspx)를 참조하세요.

* 서버 수준 로그인을 만들려면 **CREATE LOGIN** 문을 사용하세요. 자세한 내용은 [CREATE LOGIN(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms189751.aspx)을 참조하세요. 다음 문은 **login1**이라는 로그인을 만듭니다. **password1**을 원하는 암호로 바꾸세요.
  
      CREATE LOGIN login1 WITH password='password1';
* 데이터베이스 수준의 권한을 부여하려면 **CREATE USER** 문을 사용하세요. 모든 로그인은 **마스터** 데이터베이스에서 만들어야 합니다. 다른 데이터베이스에 연결하기 위한 로그인은 해당 데이터베이스에서 **CREATE USER** 문을 사용하여 데이터베이스 수준의 권한을 부여받아야 합니다. 자세한 내용은 [CREATE USER(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.
* **myTestDB**라는 데이터베이스에 login1 권한을 부여하려면 다음 단계를 완료하십시오.
  
  1. 개체 탐색기를 새로 고쳐 만든 **myTestDB** 데이터베이스를 표시하려면 개체 탐색기에서 서버 이름을 마우스 오른쪽 단추로 클릭한 후 **새로 고침**을 클릭합니다.
     
     연결을 닫았으면 파일 메뉴에서 **개체 탐색기 연결**을 선택하여 다시 연결할 수 있습니다.
  2. **myTestDB** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.
  3. myTestDB 데이터베이스에 대해 다음 문을 실행하여 서버 수준 로그인인 **login1**에 해당하는 데이터베이스 사용자를 **login1User**라는 이름으로 만듭니다.
     
         CREATE USER login1User FROM LOGIN login1;
* 데이터베이스에서 적절한 수준의 권한을 사용자 계정에 부여하려면 **sp\_addrolemember** 저장 프로시저를 사용하세요. 자세한 내용은 [sp\_addrolemember(Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx)를 참조하세요. 다음 문은 **db\_datareader** 역할에 **login1User**를 추가하여 데이터베이스에 **login1User** 읽기 전용 권한을 부여합니다.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* 예를 들어 로그인 암호를 변경하려는 경우처럼 기존 로그인을 수정하려면 **ALTER LOGIN** 문을 사용하세요. 자세한 내용은 [ALTER LOGIN(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms189828.aspx)을 참조하세요. **ALTER LOGIN** 문은 **master** 데이터베이스에 대해 실행되어야 합니다. 해당 데이터베이스에 연결되어 있는 쿼리 창으로 전환합니다. 다음 문은 암호를 다시 설정하도록 **login1** 로그인을 수정합니다. **newPassword**는 원하는 암호로 바꾸고, **oldPassword**는 현재 로그인 암호로 바꾸세요.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* 기존 로그인을 삭제하려면 **DROP LOGIN** 문을 사용하십시오. 서버 수준에서 로그인을 삭제하면 연결된 데이터베이스 사용자 계정도 삭제됩니다. 자세한 내용은 [DROP DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/ms178613.aspx)를 참조하세요. **DROP LOGIN** 문은 **master** 데이터베이스에 대해 실행되어야 합니다. 다음 문은 **login1** 로그인을 삭제합니다.
  
      DROP LOGIN login1;
* master 데이터베이스에는 로그인을 볼 수 있는 **sys.sql\_logins** 뷰가 있습니다. 기존 로그인을 모두 보려면 다음 문을 실행하세요.
  
      SELECT * FROM sys.sql_logins;

## 동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링
SQL 데이터베이스는 개별 데이터베이스를 모니터링할 수 있는 여러 동적 관리 뷰를 지원합니다. 다음은 이러한 뷰를 통해 검색할 수 있는 모니터 데이터 형식의 몇몇 예입니다. 전체 세부 정보 및 그 밖의 사용 예는 [동적 관리 뷰를 사용하여 SQL 데이터베이스 모니터링](https://msdn.microsoft.com/library/azure/ff394114.aspx)을 참조하세요.

* 동적 관리 뷰를 쿼리하려면 **VIEW DATABASE STATE** 권한이 있어야 합니다. 특정 데이터베이스 사용자에게 **VIEW DATABASE STATE** 권한을 부여하려면 데이터베이스에 연결하고 그 데이터베이스에 대해 다음 문을 실행하세요.
  
      GRANT VIEW DATABASE STATE TO login1User;
* **sys.dm\_db\_partition\_stats** 뷰를 사용하여 데이터베이스 크기를 계산합니다. **sys.dm\_db\_partition\_stats** 뷰는 데이터베이스에 있는 모든 파티션의 페이지 및 행 개수 정보를 반환하며, 데이터베이스 크기를 계산할 수 있습니다. 다음 쿼리는 데이터베이스 크기(MB)를 반환합니다.
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* 현재 사용자 연결 및 데이터베이스와 연결된 내부 작업에 대한 정보를 검색하려면 **sys.dm\_exec\_connections** 및 **sys.dm\_exec\_sessions** 뷰를 사용하세요. 다음 쿼리는 현재 연결에 대한 정보를 반환합니다.
  
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
* 캐시된 쿼리 계획의 성능 통계를 집계하려면 **sys.dm\_exec\_query\_stats** 뷰를 사용하세요. 다음 쿼리는 평균 CPU 시간별로 상위 5개의 쿼리에 대한 정보를 반환합니다.
  
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

<!---HONumber=AcomDC_0824_2016-->