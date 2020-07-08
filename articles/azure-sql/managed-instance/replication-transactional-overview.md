---
title: 트랜잭션 복제
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance에서 SQL Server 트랜잭션 복제를 사용 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 04/20/2020
ms.openlocfilehash: 2163a6e47767f6ce990526c7ececb7b4b554bf4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708501"
---
# <a name="transactional-replication-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance를 사용 하 여 트랜잭션 복제
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

트랜잭션 복제는 azure sql Managed Instance 및 SQL Server의 기능으로, Azure SQL Managed Instance의 테이블 또는 SQL Server 인스턴스의 데이터를 원격 데이터베이스에 배치 된 테이블로 복제할 수 있습니다. 이 기능을 사용하면 서로 다른 데이터베이스의 여러 테이블을 동기화할 수 있습니다.

## <a name="overview"></a>개요

트랜잭션 복제를 사용 하 여 Azure SQL Managed Instance에서 수행한 변경 내용을 다음과 같이 푸시할 수 있습니다.

- 온-프레미스 또는 Azure VM에 SQL Server 데이터베이스
- Azure SQL Database의 단일 또는 풀링된 데이터베이스
- Azure SQL의 단일 또는 풀링된 데이터베이스 Managed Instance

  > [!NOTE]
  > Azure SQL Managed Instance의 모든 기능을 사용 하려면 최신 버전의 [SSMS (SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ) 및 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용 해야 합니다.

### <a name="components"></a>구성 요소

트랜잭션 복제의 주요 구성 요소는 다음 그림에 표시 된 것 처럼 **게시자**, **배포자**및 **구독자**입니다.  

![SQL Database를 사용한 복제](./media/replication-transactional-overview/replication-to-sql-database.png)

| 역할 | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| **게시자** | 예 | 예 |
| **총판** | 예 | 예|
| **끌어오기 구독자** | 예 | 예|
| **밀어넣기 구독자**| 예 | 예|
| &nbsp; | &nbsp; | &nbsp; |

**게시자** 는 배포자로 업데이트를 전송 하 여 일부 테이블 (아티클)에 적용 된 변경 내용을 게시 합니다. 게시자는 Azure SQL Managed Instance 또는 SQL Server 인스턴스일 수 있습니다.

**배포자** 는 게시자의 아티클에 대 한 변경 내용을 수집 하 여 구독자에 게 배포 합니다. 배포자는 Azure SQL Managed Instance 또는 SQL Server 인스턴스일 수 있습니다 (게시자 버전과 같거나 높은 버전인 경우).

**구독자** 가 게시자에 대 한 변경 내용을 받습니다. SQL Server 인스턴스와 Azure SQL Managed Instance 모두 밀어넣기 및 끌어오기 구독자 일 수 있지만, 배포자가 Azure SQL Managed Instance이 고 구독자가이 아닌 경우 끌어오기 구독은 지원 되지 않습니다. Azure SQL Database의 데이터베이스는 밀어넣기 구독자 일 수만 있습니다.

Azure SQL Managed Instance은 다음 버전의 SQL Server에서 구독자가 될 수 있습니다.

- SQL Server 2016 이상
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 또는 [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 또는 [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Azure에서 게시 개체를 지원하지 않는 다른 버전의 SQL Server에서는 [데이터 다시 게시](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) 방법을 사용하여 데이터를 최신 버전의 SQL Server로 이동할 수 있습니다.
   > - 이전 버전을 사용하여 복제를 구성하는 시도는 오류 번호 MSSQL_REPL20084(프로세스가 구독자에 연결할 수 없습니다.) 및 MSSQ_REPL40532(로그인에서 요청한 서버 \<name>을 열 수 없습니다. 로그인이 실패했습니다.)가 발생할 수 있습니다.

### <a name="types-of-replication"></a>복제의 유형

다음과 같은 여러 [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)이 있습니다.

| 복제 | Azure SQL Database | Azure SQL Managed Instance |
| :----| :------------- | :--------------- |
| [**표준 트랜잭션**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 예(구독자로) | 예 |
| [**스냅샷**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 예(구독자로) | 예|
| [**병합 복제**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 아니요 | 아니요|
| [**피어-투-피어**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 아니요 | 아니요|
| [**양방향**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 예 | 예|
| [**업데이트할 수 있는 구독**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 아니요 | 아니요|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>지원 가능성 매트릭스

  Azure SQL Managed Instance에 대 한 트랜잭션 복제 지원 가능성 행렬은 SQL Server에 대 한 것과 동일 합니다.
  
| **게시자**   | **총판** | **구독자** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>사용 시기

트랜잭션 복제는 다음과 같은 시나리오에서 유용합니다.

- 하나 이상의 데이터베이스에 있는 하나 이상의 테이블에 적용 된 변경 내용을 게시 하 고 SQL Server 인스턴스의 하나 이상의 데이터베이스 또는 변경 내용에 대해 구독 한 Azure SQL Database에 배포 합니다.
- 여러 개의 분산된 데이터베이스를 동기화된 상태로 유지합니다.
- 변경 내용을 지속적으로 게시 하 여 하나의 SQL Server 인스턴스 또는 Azure SQL Managed Instance에서 다른 데이터베이스로 데이터베이스를 마이그레이션합니다.

### <a name="compare-data-sync-with-transactional-replication"></a>트랜잭션 복제와 데이터 동기화 비교

| | 데이터 동기화 | 트랜잭션 복제 |
|---|---|---|
| 장점 | - 활성-활성 지원<br/>- 온-프레미스 및 Azure SQL Database 간 양방향 | - 낮은 대기 시간<br/>- 트랜잭션 일관성<br/>- 마이그레이션 후 기존 토폴로지 다시 사용 |
| 단점 | - 5분 이상의 대기 시간<br/>- 트랜잭션 일관성 부족<br/>- 성능에 더 많은 영향을 미침 | -Azure SQL Database에서 게시할 수 없음 <br/>- 높은 유지 관리 비용 |
| | | |

## <a name="common-configurations"></a>일반 구성

일반적으로 게시자와 배포자는 모두 클라우드 또는 온-프레미스에 있어야 합니다. 현재 지원되는 구성은 다음과 같습니다.

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>SQL Managed Instance의 로컬 배포자가 있는 게시자

![게시자 및 배포자로서의 단일 인스턴스](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

게시자 및 배포자는 단일 SQL Managed Instance 내에서 구성 되 고 변경 내용을 다른 SQL Managed Instance, SQL Database 또는 SQL Server 인스턴스에 배포 합니다.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>SQL Managed Instance에 원격 배포자가 있는 게시자

이 구성에서 하나의 관리 되는 인스턴스는 다양 한 원본 SQL 관리 되는 인스턴스를 제공 하 고 Azure SQL Database, Azure SQL Managed Instance 또는 SQL Server에서 하나 이상의 대상에 변경 내용을 배포할 수 있는 다른 SQL Managed Instance에 배치 된 배포자에 변경 내용을 게시 합니다.

![게시자 및 배포자에 대한 별도 인스턴스](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

게시자 및 배포자는 두 개의 관리되는 인스턴스에서 구성됩니다. 이 구성을 사용 하는 몇 가지 제약 조건이 있습니다.

- 두 관리 되는 인스턴스는 동일한 vNet에 있습니다.
- 두 관리되는 인스턴스는 동일한 위치에 있습니다.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>원격 구독자를 사용 하는 온-프레미스 게시자/배포자

![구독자로 Azure SQL Database](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

이 구성에서 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스는 구독자입니다. 이 구성은 온-프레미스에서 Azure로의 마이그레이션을 지원합니다. 구독자가 Azure SQL Database의 데이터베이스인 경우 밀어넣기 모드 여야 합니다.  

## <a name="requirements"></a>요구 사항

- 복제 참가자 간의 연결에 SQL 인증을 사용 합니다.
- 복제에 사용 되는 작업 디렉터리에 대 한 Azure Storage 계정 공유를 사용 합니다.
- Azure 파일 공유에 액세스 하려면 서브넷 보안 규칙에서 TCP 아웃 바운드 포트 445를 엽니다.
- SQL Managed Instance 게시자/배포자이 고 구독자가이 아닌 경우 TCP 아웃 바운드 포트 1433를 엽니다. `allow_linkedserver_outbound`포트 1433 **대상 서비스 태그** 의에 대 한 SQL Managed Instance nsg 아웃 바운드 보안 규칙을에서로 변경 해야 할 수도 있습니다 `virtualnetwork` `internet` .
- 클라우드 또는 온-프레미스 둘 다에 게시자와 배포자를 둘 다 저장 합니다.
- 가상 네트워크가 서로 다른 경우 복제 참가자의 가상 네트워크 간에 VPN 피어 링을 구성 합니다.

> [!NOTE]
> 배포자가 Azure SQL Managed Instance 데이터베이스이 고 구독자가 온-프레미스 인 경우 아웃 바운드 NSG (네트워크 보안 그룹) 포트 445이 차단 되 면 Azure Storage 파일에 연결할 때 오류 53이 발생할 수 있습니다. 이 문제를 해결 하려면 [vNet NSG를 업데이트](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 합니다.

## <a name="with-failover-groups"></a>장애 조치 (failover) 그룹 사용

[활성 지역 복제](../database/active-geo-replication-overview.md) 는 트랜잭션 복제를 사용 하는 SQL Managed Instance 지원 되지 않습니다. 활성 지역 복제 대신 [자동 장애 조치 (failover) 그룹](../database/auto-failover-group-overview.md)을 사용 하지만, 장애 조치 (failover) 후에 주 관리 되는 인스턴스에서 게시를 [수동으로 삭제](transact-sql-tsql-differences-sql-server.md#replication) 하 고 보조 SQL Managed Instance에서 다시 만들어야 합니다.

[장애 조치 (failover) 그룹](../database/auto-failover-group-overview.md)의 **게시자** 또는 **배포자** SQL Managed Instance에서 지역에서 복제를 사용 하는 경우 SQL Managed Instance 관리자는 이전 주 서버의 모든 게시를 정리 하 고 장애 조치 (failover)가 발생 한 후에 새 주 복제본에서 해당 게시를 다시 구성 해야 합니다. 이 시나리오에는 다음과 같은 작업이 필요 합니다.

1. 데이터베이스에서 실행 중인 모든 복제 작업을 중지 합니다 (있는 경우).
1. 게시자 데이터베이스에서 다음 스크립트를 실행 하 여 게시자에서 구독 메타 데이터를 삭제 합니다.

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. 구독자에서 구독 메타 데이터를 삭제 합니다. 구독자 SQL Managed Instance의 구독 데이터베이스에서 다음 스크립트를 실행 합니다.

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. 게시 된 데이터베이스에서 다음 스크립트를 실행 하 여 게시자에서 모든 복제 개체를 강제로 삭제 합니다.

   ```sql
   EXEC sp_removedbreplication
   ```

1. 원래 주 SQL Managed Instance에서 이전 배포자를 강제로 삭제 합니다 (배포자를 사용 하는 이전 주 복제본으로 장애 복구 (failback) 하는 경우). 이전 배포자 SQL Managed Instance의 master 데이터베이스에서 다음 스크립트를 실행 합니다.

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

장애 조치 (failover) 그룹의 **구독자** 인스턴스에서 지역에서 복제를 사용 하는 경우 구독자 관리 되는 인스턴스의 장애 조치 (failover) 그룹 수신기 끝점에 연결 하도록 게시를 구성 해야 합니다. 장애 조치 (failover) 시 관리 되는 인스턴스 관리자의 후속 작업은 발생 한 장애 조치 (failover) 유형에 따라 달라 집니다.

- 데이터 손실이 없는 장애 조치 (failover)의 경우 장애 조치 (failover) 후 복제는 계속 작동 합니다.
- 데이터 손실이 있는 장애 조치 (failover)의 경우에도 복제가 작동 합니다. 그러면 손실 된 변경 내용이 다시 복제 됩니다.
- 데이터 손실이 있는 장애 조치 (failover)의 경우 데이터 손실이 배포 데이터베이스 보존 기간을 벗어난 경우 SQL Managed Instance 관리자는 구독 데이터베이스를 다시 초기화 해야 합니다.

## <a name="next-steps"></a>다음 단계

트랜잭션 복제를 구성하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.

- [SQL Managed Instance 게시자와 구독자 간의 복제 구성](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [SQL Managed Instance 게시자, SQL Managed Instance 배포자 및 SQL Server 구독자 간의 복제 구성](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [게시를 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- 서버 이름을 구독자로 사용 하 여 [밀어넣기 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) (예 `N'azuresqldbdns.database.windows.net` : **Adventureworks**와 같이 Azure SQL Database 이름의 데이터베이스를 대상 데이터베이스로 사용 합니다. )

## <a name="see-also"></a>참조  

- [SQL Managed Instance 및 장애 조치 (failover) 그룹으로 복제](transact-sql-tsql-differences-sql-server.md#replication)
- [SQL Database로 복제](../database/replication-to-sql-database.md)
- [관리 되는 인스턴스로 복제](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
