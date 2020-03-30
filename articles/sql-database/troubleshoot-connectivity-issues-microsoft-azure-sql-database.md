---
title: SQL 데이터베이스에 대한 일반적인 연결 문제 해결
description: Azure SQL Database 연결 문제 해결 및 다른 SQL Database 특정 문제를 해결하는 단계를 제공합니다.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208777"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Microsoft Azure SQL Database 연결 문제 및 기타 오류 문제 해결

Azure SQL Database에 대한 연결이 실패하면 오류 메시지가 표시됩니다. 이러한 연결 문제는 Azure SQL Database 재구성, 방화벽 설정, 연결 시간 시간 설정, 잘못된 로그인 정보 또는 [응용 프로그램 디자인](sql-database-develop-overview.md) 프로세스 중에 모범 사례 및 디자인 지침을 적용하지 못하여 발생할 수 있습니다. 또한 일부 Azure SQL Database 리소스의 최대 제한에 도달하면 Azure SQL Database에 연결할 수 없습니다.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>일시적인 오류 오류 메시지(40197, 40613 등)

Azure 인프라에 는 SQL Database 서비스에 과도한 워크로드 부하가 발생하는 경우 서버를 동적으로 다시 구성 하는 기능이 있습니다.  이러한 동적인 동작으로 인해 클라이언트 프로그램의 SQL Database에 대한 연결이 손실될 수 있습니다. 이러한 종류의 오류 상황을 *일시적 장애*라고 합니다. 데이터베이스 재구성 이벤트는 계획된 이벤트(예: 소프트웨어 업그레이드) 또는 계획되지 않은 이벤트(예: 프로세스 충돌 또는 부하 분산) 때문에 발생합니다. 대부분의 다시 구성 이벤트는 보통 일시적으로 발생하며 최대 60초 이내에 완료됩니다. 그러나 큰 트랜잭션으로 인해 복구가 오래 실행되는 등 이러한 이벤트가 완료되는 데 시간이 더 오래 걸리는 경우도 있습니다. 다음 표에는 SQL Database에 연결할 때 응용 프로그램에서 받을 수 있는 다양한 일시적인 오류가 나열되어 있습니다.

### <a name="list-of-transient-fault-error-codes"></a>일시적인 오류 오류 코드 목록


| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 4060 |16 |로그인에서 요청된 데이터베이스 "%.&#x2a;ls"을(를) 열 수 없습니다. 로그인이 실패했습니다. 자세한 내용은 [오류 4000 ~ 4999를](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) 참조하십시오.|
| 40197 |17 |서비스에서 요청을 처리하는 오류가 발생했습니다. 나중에 다시 시도하세요. 오류 코드 %d.<br/><br/>소프트웨어 또는 하드웨어 업그레이드, 하드웨어 오류 또는 기타 장애 조치 문제로 인해 서비스가 종료되는 경우 이 오류가 발생합니다. 오류 40197 의 메시지 내에 포함된 오류 코드(%d)는 발생한 오류 또는 장애 조치의 종류에 대한 추가 정보를 제공합니다. 오류 40197 메시지 내에 포함된 오류 코드의 일부 예제는 40020, 40143, 40166 및 40540입니다.<br/><br/>SQL Database 서버에 다시 연결하면 데이터베이스의 정상 복사본으로 자동 연결됩니다. 애플리케이션은 오류 40197을 포착하고 문제 해결을 위해 메시지 내에 포함된 오류 코드(%d)를 로그하고 리소스가 사용 가능하고 연결이 다시 설정될 때까지 SQL Database에 다시 접속을 시도해야 합니다. 자세한 내용은 [과도 오류를](sql-database-connectivity-issues.md#transient-errors-transient-faults)참조하십시오.|
| 40501 |20 |서비스가 현재 사용 중입니다. 10초 후 요청을 다시 시도하십시오. 인시던트 ID: %ls. 코드: %d. 자세한 내용은 다음을 참조하세요. <br/>&bull;&nbsp; [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |서버의 데이터베이스 '%.&#x2a;ls' '%.&#x2a;ls'을(를) 사용할 수 없습니다. 나중에 연결을 다시 시도하십시오. 문제가 지속되면 고객 지원 서비스에 문의하고 세션 추적 ID '%.&#x2a;ls'을(를) 제공하십시오.<br/><br/> 이 오류는 데이터베이스에 기존 DAC(전용 관리자 연결)가 이미 설정된 경우 발생할 수 있습니다. 자세한 내용은 [과도 오류를](sql-database-connectivity-issues.md#transient-errors-transient-faults)참조하십시오.|
| 49918 |16 |요청을 처리할 수 없습니다. 요청을 처리할 리소스가 부족합니다.<br/><br/>서비스가 현재 사용 중입니다. 요청을 나중에 다시 시도하세요. 자세한 내용은 다음을 참조하세요. <br/>&bull;&nbsp; [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |요청을 만들거나 업데이트하는 처리를 할 수 없습니다. 구독 "%ld"에 대해 진행 중인 작업을 너무 많이 만들거나 업데이트합니다.<br/><br/>서비스가 구독 또는 서버에 대한 여러 요청을 만들거나 업데이트하는 처리로 사용 중입니다. 요청은 현재 리소스 최적화에 대해 차단됩니다. 보류 중인 작업에 대해 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx)를 쿼리합니다. 만들기 또는 업데이트를 보류 중인 요청이 완료되거나 보류 중인 요청 중 하나를 삭제할 때까지 대기하고 나중에 요청을 다시 시도합니다. 자세한 내용은 다음을 참조하세요. <br/>&bull;&nbsp; [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |요청을 처리할 수 없습니다. 구독 "%ld"에 대해 진행 중인 작업을 너무 많습니다.<br/><br/>서비스가 구독에 대한 여러 요청을 처리하는 데 사용 중입니다. 요청은 현재 리소스 최적화에 대해 차단됩니다. 작업 상태에 대해 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx)를 쿼리합니다. 보류 중인 요청이 완료되거나 보류 중인 요청 중 하나를 삭제할 때까지 대기하고 나중에 요청을 다시 시도합니다. 자세한 내용은 다음을 참조하세요. <br/>&bull;&nbsp; [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'에서 긴 대기로 인해 읽기 보조에 대한 로그인에 실패했습니다. 행 버전이 복제본이 재활용될 때 처리 중이었던 트랜잭션에 대해 누락되었기 때문에 로그인에 복제본을 사용할 수 없습니다. 주 복제본에서 활성 트랜잭션을 롤백하거나 커밋하여 문제를 해결할 수 있습니다. 주 복제본에서 긴 쓰기 트랜잭션을 방지하여 이 조건의 발생을 최소화할 수 있습니다. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>일시적인 연결 문제를 해결하는 단계

1. [Microsoft Azure 서비스 대시보드](https://azure.microsoft.com/status)에서 애플리케이션이 오류를 보고한 시간 동안 발생한 알려진 서비스 중단을 확인합니다.
2. Azure SQL Database와 같이 클라우드 서비스에 연결하는 애플리케이션에서는 주기적으로 다시 구성 이벤트가 발생하므로, 이러한 이벤트를 사용자에게 애플리케이션 오류로 표시하는 대신 해당 오류를 처리하는 다시 시도 논리를 구현해야 합니다. 
3. 데이터베이스가 리소스 한계에 도달하면 일시적인 연결 문제가 발생한 것처럼 보일 수 있습니다. [리소스 한도](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached)를 참조하세요.
4. 연결 문제가 계속 발생하거나 애플리케이션에서 오류가 발생하는 기간이 60초를 초과하는 경우 또는 특정일에 오류가 여러 번 발생하는 경우에는 **Azure 지원** 사이트에서 [지원 받기](https://azure.microsoft.com/support/options)를 선택하여 Azure 지원 요청을 접수합니다.

#### <a name="implementing-retry-logic"></a>재시도 논리 구현
자체 해결을 위한 일시적 오류 시간이 지난 후 연결을 다시 시도할 수 있도록 클라이언트 프로그램에 재시도 논리가 있는 것이 좋습니다.  첫 번째 재시도 전에 5초간 지연하는 것이 좋습니다. 5초보다 짧은 지연 후 재시도는 클라우드 서비스에 많은 위험이 있습니다. 각 후속 재시도에 대해 지연 시간은 최대 60초까지 기하급수적으로 증가해야 합니다.

다시 시도 논리의 코드 예제는 다음을 참조하십시오.
- [ADO.NET 사용하여 SQL에 복원력 있게 연결](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [PHP로 SQL에 탄력적으로 연결](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

응용 프로그램 검토에서 일시적인 오류 처리에 대한 자세한 내용은
* [SQL 데이터베이스에 대한 일시적인 연결 오류 문제 해결](sql-database-connectivity-issues.md)

ADO.NET을 사용하는 클라이언트에 대한 *차단 기간* 의 설명은 [SQL Server 연결 풀링(ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)에서 사용 가능합니다.

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>SQL Database 서버에 대한 연결을 설정하는 동안 네트워크 관련 또는 인스턴스별 오류가 발생했습니다.

응용 프로그램이 서버에 연결할 수 없는 경우 문제가 발생합니다.

이 문제를 해결하려면 단계 섹션의 단계(제시된 [순서대로)를 시도하십시오.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>서버/인스턴스를 찾을 수 없거나 액세스할 수 없습니다(오류 26, 40, 10053).

#### <a name="error-26-error-locating-server-specified"></a>오류 26: 서버 오류 찾기 지정

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>오류 40: 서버에 대한 연결을 열 수 없습니다.

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>오류 10053: 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다.

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

이러한 문제는 응용 프로그램이 서버에 연결할 수 없는 경우에 발생합니다.

이러한 문제를 해결하려면 단계 섹션의 단계(제시된 [순서대로)를 시도하여 일반적인 연결 문제를 해결합니다.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>방화벽 문제로 인해 서버에 연결할 수 없습니다.

### <a name="error-40615-cannot-connect-to--servername-"></a>오류 40615: < 서버 이름 > 연결할 수 없습니다.

이 문제를 해결하려면 [Azure 포털을 통해 SQL Database에서 방화벽 설정을 구성합니다.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>오류 5: < 서버 이름 > 연결할 수 없습니다.

이 문제를 해결하려면 클라이언트와 인터넷 사이의 모든 방화벽에서 포트 1433이 아웃바운드 연결을 위해 열려 있는지 확인합니다.

자세한 내용은 [SQL Server 액세스를 허용하도록 Windows 방화벽 구성을](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)참조하십시오.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>서버에 로그인할 수 없음(오류 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>사용자 '< 사용자 이름 >'에 대한 로그인이 실패했습니다.

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

이 문제를 해결하려면 서비스 관리자에게 문의하여 유효한 SQL Server 사용자 이름과 암호를 제공합니다.

일반적으로 서비스 관리자는 다음 단계를 사용하여 로그인 자격 증명을 추가할 수 있습니다.

1. SQL 서버 관리 스튜디오(SSMS)를 사용하여 서버에 로그인합니다.
2. 다음 SQL 쿼리를 실행하여 로그인 이름이 비활성화되어 있는지 확인합니다.

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. 해당 이름이 비활성화된 경우 다음 문을 사용하여 활성화합니다.

   ```sql
   Alter login <User name> enable
   ```

4. SQL 로그인 사용자 이름이 없는 경우 다음 단계를 수행하여 만듭니다.

   1. SSMS에서 **보안을** 두 번 클릭하여 확장합니다.
   2. 마우스 오른쪽 단추로 **로그인**을 클릭한 다음 **새 로그인**을 선택합니다.
   3. 자리 표시자가 있는 생성된 스크립트에서 다음 SQL 쿼리를 편집하고 실행합니다.

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. **데이터베이스**를 두 번 클릭합니다.
6. 사용자에게 권한을 부여할 데이터베이스를 선택합니다.
7. **보안**을 두 번 클릭합니다.
8. **사용자**를 마우스 오른쪽 단추로 클릭한 다음 **새 사용자를**선택합니다.
9. 자리 표시자가 있는 생성된 스크립트에서 다음 SQL 쿼리를 편집하고 실행합니다.

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > 특정 사용자를 `sp_addrolemember` 특정 데이터베이스 역할에 매핑하는 데도 사용할 수 있습니다.

자세한 내용은 [Azure SQL Database의 데이터베이스 및 로그인 관리를](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)참조하십시오.

## <a name="connection-timeout-expired-errors"></a>연결 시간 시간 만료 오류

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException(0x80131904): 연결 시간 시간이 만료됨

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException(0x80131904): 시간 시간이 만료됨

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.Entity예외: 열기에서 기본 공급자가 실패했습니다.

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>< 서버 이름 > 연결할 수 없습니다.

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

이러한 예외는 연결 또는 쿼리 문제로 인해 발생할 수 있습니다. 이 오류가 연결 문제로 인해 발생했는지 확인하려면 [연결 문제로 인해 오류가 발생하는지 확인을](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)참조하십시오.

응용 프로그램이 서버에 연결할 수 없기 때문에 연결 시간 시간이 발생합니다. 이 문제를 해결하려면 단계 섹션의 단계(제시된 [순서대로)를 시도하십시오.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>리소스 관리 오류

### <a name="error-10928-resource-id-d"></a>오류 10928: 리소스 ID: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

이 문제를 해결하려면 다음 방법 중 하나를 시도해 보십시오.

* 장기 실행 쿼리가 있는지 확인합니다.

  > [!NOTE]
  > 이것은 문제를 해결하지 못할 수 있는 최소한의 접근 방식입니다.

1. 다음 SQL 쿼리를 실행하여 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 보기를 확인하여 차단 요청을 확인합니다.

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 헤드 블로커의 **입력 버퍼를** 결정합니다.
3. 헤드 차단기 쿼리를 조정합니다.

   자세한 문제 해결 절차는 [클라우드에서 쿼리가 잘 실행되고 있습니까?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

데이터베이스가 차단 및 장기 실행 쿼리를 해결에도 불구하고 지속적으로 한계에 도달하면 더 많은 리소스 [에디션이](https://azure.microsoft.com/pricing/details/sql-database/)있는 버전으로 업그레이드하는 것이 좋습니다.

동적 관리 보기에 대한 자세한 내용은 [시스템 동적 관리 보기를](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)참조하십시오.

데이터베이스 제한에 대한 자세한 내용은 [Azure SQL Database 서버의 SQL Database 리소스 제한을](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)참조하십시오.

### <a name="error-10929-resource-id-1"></a>오류 10929: 리소스 ID: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>오류 40501: 서비스가 현재 사용 중입니다.

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

이는 리소스 제한을 초과하고 있음을 나타내는 엔진 제한 오류입니다.

리소스 제한에 대한 자세한 내용은 [데이터베이스 서버 리소스 제한을](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)참조하십시오.

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>오류 40544: 데이터베이스의 크기 할당량에 도달했습니다.

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

이 오류는 데이터베이스가 크기 할당량에 도달하면 발생합니다.

다음 단계는 문제를 해결하거나 추가 옵션을 제공하는 데 도움이 될 수 있습니다.

1. Azure 포털에서 대시보드를 사용하여 데이터베이스의 현재 크기를 확인합니다.

   > [!NOTE]
   > 가장 많은 공간을 소비하는 테이블을 식별하여 정리할 수 있는 잠재적인 후보를 확인하려면 다음 SQL 쿼리를 실행합니다.

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. 현재 크기가 에디션에서 지원되는 최대 크기를 초과하지 않는 경우 ALTER DATABASE를 사용하여 MAXSIZE 설정을 늘릴 수 있습니다.
3. 데이터베이스가 에디션에 대해 지원되는 최대 크기를 이미 초과한 경우 다음 단계 중 하나 이상을 시도해 보십시오.

   * 정상적인 데이터베이스 정리 작업을 수행합니다. 예를 들어, SSIS(SQL Server 통합 서비스) 또는 대량 복사 프로그램(bcp) 유틸리티를 사용하여 원치 않는 데이터를 정리하거나 데이터를 이동합니다.
   * 데이터를 분할 또는 삭제하거나 인덱스를 삭제하거나 가능한 해결 방법에 대한 설명서를 참조하십시오.
   * 데이터베이스 크기 조정의 경우 [단일 데이터베이스 리소스 크기 조정](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) 및 [탄력적 풀 리소스 확장](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)을 참조하십시오.

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>오류 40549: 장기 실행 트랜잭션이 있기 때문에 세션이 종료됩니다.

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

이 오류가 반복적으로 발생하면 다음 단계를 수행하여 문제를 해결해 보십시오.

1. sys.dm_exec_requests 보기를 확인하여 total_elapsed_time 열에 대한 값이 높은 열려 있는 세션을 확인합니다. 다음 SQL 스크립트를 실행하여 이 검사를 수행합니다.

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 장기 실행 쿼리에 대한 입력 버퍼를 결정합니다.
3. 쿼리를 조정합니다.

쿼리 일괄 처리도 고려하십시오. 일괄 처리에 대한 자세한 내용은 [일괄 처리를 사용하여 SQL Database 응용 프로그램 성능을 향상시키는 방법을](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)참조하십시오.

자세한 문제 해결 절차는 [클라우드에서 쿼리가 잘 실행되고 있습니까?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>오류 40551: 과도한 TEMPDB 사용으로 인해 세션이 종료되었습니다.

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

이 문제를 해결하려면 아래 단계를 수행합니다.

1. 쿼리를 변경하여 임시 테이블스페이스 사용량을 줄입니다.
2. 임시 개체가 더 이상 필요하지 않은 후 삭제합니다.
3. 테이블을 트렁킨다거나 사용하지 않는 테이블을 제거합니다.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>오류 40552: 과도한 트랜잭션 로그 공간 사용으로 인해 세션이 종료되었습니다.

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

이 문제를 해결하려면 다음 방법을 사용해 보세요.

* 삽입, 업데이트 또는 삭제 작업으로 인해 문제가 발생할 수 있습니다.
일괄 처리를 구현하거나 여러 개의 작은 트랜잭션으로 분할하여 즉시 작동하는 행 수를 줄이십시오.
* 인덱스 다시 작성 작업으로 인해 문제가 발생할 수 있습니다. 이 문제를 해결하려면 테이블에 영향을 받는 행 수 * (바이트 + 80으로 업데이트된 필드의 평균 크기)< 2기가바이트(GB)를 확인하십시오.

  > [!NOTE]
  > 인덱스 다시 작성의 경우 업데이트된 필드의 평균 크기를 평균 인덱스 크기로 대체해야 합니다.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>오류 40553: 과도한 메모리 사용으로 인해 세션이 종료되었습니다.

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

이 문제를 해결하려면 쿼리를 최적화하십시오.

자세한 문제 해결 절차는 [클라우드에서 쿼리가 잘 실행되고 있습니까?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="table-of-additional-resource-governance-error-messages"></a>추가 리소스 거버넌스 오류 메시지 테이블

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 10928 |20 |리소스 ID: %d입니다. 데이터베이스에 대한 %s 제한이 %d이며 이 제한에 도달했습니다. 자세한 내용은 [단일 데이터베이스 및 풀링된 데이터베이스에 대한 SQL Database 리소스 한도](sql-database-resource-limits-database-server.md)를 참조하세요.<br/><br/>리소스 ID는 제한에 도달한 리소스를 나타냅니다. 작업자 스레드의 경우 리소스 ID = 1입니다. 세션의 경우 리소스 ID = 2입니다.<br/><br/>이 오류 및 문제를 해결하는 방법에 대한 자세한 내용은 다음을 참조하세요. <br/>&bull;&nbsp; [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |리소스 ID: %d입니다. %s의 최소 보장은 %d이며, 최대 한도는 %d이고, 해당 데이터베이스의 현재 사용량은 %d입니다. 하지만 현재 서버 사용량이 너무 많아 해당 데이터베이스에 대해 %d 이상의 요청을 지원할 수 없습니다. 리소스 ID는 제한에 도달한 리소스를 나타냅니다. 작업자 스레드의 경우 리소스 ID = 1입니다. 세션의 경우 리소스 ID = 2입니다. 자세한 내용은 다음을 참조하세요. <br/>&bull;&nbsp; [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md). <br/>그렇지 않은 경우 나중에 다시 시도하세요. |
| 40544 |20 |데이터베이스가 크기 할당량에 도달했습니다. 데이터를 분할 또는 삭제하거나 인덱스를 삭제하거나 가능한 해결 방법에 대한 설명서를 참조하십시오. 데이터베이스 크기 조정의 경우 [단일 데이터베이스 리소스 크기 조정](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하십시오.|
| 40549 |16 |트랜잭션을 오래 실행하여 세션이 종료됩니다. 트랜잭션을 줄여 보세요. 일괄 처리에 대한 자세한 내용은 [일괄 처리를 사용하여 SQL Database 응용 프로그램 성능을 향상시키는 방법을](sql-database-use-batching-to-improve-performance.md)참조하십시오.|
| 40550 |16 |잠금을 너무 많이 획득하여 세션이 종료되었습니다. 단일 트랜잭션에서 읽거나 수정하는 행 수를 줄여 보세요. 일괄 처리에 대한 자세한 내용은 [일괄 처리를 사용하여 SQL Database 응용 프로그램 성능을 향상시키는 방법을](sql-database-use-batching-to-improve-performance.md)참조하십시오.|
| 40551 |16 |과도한 `TEMPDB` 사용으로 인해 세션이 종료되었습니다. 쿼리를 수정하여 임시 테이블 공간 사용량을 줄여 보세요.<br/><br/>임시 개체를 사용하는 경우 세션에서 더 이상 필요하지 않을 때 임시 개체를 삭제하여 `TEMPDB` 데이터베이스의 공간을 절약하세요. SQL 데이터베이스의 tempdb 사용에 대한 자세한 내용은 [SQL 데이터베이스의 Tempdb 데이터베이스를](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)참조하십시오.|
| 40552 |16 |트랜잭션 로그 공간 사용량이 너무 많아 세션이 종료되었습니다. 단일 트랜잭션에서 수정하는 행 수를 줄여 보세요. 일괄 처리에 대한 자세한 내용은 [일괄 처리를 사용하여 SQL Database 응용 프로그램 성능을 향상시키는 방법을](sql-database-use-batching-to-improve-performance.md)참조하십시오.<br/><br/>`bcp.exe` 유틸리티 또는 `System.Data.SqlClient.SqlBulkCopy` 클래스를 사용하여 대량 삽입을 수행하는 경우 `-b batchsize` 또는 `BatchSize` 옵션을 사용하여 각 트랜잭션에서 서버로 복사된 행의 수를 제한하세요. `ALTER INDEX` 문을 사용하여 인덱스를 다시 작성하는 경우 `REBUILD WITH ONLINE = ON` 옵션을 사용하여 시도하십시오. vCore 구매 모델의 트랜잭션 로그 크기에 대한 자세한 내용은 다음을 참조하십시오. <br/>&bull;&nbsp; [단일 데이터베이스에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [탄력적 풀에 대한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |메모리 사용량이 너무 많아 세션이 종료되었습니다. 쿼리를 수정하여 처리할 행 수를 줄여 보세요.<br/><br/>Transact-SQL 코드에서 `ORDER BY` 및 `GROUP BY` 작업의 수를 줄이면 쿼리의 메모리 요구 사항이 줄어듭니다. 데이터베이스 크기 조정의 경우 [단일 데이터베이스 리소스 크기 조정](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하십시오.|

## <a name="elastic-pool-errors"></a>탄력적 풀 오류

다음 오류는 탄력적 풀 만들기 및 사용하기와 관련이 있습니다.

| 오류 코드 | 심각도 | 설명 | 정정 작업 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |탄력적 풀이 스토리지 용량 한도에 도달했습니다. 탄력적 풀의 스토리지 사용량은 (%d)MB를 초과할 수 없습니다. 탄력적 풀이 스토리지 용량 한도에 도달했을 때 데이터베이스에 데이터를 기록하려고 했습니다. 리소스 제한에 대한 자세한 내용은 다음을 참조하십시오. <br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [v탄성 풀에 대한 vCore 기반 제한.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> |가능하다면 탄력적 풀의 DTU를 늘리거나 탄력적 풀에 스토리지를 추가하여 스토리지 용량 한도를 늘리거나, 탄력적 풀에 있는 개별 데이터베이스에서 사용하는 스토리지를 줄이거나, 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. 탄력적 풀 확장의 경우 [탄력적 풀 리소스 확장을](sql-database-elastic-pool-scale.md)참조하십시오.|
| 10929 | 16 |%s의 최소 보장은 %d이며, 최대 한도는 %d이고, 해당 데이터베이스의 현재 사용량은 %d입니다. 하지만 현재 서버 사용량이 너무 많아 해당 데이터베이스에 대해 %d 이상의 요청을 지원할 수 없습니다. 리소스 제한에 대한 자세한 내용은 다음을 참조하십시오. <br/>&bull;&nbsp; [탄력적 풀에 대한 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [v탄성 풀에 대한 vCore 기반 제한.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> 그렇지 않은 경우 나중에 다시 시도하세요. 데이터베이스당 DTU/vCore 최솟값, 데이터베이스당 DTU/vCore 최댓값. 탄력적 풀에 있는 전체 데이터베이스의 동시 작업자(요청) 수 합계가 풀 한도를 초과하려고 했습니다. |가능하다면 탄력적 풀의 DTU 또는 vCore를 늘려 작업자 한도를 늘리거나 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 40844 | 16 |서버 '%ls'에 있는 데이터베이스 '%ls'은(는) 탄력적 풀에 포함된 '%ls' 버전 데이터베이스이며, 연속 복사 관계를 가질 수 없습니다.  |해당 없음 |
| 40857 | 16 |서버: '%ls'에서 탄력적 풀을 찾을 수 없음, 탄력적 풀 이름: '%ls'. 지정한 탄력적 풀이 지정한 서버에 존재하지 않습니다. | 유효한 탄력적 풀 이름을 입력하세요. |
| 40858 | 16 |탄력적 풀 '%ls'이(가) 서버 '%ls'에 이미 있습니다. 지정한 탄력적 풀이 지정한 SQL Database 서버에 이미 있습니다. | 새 탄력적 풀 이름을 입력하세요. |
| 40859 | 16 |탄력적 풀이 서비스 계층 '%ls'을(를) 지원하지 않습니다. 지정한 서비스 계층은 탄력적 풀 프로비저닝에 대해 지원되지 않습니다. |기본 서비스 계층을 사용하려면 오류를 수정하거나 서비스 계층을 빈 상태로 두세요. |
| 40860 | 16 |탄력적 풀 '%ls' 및 서비스 목표'%ls'의 조합은 유효하지 않습니다. 리소스 종류가 'ElasticPool'로 지정된 경우에만 탄력적 풀과 서비스 계층을 함께 지정할 수 있습니다. |탄력적 풀과 서비스 계층의 올바른 조합을 지정하세요. |
| 40861 | 16 |데이터베이스 버전 '%.*ls'이(가) '%.* ls'인 탄력적 풀 서비스 계층과 다를 수 없습니다. 데이터베이스 버전이 탄력적 풀 서비스 계층과 다릅니다. |탄력적 풀 서비스 계층과 다른 데이터베이스 버전을 지정하지 마세요.  데이터베이스 버전은 지정할 필요가 없습니다. |
| 40862 | 16 |탄력적 풀 서비스 목표를 지정한 경우 탄력적 풀 이름을 지정해야 합니다. 탄력적 풀 서비스 목표가 탄력적 풀을 고유하게 식별하지 못합니다. |탄력적 풀 서비스 목표를 사용하는 경우 탄력적 풀 이름을 지정하세요. |
| 40864 | 16 |탄력적 풀의 DTU는 서비스 계층 '%.*ls'에 대해 최소 (%d) DTU 이상이어야 합니다. 탄력적 풀의 DTU를 최소 한도 아래로 설정하려고 했습니다. |탄력적 풀의 DTU를 최소 한도 이상으로 다시 설정하세요. |
| 40865 | 16 |탄력적 풀의 DTU는 서비스 계층 '%.*ls'에 대해 (%d) DTU를 초과할 수 없습니다. 탄력적 풀의 DTU를 최대 한도 위로 설정하려고 했습니다. |탄력적 풀의 DTU를 최대 한도 미만으로 다시 설정하세요. |
| 40867 | 16 |데이터베이스당 DTU 최대값은 서비스 계층 '%.*ls'에 대해 최소 (%d) 이상이어야 합니다. 데이터베이스당 DTU 최대값을 지원되는 한도 아래로 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40868 | 16 |데이터베이스당 DTU 최대값은 서비스 계층 '%.*ls'에 대해 (%d)을(를) 초과할 수 없습니다. 데이터베이스당 DTU 최대값을 지원되는 한도를 초과하여 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40870 | 16 |데이터베이스당 DTU 최소값은 서비스 계층 '%.*ls'에 대해 (%d)을(를) 초과할 수 없습니다. 데이터베이스당 DTU 최소값을 지원되는 한도를 초과하여 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40873 | 16 |데이터베이스 수(%d) 및 데이터베이스당 DTU 최소값(%d)은 탄력적 풀의 DTU(%d)를 초과할 수 없습니다. 탄력적 풀에 있는 데이터베이스의 DTU 최소값을 탄력적 풀의 DTU를 초과하는 값으로 지정하려고 했습니다. | 탄력적 풀의 DTU를 늘리거나, 데이터베이스당 DTU 최소값을 줄이거나, 탄력적 풀에 포함된 데이터베이스 수를 줄이는 것을 고려하세요. |
| 40877 | 16 |탄력적 풀에 데이터베이스가 포함되어 있으면 삭제할 수 없습니다. 탄력적 풀에 하나 이상의 데이터베이스가 포함되어 있으므로 삭제할 수 없습니다. |탄력적 풀을 삭제하려면 탄력적 풀에서 데이터베이스를 제거하세요. |
| 40881 | 16 |탄력적 풀 '%.*ls'이(가) 데이터베이스 개수 한도에 도달했습니다.  탄력적 풀의 데이터베이스 개수 한도는 DTU가 (%d)인 탄력적 풀의 경우 (%d)을(를) 초과할 수 없습니다. 탄력적 풀의 데이터베이스 개수 한도가 도달된 상태에서 탄력적 풀에 데이터베이스를 만들거나 추가하려고 했습니다. | 가능하다면 탄력적 풀의 DTU를 늘려 데이터베이스 한도를 확대하거나 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 40889 | 16 |데이터베이스에 충분한 스토리지 공간을 제공하지 못하게 되므로 스토리지 풀'%.*ls'의 DTU 또는 스토리지 한도를 줄일 수 없습니다. 탄력적 풀의 스토리지 한도를 스토리지 사용량 아래로 줄이려고 했습니다. | 탄력적 풀에 있는 개별 데이터베이스의 스토리지 사용량을 줄이거나 풀에서 데이터베이스를 제거하여 DTU 또는 스토리지 한도를 줄이는 것을 고려하세요. |
| 40891 | 16 |데이터베이스당 DTU 최소값 (%d)은(는) 데이터베이스당 DTU 최대값 (%d)을(를) 초과할 수 없습니다. 데이터베이스당 DTU 최소값을 데이터베이스당 DTU 최대값보다 높게 설정하려고 했습니다. |데이터베이스당 DTU 최소값이 데이터베이스당 DTU 최대값을 초과하지 않도록 하세요. |
| TBD | 16 |탄력적 풀에 있는 개별 데이터베이스의 스토리지 크기는 '%.*ls' 서비스 계층 탄력적 풀에서 허용하는 최대 크기를 초과할 수 없습니다. 데이터베이스의 최대 크기가 탄력적 풀 서비스 계층에서 허용하는 최대 크기를 초과합니다. |데이터베이스의 최대 크기를 탄력적 풀 서비스 계층에서 허용하는 최대 크기 한도 내로 설정하세요. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>로그인에서 요청한 데이터베이스 "마스터"를 열 수 없습니다. 로그인에 실패했습니다.

이 문제는 계정에 마스터 데이터베이스에 액세스할 수 있는 권한이 없기 때문에 발생합니다. 그러나 기본적으로 SQL 서버 관리 스튜디오(SSMS)는 마스터 데이터베이스에 연결하려고 시도합니다.

이 문제를 해결하려면 다음 단계를 따릅니다.

1. SSMS의 로그인 화면에서 **옵션을**선택한 다음 **연결 속성을**선택합니다.
2. 데이터베이스에 **연결** 필드에서 사용자의 기본 데이터베이스 이름을 기본 로그인 데이터베이스로 입력한 다음 **연결을**선택합니다.

   ![연결 속성](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>연결 문제로 인해 오류가 발생하는지 확인

연결 문제로 인해 오류가 발생하는지 확인하려면 다음과 같은 연결을 여는 호출을 표시하는 프레임의 스택 추적을 **검토합니다(SqlConnection** 클래스에 대한 참조 참고).

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

쿼리 문제로 예외가 트리거되면 다음과 유사한 호출 스택이 **표시됩니다(SqlCommand** 클래스에 대한 참조 참고). 이 경우 [쿼리를 조정합니다.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

미세 조정 성능에 대한 추가 지침은 다음 리소스를 참조하십시오.

* [Azure SQL 인덱스 및 통계를 유지하는 방법](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Azure SQL Database에서 쿼리 성능 수동 튜닝](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [동적 관리 보기를 사용하여 성능 Azure SQL 데이터베이스 모니터링](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Azure SQL Database에서 쿼리 저장소 운영](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>일반적인 연결 문제 해결 단계

1. TCP/IP가 응용 프로그램 서버의 클라이언트 프로토콜로 활성화되어 있는지 확인합니다. 자세한 내용은 [클라이언트 프로토콜 구성을](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)참조하십시오. SQL Server 도구가 설치되어 있지 않은 응용 프로그램 서버에서는 **cliconfg.exe(SQL** Server 클라이언트 네트워크 유틸리티)를 실행하여 TCP/IP가 활성화되어 있는지 확인합니다.
2. 응용 프로그램의 연결 문자열을 확인하여 올바르게 구성되었는지 확인합니다. 예를 들어 연결 문자열에서 올바른 포트(1433)와 정규화된 서버 이름을 지정해야 합니다.
[SQL Server 연결 정보 받기를](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)참조하십시오.
3. 연결 시간 시간 시간 값을 늘려 보십시오. 연결 시간 시간을 30초 이상 사용하는 것이 좋습니다.
4. SQL Server [관리 스튜디오(SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)( UDL 파일, ping 또는 통신망)를 사용하여 응용 프로그램 서버와 Azure SQL 데이터베이스 간의 연결을 테스트합니다. 자세한 내용은 SQL Server 연결 문제 및 [연결 문제 진단](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)문제를 [해결합니다.](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)

   > [!NOTE]
   > 문제 해결 단계로 다른 클라이언트 컴퓨터에서 연결을 테스트할 수도 있습니다.

5. 가장 좋은 방법은 다시 시도 논리가 제자리에 있는지 확인합니다. 다시 시도 논리에 대한 자세한 내용은 [SQL Database에 대한 일시적인 오류 및 연결 오류 해결을](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)참조하십시오.

이러한 단계를 수행해도 문제가 해결되지 않으면 더 많은 데이터를 수집한 다음 지원팀에 문의하세요. 응용 프로그램이 클라우드 서비스인 경우 로깅을 사용하도록 설정합니다. 이 단계는 실패의 UTC 타임스탬프를 반환합니다. 또한 SQL Azure는 추적 ID를 반환합니다. [Microsoft 고객 지원 서비스는](https://azure.microsoft.com/support/options/) 이 정보를 사용할 수 있습니다.

로깅을 활성화하는 방법에 대한 자세한 내용은 [Azure App Service의 앱에 대한 진단 로깅 활성화를](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure SQL 연결 아키텍처](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Azure SQL 데이터베이스 및 데이터 웨어하우스 네트워크 액세스 제어](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
