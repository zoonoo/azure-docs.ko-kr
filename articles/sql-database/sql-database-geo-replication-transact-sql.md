<properties
    pageTitle="TRANSACT-SQL로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성 | Microsoft Azure"
    description="TRANSACT-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="11/10/2015"
    ms.author="carlrab"/>

# TRANSACT-SQL로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


이 문서에서는 TRANSACT-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제를 구성하는 방법을 보여 줍니다.


지역에서 복제를 사용하면 서로 다른 데이터 센터 위치(지역)에 최대 4개의 복제본(보조) 데이터베이스를 생성할 수 있습니다. 보조 데이터베이스를 데이터 센터 정전 또는 주 데이터베이스에 연결하지 못하는 경우 사용할 수 있습니다.

지역에서 복제는 Standard 및 Premium 데이터베이스에서만 사용할 수 있습니다.

Standard 데이터베이스는 읽을 수 없는 보조를 하나를 가질 수 있으며 권장되는 영역을 사용해야 합니다. 프리미엄 데이터베이스는 사용 가능한 지역 중에서 최대 4개의 읽기 가능한 보조를 가질 수 있습니다.


지역에서 복제를 구성하려면 다음이 필요합니다.

- Azure 구독 - Azure 구독이 없는 경우 이 페이지의 맨 위에서 **무료 평가판**을 클릭합니다. 그 후 돌아와서 이 문서를 마칩니다.
- 논리 Azure SQL 데이터베이스 서버 <MyLocalServer> 및 SQL 데이터베이스 <MyDB> - 다른 지리적 영역으로 복제하려는 주 데이터베이스입니다.
- 하나 이상의 논리 Azure SQL 데이터베이스 서버 <MySecondaryServer(n)> - 지역에서 복제 보조 데이터베이스를 만드는 파트너 서버가 될 수 있는 논리 서버입니다.
- 주 데이터베이스에서 DBManager인 로그인은 지역에서 복제하고 지역에서 복제를 구성하는 파트너 서버의 DBManager가 되는 로컬 데이터베이스의 db\_ownership을 가지고 있습니다.
- 최신 버전의 SQL Server Management Studio - SSMS(SQL Server Management Studio)의 최신 버전을 가져오려면 [SQL Server Management Studio 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)로 이동합니다. SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 논리 서버 및 데이터베이스 관리에 대한 정보는 [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-azure-ssms.md)를 참조하세요.

## 보조 데이터베이스 추가

**ALTER DATABASE** 문을 사용하여 파트너 서버에서 지역에서 복제된 보조 데이터베이스를 만들 수 있습니다. 복제할 데이터베이스를 포함하는 서버의 마스터 데이터베이스에서 이 문을 실행합니다. 지역에서 복제된 데이터베이스("주 데이터베이스")는 복제되는 데이터베이스와 동일한 이름을 가지며 기본적으로 주 데이터베이스와 동일한 서비스 수준을 가집니다. 보조 데이터베이스는 읽을 수 있거나 읽을 수 없을 수 있으며 단일 데이터베이스 또는 탄력적인 데이터베이스가 될 수 있습니다. 자세한 내용은 [ALTER DATABASE(Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 및 [서비스 계층](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)을 참조하세요. 보조 데이터베이스가 만들어지고 시드된 후 데이터는 주 데이터베이스에서 비동기적으로 복제를 시작합니다. 다음 단계에서는 Management Studio를 사용하여 지역에서 복제를 구성하는 방법을 설명합니다. 단일 데이터베이스 또는 탄력적 데이터베이스를 사용하여 읽을 수 없는 및 읽을 수 있는 보조를 만드는 단계가 제공됩니다.

> [AZURE.NOTE]보조 데이터베이스가 지정된 파트너 서버에 있는 경우(예: 지역에서 복제 관계는 현재 존재하거나 이전에 존재했으므로) 명령이 실패합니다.


### 읽을 수 없는 보조(단일 데이터베이스) 추가

단일 데이터베이스로 읽을 수 없는 보조를 만들려면 다음 단계를 사용합니다.

1. SQL Server Management Studio 13.0.600.65 이상의 버전을 사용합니다.다.

 	 >[AZURE.IMPORTANT] [최신](https://msdn.microsoft.com/library/mt238290.aspx) 버전의 SQL Server Management Studio를 다운로드합니다. Azure 포털에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다.


2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 로컬 데이터베이스를 지역에서 복제 주 데이터베이스(<MySecondaryServer1>의 읽을 수 있는 보조 데이터베이스와 함께)로 만듭니다.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. **실행**을 클릭하여 쿼리를 실행합니다.


### 읽을 수 있는 보조(단일 데이터베이스) 추가
단일 데이터베이스로 읽을 수 있는 보조를 만들려면 다음 단계를 사용합니다.

1. Management Studio에서 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 로컬 데이터베이스를 지역에서 복제 주 데이터베이스(보조 서버의 읽을 수 있는 보조 데이터베이스와 함께)로 만듭니다.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. **실행**을 클릭하여 쿼리를 실행합니다.



### 읽을 수 없는 보조(탄력적 데이터베이스) 추가
탄력적 데이터베이스로 읽을 수 없는 보조를 만들려면 다음 단계를 사용합니다.

1. Management Studio에서 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 로컬 데이터베이스를 지역에서 복제 주 데이터베이스(탄력적 풀에 포함된 보조 서버의 읽을 수 없는 보조 데이터베이스와 함께)로 만듭니다.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool1);

4. **실행**을 클릭하여 쿼리를 실행합니다.



### 읽을 수 있는 보조(탄력적 데이터베이스) 추가
읽을 수 있는 보조 데이터베이스를 탄력적 데이터베이스로 만들려면 다음 단계를 사용합니다.

1. Management Studio에서 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 로컬 데이터베이스를 지역에서 복제 주 데이터베이스(탄력적 풀에 포함된 보조 서버의 읽을 수 있는 보조 데이터베이스와 함께)로 만듭니다.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool2);

4. **실행**을 클릭하여 쿼리를 실행합니다.



## 보조 데이터베이스 제거

**ALTER DATABASE** 문을 사용하여 보조 데이터베이스와 주 데이터베이스 간의 복제 파트너 관계를 영구적으로 종료합니다. 이 문은 주 데이터베이스가 상주하는 마스터 데이터베이스에서 실행됩니다. 관계가 종료된 후 보조 데이터베이스는 일반 읽기-쓰기 데이터베이스가 됩니다. 보조 데이터베이스에 대한 연결이 끊어진 경우 명령이 성공하지만 연결이 복원된 후 보조는 읽기-쓰기가 됩니다. 자세한 내용은 [ALTER DATABASE(Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 및 [서비스 계층](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)을 참조하세요.

지역에서 복제 파트너 관계에서 지역에서 복제된 보조 데이터베이스를 제거하려면 다음 단계를 사용합니다.

1. Management Studio에서 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 지역에서 복제된 보조 데이터베이스를 제거합니다.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. **실행**을 클릭하여 쿼리를 실행합니다.


## 새 주 데이터베이스가 되도록 보조 데이터베이스를 승격하는 계획된 장애 조치 시작

**ALTER DATABASE** 문을 사용하여 계획된 방식으로 보조 데이터베이스가 새로운 주 데이터베이스가 되도록 수준을 올리고, 기존의 주 데이터베이스가 보조 데이터베이스가 되도록 수준을 내릴 수 있습니다 이 문은 승격되는 지역에서 복제된 보조 데이터베이스가 상주하는 Azure SQL 데이터베이스 논리 서버의 마스터 데이터베이스에서 실행됩니다. 이 기능은 DR 훈련 중과 같은 계획된 장애 조치에 대해 설계되었으며 주 데이터베이스는 사용할 수 있어야 합니다.

명령은 다음 워크플로 수행합니다.

1. 복제를 보조 데이터베이스로 플러시될 모든 미해결 트랜잭션을 초래하고 모든 새 트랜잭션을 차단하는 동기 모드로 일시적으로 전환

2. 지역에서 복제 파트너 관계에서 두 데이터베이스의 역할을 전환합니다.

이 시퀀스는 데이터 손실이 발생하지 않음을 보장합니다. 역할이 전환되는 동안 두 데이터베이스를 모두 사용할 수 없는 (0-25초의 순서로) 짧은 기간이 있습니다. 전체 작업은 정상적인 상황에서 완료하는데 1분 미만이 걸려야 합니다. 자세한 내용은 [ALTER DATABASE(Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 및 [서비스 계층](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)을 참조하세요.


> [AZURE.NOTE]명령이 실행될 때 주 데이터베이스를 사용할 수 없는 경우 기본 서버를 사용할 수 없음을 나타내는 오류 메시지와 함께 명령이 실패합니다. 드문 경우로 작업을 완료할 수 없으며 중지될 수 있습니다. 이 경우 사용자는 강제 장애 조치 명령을 실행하고 데이터 손실을 허용할 수 있습니다.

계획된 장애 조치를 시작하려면 다음 단계를 사용합니다.

1. Management Studio에서 지역에서 복제된 보조 데이터베이스가 상주하는 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 지역에서 복제된 데이터베이스를 지역에서 복제 주 데이터베이스(<ElasticPool2>에 포함된 <MySecondaryServer4>의 읽을 수 있는 보조 데이터베이스와 함께)로 만듭니다.

        ALTER DATABASE <MyDB> FAILOVER;

4. **실행**을 클릭하여 쿼리를 실행합니다.



## 주 데이터베이스에서 보조 데이터베이스로 계획되지 않은 장애 조치 시작

**ALTER DATABASE** 문을 사용하여 주 데이터베이스를 더 이상 사용할 수 없는 경우에, 계획되지 않은 방식으로 보조 데이터베이스가 새로운 주 데이터베이스가 되도록 수준을 올리고, 기존의 주 데이터베이스가 보조 데이터베이스가 되도록 강제로 수준을 내릴 수 있습니다 이 문은 승격되는 지역에서 복제된 보조 데이터베이스가 상주하는 Azure SQL 데이터베이스 논리 서버의 마스터 데이터베이스에서 실행됩니다.

이 기능은 데이터베이스의 가용성 복원이 중요하고 일부 데이터 손실이 허용되는 경우 재해 복구를 위해 설계되었습니다. 강제 장애 조치가 호출되면 지정된 보조 데이터베이스는 즉시 주 데이터베이스가 되며 쓰기 트랜잭션 승인을 시작합니다. 기존 주 데이터베이스가 이 새 주 데이터베이스와 다시 연결할 수 있는 즉시 기존 주 데이터베이스에서 증분 백업이 수행되고 이어서 이전 주 데이터베이스는 새 주 데이터베이스에 대한 보조 데이터베이스로 만들어지며 이는 단순히 새 주의 복제본 동기화입니다.

그러나 특정 시점 복원은 보조 데이터베이스에서 지원되지 않으므로 강제 장애 조치가 발생하기 전에 새 주 데이터베이스에 복제되지 않은 이전 주 데이터베이스에 커밋된 데이터를 복구하고자 할 경우 사용자는 손실된 데이터를 복구하도록 지원해야 합니다.

> [AZURE.NOTE]주 및 보조가 온라인일 때 명령이 실행되는 경우 이전 주는 새 보조가 되지만 데이터 동기화는 시도되지 않습니다. 따라서 일부 데이터 손실이 발생할 수 있습니다.


주 데이터베이스에 여러 보조 데이터베이스가 있는 경우 명령은 명령이 실행된 보조 서버에서만 성공합니다. 그러나 다른 보조 데이터베이스는 강제 장애 조치가 발생했음을 모릅니다. 사용자는 "보조 제거" API를 사용하여 이 구성을 수동으로 복구한 다음 이러한 추가 보조 데이터베이스에 대해 지역에서 복제를 다시 구성해야 합니다.

지역에서 복제 파트너 관계에서 지역에서 복제된 보조 데이터베이스를 강제로 제거하려면 다음 단계를 사용합니다.

1. Management Studio에서 지역에서 복제된 보조 데이터베이스가 상주하는 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 다음 **ALTER DATABASE** 문을 사용하여 <ElasticPool2>에서 <MySecondaryServer4>의 읽을 수 있는 보조 데이터베이스와 함께 <MyLocalDB>을(를) 지역에서 복제 주 데이터베이스로 만듭니다.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. **실행**을 클릭하여 쿼리를 실행합니다.

## 지역에서 복제 구성 및 상태 모니터링

모니터링 작업에는 지역에서 복제 구성의 모니터링 및 데이터 복제 상태 모니터링이 포함됩니다. 마스터 데이터베이스의 **sys.dm\_geo\_replication\_links** 동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 논리 서버의 각 데이터베이스에 대한 모든 기존 복제 링크에 대한 정보를 반환할 수 있습니다. 이 뷰는 주 및 보조 데이터베이스 간의 각 복제 링크에 대한 행을 포함합니다. **sys.dm\_replication\_status** 동적 관리 뷰를 사용하여 현재 복제 링크에 참여하는 각 Azure SQL 데이터베이스에 대한 행을 반환할 수 있습니다. 주 및 보조 데이터베이스가 포함됩니다. 지정된 주 데이터베이스에 두 개 이상의 연속 복제 링크가 있는 경우 이 테이블은 각 관계에 대한 행을 포함합니다. 논리 마스터를 포함한 모든 데이터베이스에 뷰가 생성됩니다. 그러나 논리 마스터에서 이 뷰를 쿼리하면 빈 집합이 반환됩니다. **sys.dm\_operation\_status** 동적 관리 뷰를 사용하여 복제 링크의 상태를 포함하는 모든 데이터베이스 작업에 대한 상태를 표시할 수 있습니다. 자세한 내용은 [sys.dm\_geo\_replication\_links(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/mt575504.aspx) 및 [sys.dm\_operation\_status(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn270022.aspx)를 참조하세요.

지역에서 복제 파트너 관계를 모니터링하려면 다음 단계를 사용합니다.

1. Management Studio에서 Azure SQL 데이터베이스 논리 서버에 연결합니다.

2. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

3. 지역에서 복제 링크와 함께 모든 데이터베이스를 표시하려면 다음 문을 사용합니다.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. **실행**을 클릭하여 쿼리를 실행합니다.
5. 데이터베이스 폴더를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **MyDB**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.
6. 다음 문을 사용하여 복제 지연 및 MyDB의 보조 데이터베이스의 마지막 복제 시간을 표시합니다.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. **실행**을 클릭하여 쿼리를 실행합니다.
8. 다음 문을 사용하여 데이터베이스 MyDB와 연결되는 가장 최근의 지역에서 복제 작업을 표시합니다.

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. **실행**을 클릭하여 쿼리를 실행합니다.


## 다음 단계

- [재해 복구 연습](sql-database-disaster-recovery-drills.md)


## 추가 리소스

- [새 지역에서 복제 기능에 대한 주요 내용](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [지역에서 복제를 사용하여 비즈니스 연속성을 위한 클라우드 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->