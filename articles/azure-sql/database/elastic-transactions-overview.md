---
title: 클라우드 데이터베이스 간 분산 트랜잭션 (미리 보기)
description: Azure SQL Database 및 Azure SQL Managed Instance를 사용 하는 Elastic Database 트랜잭션 개요.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 65cd35dd60ed05da51b6da56882af4522b1b7573
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043430"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>클라우드 데이터베이스 간 분산 트랜잭션 (미리 보기)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에 대 한 탄력적 데이터베이스 트랜잭션을 사용 하면 여러 데이터베이스에 걸쳐 트랜잭션을 실행할 수 있습니다. 탄력적 데이터베이스 트랜잭션은 ADO.NET를 사용 하는 .NET 응용 프로그램에 사용할 수 있으며, [시스템. 트랜잭션](https://msdn.microsoft.com/library/system.transactions.aspx) 클래스를 사용 하 여 친숙 한 프로그래밍 환경과 통합할 수 있습니다. 라이브러리를 가져오려면 [.NET Framework 4.6.1(웹 설치 관리자)](https://www.microsoft.com/download/details.aspx?id=49981)을 참조하세요.
또한 Managed Instance 분산 트랜잭션은 [transact-sql](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)에서 사용할 수 있습니다.

온-프레미스에서 이러한 시나리오는 일반적으로 MSDTC (Microsoft DTC(Distributed Transaction Coordinator))를 실행 해야 합니다. MSDTC는 Azure에서 Platform as a Service 응용 프로그램에 사용할 수 없으므로 이제 분산 트랜잭션을 조정 하는 기능이 SQL Database 또는 Managed Instance에 직접 통합 되었습니다. 다음 그림에 표시 된 것 처럼 응용 프로그램은 모든 데이터베이스에 연결 하 여 분산 트랜잭션을 시작할 수 있으며, 데이터베이스 또는 서버 중 하나가 분산 트랜잭션을 투명 하 게 조정 합니다.

이 문서에서는 "분산 트랜잭션" 및 "탄력적 데이터베이스 트랜잭션" 이라는 용어는 동의어로 간주 되며 교대로 사용 됩니다.

  ![Elastic Database 트랜잭션을 사용한 Azure SQL Database와 분산 트랜잭션 ][1]

## <a name="common-scenarios"></a>일반적인 시나리오

탄력적 데이터베이스 트랜잭션을 사용 하면 응용 프로그램에서 여러 다른 데이터베이스에 저장 된 데이터를 원자 단위로 변경할 수 있습니다. 미리 보기는 C# 및 .NET의 클라이언트 쪽 개발 환경에 중점을 둡니다. [Transact-sql](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 을 사용 하 여 서버 쪽 환경 (저장 프로시저 또는 서버 쪽 스크립트에 작성 된 코드)은 Managed Instance에만 사용할 수 있습니다.
> [!IMPORTANT]
> 미리 보기에서는 Azure SQL Database와 Azure SQL Managed Instance 간에 탄력적 데이터베이스 트랜잭션 실행이 현재 지원 되지 않습니다. 탄력적 데이터베이스 트랜잭션은 SQL 데이터베이스 집합 또는 관리 되는 인스턴스 집합에만 걸쳐 있을 수 있습니다.

탄력적 데이터베이스 트랜잭션은 다음 시나리오를 대상으로 합니다.

* Azure의 다중 데이터베이스 응용 프로그램:이 시나리오에서는 여러 다른 종류의 데이터가 서로 다른 데이터베이스에 상주 하도록 SQL Database 또는 Managed Instance에서 여러 데이터베이스에 데이터가 수직 분할 됩니다. 일부 작업에는 두 개 이상의 데이터베이스에 유지 되는 데이터를 변경 해야 합니다. 애플리케이션은 여러 데이터베이스에 걸쳐 변경을 조정하고 원자성을 보장하기 위해 탄력적 데이터베이스 트랜잭션을 사용합니다.
* Azure의 분할 된 데이터베이스 응용 프로그램:이 시나리오에서 데이터 계층은 [Elastic Database 클라이언트 라이브러리](elastic-database-client-library.md) 또는 자체 분할를 사용 하 여 SQL Database 또는 Managed Instance에서 여러 데이터베이스에 걸쳐 데이터를 수평으로 분할 합니다. 한 가지 주요한 사용 사례는 여러 테넌트에 변경이 미칠 때 분할된 다중 테넌트 애플리케이션에 원자성 변경을 수행해야 하는 경우입니다. 서로 다른 데이터베이스에 상주하는 하나의 테넌트에서 다른 테넌트로 전송하는 인스턴스를 생각해 볼 수 있습니다. 두 번째 경우는 대량 테 넌 트의 용량 요구를 수용 하는 세분화 된 분할입니다 .이 경우 일반적으로 일부 원자성 작업은 동일한 테 넌 트에 사용 되는 여러 데이터베이스에서 확장 해야 함을 의미 합니다. 세 번째 사례는 여러 데이터베이스에 걸쳐 복제되는 참조 데이터에 대한 원자성 업데이트입니다. 이러한 방식의 트랜잭션 처리된, 원자성, 작업은 미리 보기를 사용하여 여러 데이터베이스에 걸쳐 조정될 수 있습니다.
  탄력적 데이터베이스 트랜잭션은 데이터베이스 전반의 트랜잭션 원자성을 보장 하기 위해 2 단계 커밋을 사용 합니다. 단일 트랜잭션 내에서 한 번에 100 개 미만의 데이터베이스를 포함 하는 트랜잭션에 적합 합니다. 이러한 한도는 적용 되지 않지만 이러한 제한을 초과 하는 경우 탄력적 데이터베이스 트랜잭션에 대 한 성능 및 성공률을 저하 시킬 수 있습니다.

## <a name="installation-and-migration"></a>설치 및 마이그레이션

탄력적 데이터베이스 트랜잭션에 대 한 기능은 .NET 라이브러리 System.Data.dll 및 System.Transactions.dll에 대 한 업데이트를 통해 제공 됩니다. DLL은 원자성을 보장하기 위해 필요한 경우 2단계 커밋이 사용되도록 합니다. 탄력적 데이터베이스 트랜잭션을 사용하여 애플리케이션 개발을 시작하려면 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 이상 버전을 설치합니다. 이전 버전의 .NET framework를 실행하면, 트랜잭션이 분산 트랜잭션으로 승격하지 못하고 예외가 발생합니다.

설치 후에는 SQL Database 및 Managed Instance에 대 한 연결을 사용 하 여 시스템 트랜잭션에서 분산 트랜잭션 Api를 사용할 수 있습니다. 이러한 Api를 사용 하는 기존 MSDTC 응용 프로그램이 있는 경우 4.6.1 Framework를 설치한 후 .NET 4.6에 대 한 기존 응용 프로그램을 다시 빌드합니다. 프로젝트에서 .NET 4.6를 대상으로 하는 경우 새 프레임 워크 버전에서 업데이트 된 Dll을 자동으로 사용 하 고 SQL Database 또는 Managed Instance에 대 한 연결을 조합 하 여 분산 트랜잭션 API 호출을 수행 합니다.

탄력적 데이터베이스 트랜잭션은 MSDTC를 설치할 필요가 없습니다. 대신, 탄력적 데이터베이스 트랜잭션은 및 서비스 내에서 직접 관리 됩니다. 이는 SQL Database 또는 Managed Instance에서 분산 트랜잭션을 사용할 필요가 없기 때문에 클라우드 시나리오를 크게 간소화 합니다. 섹션 4는 탄력적 데이터베이스 트랜잭션 배포 방법 및 필요한 .NET framework를 Azure에 대한 클라우드 애플리케이션과 함께 보다 자세히 설명합니다.

## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services용 .NET 설치

Azure에서는 .NET 애플리케이션을 호스트하는 여러 제품을 제공합니다. [Azure App Service, Cloud Services 및 Virtual Machines 비교](/azure/architecture/guide/technology-choices/compute-decision-tree)에서 다양한 제품을 비교할 수 있습니다. 제품의 게스트 OS가 탄력적인 트랜잭션에 필요한 .NET 4.6.1보다 작은 경우 게스트 OS를 4.6.1로 업그레이드해야 합니다.

Azure App Service 게스트 OS에 대 한 업그레이드는 현재 지원 되지 않습니다. Azure Virtual Machines의 경우 VM에 로그인하여 최신 .NET Framework용 설치 관리자를 실행하기만 하면 됩니다. Azure Cloud Services의 경우 최신 .NET 버전의 설치를 배포의 시작 작업에 포함해야 합니다. 개념 및 단계는 [클라우드 서비스 역할에 .NET 설치](../../cloud-services/cloud-services-dotnet-install-dotnet.md)에 문서화되어 있습니다.  

.NET 4.6.1용 설치 관리자는 Azure 클라우드 서비스에서 프로세스를 부트스트랩하는 과정에서 .NET 4.6용 설치 관리자보다 더 많은 임시 스토리지 공간을 필요로 할 수 있습니다. 성공적인 설치를 위해 다음 예에서처럼 LocalResources 섹션의 ServiceDefinition.csdef 파일과 시작 작업의 환경 설정에서 Azure 클라우드 서비스의 임시 스토리지 공간을 늘려야 합니다.

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET 개발 환경

### <a name="multi-database-applications"></a>다중 데이터베이스 애플리케이션

다음 샘플 코드는 .NET System.Transactions와 친숙한 프로그래밍 환경을 사용합니다. TransactionScope 클래스는 .NET에서 앰비언트 트랜잭션을 설정합니다. (“앰비언트 트랜잭션”이란 현재 스레드에 존재하는 트랜잭션입니다.) 모든 연결은 트랜잭션에 참여하는 TransactionScope 내에서 열려 있습니다. 다른 데이터베이스가 참여하면, 트랜잭션은 분산 트랜잭션으로 자동 승격됩니다. 트랜잭션의 결과는 커밋을 나타내기 위해 완료해야 하는 범위를 설정하여 제어됩니다.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>분할된 데이터베이스 애플리케이션

또한 SQL Database 및 Managed Instance에 대 한 탄력적 데이터베이스 트랜잭션은 탄력적 데이터베이스 클라이언트 라이브러리의 OpenConnectionForKey 메서드를 사용 하 여 확장 된 데이터 계층에 대 한 연결을 여는 분산 트랜잭션 조정을 지원 합니다. 여러 개의 다른 분할 키 값에 걸쳐 변경 사항에 대해 트랜잭션 일관성을 보장해야 하는 경우를 고려해 보겠습니다. 다른 분할 키 값을 호스팅하는 분할에 대한 연결은 OpenConnectionForKey를 사용하여 중개됩니다. 일반적인 상황에서 트랜잭션 보장이 분산 트랜잭션을 요구하도록 하기 위해서 다른 분할에 연결될 수 있습니다.
다음 코드 샘플에서 이 접근 방법을 보여 줍니다. shardmap이라는 매개 변수가 탄력적 데이터베이스 클라이언트 라이브러리의 분할된 데이터베이스 맵을 나타내기 위해 사용된다고 가정합니다.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-sql 개발 환경

Transact-sql을 사용 하는 서버 쪽 분산 트랜잭션은 Azure SQL Managed Instance 에서만 사용할 수 있습니다. 분산 트랜잭션은 동일한 [서버 신뢰 그룹](https://aka.ms/mitrusted-groups)에 속한 관리 되는 인스턴스에서만 실행할 수 있습니다. 이 시나리오에서 관리 되는 인스턴스는 서로를 참조 하기 위해 [연결 된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) 를 사용 해야 합니다.

다음 예제 Transact-sql 코드는 [BEGIN DISTRIBUTED transaction](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 을 사용 하 여 분산 트랜잭션을 시작 합니다.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='managedinstance02', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='sql-managed-instance-02.48ea8fd5ac90.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'managedinstance02', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>.NET 및 Transact-sql 개발 환경 결합

System.object 클래스를 사용 하는 .NET 응용 프로그램은 TransactionScope 클래스를 Transact-sql 문 BEGIN 분산 트랜잭션과 결합할 수 있습니다. TransactionScope 내에서 BEGIN DITRIBUTED TRANSACTION을 실행 하는 내부 트랜잭션은 명시적으로 분산 트랜잭션으로 승격 됩니다. 또한 TransactionScope 내에서 두 번째 SqlConnecton를 열면 암시적으로 분산 트랜잭션으로 승격 됩니다. 분산 트랜잭션이 시작 되 면 .NET 또는 Transact-sql에서 들어오는 모든 후속 트랜잭션 요청이 부모 분산 트랜잭션에 조인 됩니다. 결과적으로 BEGIN 문에 의해 시작 된 모든 중첩 된 트랜잭션 범위는 동일한 트랜잭션으로 종료 되 고 커밋/롤백 문은 전체 결과에 다음과 같은 영향을 미칠 수 있습니다.
 * COMMIT 문은 BEGIN 문에 의해 시작 된 트랜잭션 범위에 영향을 주지 않습니다. 즉, 전체 () 메서드가 TransactionScope 개체에서 호출 되기 전에는 결과가 커밋되지 않습니다. TransactionScope 개체가 완료 되기 전에 소멸 되 면 해당 범위 내에서 수행 된 모든 변경 내용이 롤백됩니다.
 * ROLLBACK 문을 수행 하면 전체 TransactionScope가 롤백됩니다. TransactionScope 내에서 새 트랜잭션을 등록 하려고 하면 나중에 실패 하 고 TransactionScope 개체에서 Complete ()를 호출 하려고 시도 합니다.

Transact-sql을 사용 하 여 트랜잭션이 분산 트랜잭션으로 명시적으로 승격 되는 예는 다음과 같습니다.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

다음 예에서는 두 번째 SqlConnecton이 TransactionScope 내에서 시작 된 후 암시적으로 분산 트랜잭션으로 승격 된 트랜잭션을 보여 줍니다.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Azure SQL Database에 대 한 여러 서버 간 트랜잭션

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

탄력적 데이터베이스 트랜잭션은 Azure SQL Database의 여러 서버에서 지원 됩니다. 트랜잭션이 서버 간 경계를 초과 하는 경우 참여 하는 서버를 상호 통신 관계에 먼저 입력 해야 합니다. 통신 관계가 설정된 후에는 두 서버 중 하나의 모든 데이터베이스가 다른 서버 데이터베이스와의 탄력적인 트랜잭션에 참여할 수 있습니다. 두 개 이상의 서버에 걸친 트랜잭션을 사용 하는 경우에는 모든 서버 쌍에 대해 통신 관계가 준비 되어야 합니다.

다음 PowerShell cmdlet을 사용하여 탄력적인 데이터베이스 트랜잭션에 대한 서버 간 통신 관계를 관리할 수 있습니다.

* **AzSqlServerCommunicationLink**:이 cmdlet을 사용 하 여 Azure SQL Database의 두 서버 간에 새 통신 관계를 만들 수 있습니다. 대칭 관계는 두 서버 모두 다른 서버를 사용 하 여 트랜잭션을 시작할 수 있음을 의미 합니다.
* **Get-AzSqlServerCommunicationLink**: 기존 통신 관계와 해당 속성을 검색하려면 이 cmdlet을 사용합니다.
* **Remove-AzSqlServerCommunicationLink**: 기존 통신 관계와 해당 속성을 제거하려면 이 cmdlet을 사용합니다.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 여러 서버 간 트랜잭션

분산 트랜잭션은 Azure SQL Managed Instance의 여러 서버에서 지원 됩니다. 트랜잭션이 Managed Instance 경계를 교차 하는 경우 참여 하는 인스턴스를 상호 보안 및 통신 관계에 먼저 입력 해야 합니다. 이 작업은 Azure Portal에서 수행할 수 있는 [서버 신뢰 그룹](https://aka.ms/mitrusted-groups)을 설정 하 여 수행 됩니다.

  ![Azure Portal의 서버 신뢰 그룹][3]

다음 다이어그램에서는 .NET 또는 Transact-sql을 사용 하 여 분산 트랜잭션을 실행할 수 있는 관리 되는 인스턴스가 있는 서버 신뢰 그룹을 보여 줍니다.

  ![탄력적 트랜잭션을 사용 하 여 Azure SQL Managed Instance를 사용 하는 분산 트랜잭션][2]

## <a name="monitoring-transaction-status"></a>트랜잭션 상태 모니터링

Dmv (동적 관리 뷰)를 사용 하 여 진행 중인 탄력적 데이터베이스 트랜잭션의 상태와 진행률을 모니터링할 수 있습니다. 트랜잭션과 관련 된 모든 Dmv는 SQL Database 및 Managed Instance의 분산 트랜잭션과 관련이 있습니다. 해당 DMV 목록은 다음에서 찾을 수 있습니다. [트랜잭션 관련 동적 관리 뷰 및 함수(Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)

다음 DMV는 특히 유용합니다.

* **sys.dm\_tran\_active\_transactions**: 현재 활성 트랜잭션과 그 상태를 나열합니다. UOW(작업 단위) 열은 동일한 분산 트랜잭션에 속하는 다른 자식 트랜잭션을 식별할 수 있습니다. 동일한 분산 트랜잭션 내의 모든 트랜잭션은 동일한 UOW 값을 갖습니다. 자세한 내용은 [DMV 설명서](https://msdn.microsoft.com/library/ms174302.aspx)를 참조 하세요.
* **sys.dm\_tran\_database\_transactions**: 로그에 트랜잭션 배치 같은 트랜잭션에 대한 추가적인 정보를 제공합니다. 자세한 내용은 [DMV 설명서](https://msdn.microsoft.com/library/ms186957.aspx)를 참조 하세요.
* **sys.dm\_tran\_locks**: 진행 중인 트랜잭션에 의해 현재 유지되는 잠금에 대한 정보를 제공합니다. 자세한 내용은 [DMV 설명서](https://msdn.microsoft.com/library/ms190345.aspx)를 참조 하세요.

## <a name="limitations"></a>제한 사항

현재 SQL Database의 탄력적 데이터베이스 트랜잭션에는 다음과 같은 제한 사항이 적용 됩니다.

* SQL Database의 데이터베이스에서의 트랜잭션만 지원 됩니다. 다른 [X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA) 리소스 공급자 및 SQL Database 외부의 데이터베이스는 탄력적 데이터베이스 트랜잭션에 참여할 수 없습니다. 즉, 탄력적 데이터베이스 트랜잭션은 온-프레미스 SQL Server 및 Azure SQL Database에 걸쳐 확장 될 수 없습니다. 온-프레미스 분산 트랜잭션을 위해서는 MSDTC를 계속 사용하세요.
* .NET 애플리케이션의 클라이언트 조정 트랜잭션만 지원됩니다. BEGIN DISTRIBUTED TRANSACTION 같은 T-SQL에 대한 서버 쪽 지원이 계획되어 있지만 아직 제공되지 않습니다.
* WCF 서비스 전반의 트랜잭션은 지원 되지 않습니다. 예를 들어 트랜잭션을 실행하는 WCF 서비스 메서드가 있습니다. 트랜잭션 범위 내로 호출을 묶으면 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)으로 실패합니다.

현재 Managed Instance의 분산 트랜잭션에는 다음과 같은 제한 사항이 적용 됩니다.

* Managed Instance의 데이터베이스에서의 트랜잭션만 지원 됩니다. 다른 [X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA) 리소스 공급자와 Azure SQL Managed Instance 외부의 데이터베이스는 분산 트랜잭션에 참여할 수 없습니다. 즉, 분산 트랜잭션은 온-프레미스 SQL Server 및 Azure SQL Managed Instance에서 확장할 수 없습니다. 온-프레미스 분산 트랜잭션을 위해서는 MSDTC를 계속 사용하세요.
* WCF 서비스 전반의 트랜잭션은 지원 되지 않습니다. 예를 들어 트랜잭션을 실행하는 WCF 서비스 메서드가 있습니다. 트랜잭션 범위 내로 호출을 묶으면 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)으로 실패합니다.
* 분산 트랜잭션에 참여 하려면 Azure SQL Managed Instance가 [서버 트러스트 그룹](https://aka.ms/mitrusted-groups) 의 일부 여야 합니다.
* [서버 신뢰 그룹](https://aka.ms/mitrusted-groups) 의 제한 사항은 분산 트랜잭션에 영향을 줍니다.
* 분산 트랜잭션에 참여 하는 관리 되는 인스턴스는 개인 끝점을 통해 연결 해야 하며 (배포 되는 가상 네트워크의 개인 IP 주소를 사용 하 여) 개인 Fqdn을 사용 하 여 상호 참조 해야 합니다. 클라이언트 응용 프로그램은 개인 끝점에서 분산 트랜잭션을 사용할 수 있습니다. 또한 Transact-sql에서 전용 끝점을 참조 하는 연결 된 서버를 활용 하는 경우 클라이언트 응용 프로그램은 공용 끝점 에서도 분산 트랜잭션을 사용할 수 있습니다. 이러한 제한 사항은 다음 다이어그램에 설명 되어 있습니다.
  ![개인 끝점 연결 제한][4]
## <a name="next-steps"></a>다음 단계

* 질문이 있는 경우 [Microsoft Q&SQL Database에 대 한 질문 페이지](https://docs.microsoft.com/answers/topics/azure-sql-database.html)에 문의 하세요.
* 기능 요청에 대해 [SQL Database 피드백 포럼](https://feedback.azure.com/forums/217321-sql-database/) 또는 [Managed Instance 포럼](https://feedback.azure.com/forums/915676-sql-managed-instance)에 추가 합니다.



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
 
